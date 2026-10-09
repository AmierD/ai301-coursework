# Plan
## Summary
Make a profile's `portfolio_url` feed the review. At review time, fetch the portfolio page safely, extract its main text (bio, project descriptions), chunk and embed it into the vector store through `IngestionPipeline`, and record a real `ingested_sources` row for it. Issue: codepath/pathreview-ai301-fa26-s3#2.

## Diagnosis
Reproduced at `2f4e82f` (see repro report on #2).

- `core/services/review_service.py:231-239`: the portfolio branch of `_run_ingestion_pipeline()` builds the placeholder string `"Portfolio data from <url>"`. Nothing fetches the page.
- The same branch builds `IngestedSource(..., raw_data=...)`, but `core/models/ingested_source.py` has no `raw_data` column (it has `source_url`, `content_hash`, `chunk_count`). The constructor likely raises, the broad `except` logs `portfolio_ingestion_failed`, and no row is saved. That would explain the `(0 rows)` in the repro. Confirm this from the logs before fixing.
- `ingestion/pipeline.py` is never called from the app. Only `tests/unit/` imports `ingestion.*`. `IngestionPipeline` has `ingest_resume`, `ingest_readme`, and `ingest_repo_metadata`, but nothing for a web page, and `ingestion/parsers/` has no HTML parser.
- `api/schemas/profile.py`: `portfolio_url` is a plain `str` (max 500) with no validation. The repro saved `portfolio.mieracle.com` with no scheme, which `httpx` can't fetch. Any value is accepted, including internal addresses such as `http://localhost` or `http://169.254.169.254`, so fetching it server-side would allow SSRF (server-side request forgery).

## Scope
I will be making edits to the following:
- `ingestion/parsers/web_parser.py` (new)
- `ingestion/pipeline.py`
- `core/services/review_service.py`
- `api/schemas/profile.py`
- `tests/unit/`

### Out of scope
- Saving and updating `portfolio_url` already works (`core/services/profile_service.py`, `api/routes/profiles.py`).
- GitHub and resume ingestion are also placeholders and also never reach the vector store. The issue says "alongside GitHub and resume data", but I will only connect the portfolio source. I'll write up the other two as a follow-up rather than fix them here.
- `_run_rag_retrieval_generation()` returns hardcoded sections, so the review *text* will not change after this fix. Connecting retrieval to generation is a separate issue.
- Pages that render their content with JavaScript (only an empty `<div id="root">` in the HTML). No headless browser. These pages are handled as "no extractable content" (see step 1).
- Crawling beyond the single page at `portfolio_url`.

## Changes

1. **Create new `WebParser` in `ingestion/parsers/web_parser.py`**
   - `class WebParser(BaseParser)` with `parse(self, content: str | bytes) -> ParseResult`, matching `ReadmeParser`'s input handling: decode bytes as UTF-8 with `errors="replace"` and raise `ValueError` for any other type.
   - Extract text with the standard library's `html.parser.HTMLParser`, so no new dependency is needed.
     - Skip text inside `<script>`, `<style>`, `<noscript>`, `<template>`, `<svg>`, `<nav>`, and `<footer>`.
     - Write `<h1>`–`<h3>` as markdown `#`/`##`/`###` lines so the chunker splits by section (About, Projects, …).
     - Treat block tags (`p`, `div`, `li`, `section`, `br`, …) as line breaks, then collapse extra whitespace.
   - Metadata: `source_type: "portfolio"`, `title` (from `<title>`), `heading_count`, `word_count`.
   - If no meaningful text is left (for example, a JavaScript-only page), raise `ValueError("No extractable text")` so the caller logs it and skips the source.

2. **Fetching and ingestion in `ingestion/pipeline.py`**
   1. Add `self.web_parser = WebParser()` in `__init__`.
   2. Add a `_fetch_page(url) -> str` helper using `httpx`:
      - Allow only `http`/`https`. Resolve the host and refuse private, loopback, link-local, and reserved IPs. Turn off automatic redirects and check each redirect target the same way (at most 3 hops).
      - 10s timeout, stop reading after ~2 MB, and require `Content-Type: text/html`.
      - Raise `ValueError` for anything that is refused.
   3. Add `ingest_portfolio(self, profile_id: str, url: str) -> IngestResult`, following `ingest_readme`:
      fetch with `_fetch_page`, then
      `source_id = f"portfolio_{profile_id}_{hash(html)}"`, then `_check_skip`, then
      `web_parser.parse`, then merge metadata (`source_id`, `profile_id`, `url`, `source_type: "portfolio"`), then
      `strategy_selector.chunk`, then `batch_processor.process`, then `_record_ingested_source`, and return an `IngestResult`.
      Fetching inside the pipeline matches the issue ("the pipeline should fetch the page content"). Because `_fetch_page` is a separate method, tests can replace it with a fake.

3. **URL validation in `api/schemas/profile.py`**
   - Add a validator on `portfolio_url` in `ProfileCreate` and `ProfileUpdate`: trim it, add `https://` when there's no scheme (so `portfolio.mieracle.com` from the repro works), reject anything that isn't `http`/`https`, and require a hostname.
   - This only catches bad input early. The IP check in `_fetch_page` is the real SSRF protection, because a public domain name can resolve to an internal IP.

4. **Connect the portfolio branch in `core/services/review_service.py`**
   - Build an `IngestionPipeline` with a Chroma collection (reuse `rag/retriever/vector_store.py`'s `VectorStore.get_collection`) and `get_embedding_provider(...)` from `ingestion/embeddings/provider.py` (`mock` in dev and tests).
   - `_run_ingestion_pipeline` is `async`, but the pipeline is synchronous and blocking, so call it with `await asyncio.to_thread(pipeline.ingest_portfolio, str(profile.id), profile.portfolio_url)`.
   - Replace the placeholder dict. Append `{"source_type": "portfolio", "url": ..., "source_id": ..., "chunk_count": ...}` to `sources`, which keeps the list-of-dicts shape the later steps expect.
   - Fix the row: `IngestedSource(profile_id=..., source_type="portfolio", source_url=url, content_hash=..., chunk_count=result.chunk_count)`, with no `raw_data`.
   - Keep the existing `except`. A fetch or parse failure logs `portfolio_ingestion_failed` and the review continues.
   - Open question for the maintainer: the model's comment lists `"web"` as a source type, while the service uses `"portfolio"`. I'll use `"portfolio"` unless told otherwise.

## Testing Plan

**Unit tests (`pytest -m unit`)**
- `tests/unit/test_web_parser.py`, modeled on `test_readme_parser.py`, with an inline HTML fixture:
  - Text in script, style, nav, and footer is removed. Bio and project text is kept.
  - `<title>` goes into metadata. `<h1>`/`<h2>` become `#` lines, and `heading_count` is correct.
  - Whitespace is collapsed. Bytes input works. Non-str/bytes input raises `ValueError`.
  - A JavaScript-only page (`<div id="root"></div>` plus scripts) raises `ValueError`.
- `tests/unit/test_ingest_portfolio.py`:
  - `ingest_portfolio` with `_fetch_page` replaced by a fake, `MockEmbeddingProvider`, and a fake vector DB returns `chunk_count > 0` and `skipped=False`, and the stored chunks carry `source_type: "portfolio"` and `url` metadata.
  - `_fetch_page` refuses `ftp://…`, `http://localhost`, `http://127.0.0.1`, `http://169.254.169.254`, `http://10.0.0.5`, a redirect to a private IP, and a non-HTML content type, without making real network calls (use `httpx.MockTransport`).
- Schema tests: `portfolio.mieracle.com` would become `https://portfolio.mieracle.com`. `javascript:alert(1)` and `file:///etc/passwd` would be rejected.
- Rerun `tests/unit/test_review_service.py` and fix anything the new code breaks.

**Manual check (the repro steps, after the fix)**
1. Follow the repro steps 1–7 on #2 with `portfolio_url=portfolio.mieracle.com`.
2. Expected: `select source_type, source_url, chunk_count from ingested_sources;` returns one `portfolio` row with `source_url = https://portfolio.mieracle.com` and `chunk_count > 0`.
3. Expected: the Chroma collection has chunks with `source_type = "portfolio"` for that profile.
4. Negative case: set `portfolio_url=http://localhost:8000`. The review still completes, the logs show `portfolio_ingestion_failed`, and no portfolio row or chunks are saved.

## Deviations

- **Chunking:** portfolio text goes through the semantic chunker (the `StrategySelector` default), not the structural chunker. The structural chunker has a known bug (#56) that drops any text before the first heading, so pages with few or no headings would lose content or produce 0 chunks. The parser still writes `#`/`##`/`###` lines, so each section stays labelled in the text.
- **No `content_hash` in the row:** the `ingested_sources` row sets `source_type`, `source_url`, and `chunk_count`, and leaves `content_hash` empty. Nothing in the codebase reads it yet, so I didn't add a hash to `IngestResult`.
- **`IngestionPipeline` takes an optional `http_transport`:** this lets the fetch tests use `httpx.MockTransport` with no real network calls.
- **Out-of-scope file changed, `api/routes/profiles.py`:** `POST /profiles` builds `ProfileCreate` inside a broad `except Exception`, so the new URL validation came back as a 500. I added an `except ValidationError` that returns a 422 with the error details.
- **Out-of-scope file changed, `core/config.py`:** added `embedding_provider` (default `"mock"`). There was no setting for it, and `llm_provider` can hold values `get_embedding_provider` rejects.
- **Chroma:** chunks go to the local `VectorStore` (`.chromadb/`) in collection `profile_<id>`, the name `rag/retriever/hybrid.py` already reads. The docker Chroma server (0.4.22) can't be used, because the installed `chromadb` client (1.5.9) speaks a newer API.
- **Diagnosis confirmed:** `IngestedSource(raw_data=...)` raises `TypeError: 'raw_data' is an invalid keyword argument`, which the broad `except` swallowed. The GitHub and resume branches have the same bug and are left for the follow-up.
- **Known limits:** DNS rebinding isn't blocked (the hostname is checked, then looked up again when `httpx` connects). `_check_skip` is still a placeholder, so every review logs a harmless "Could not check if source already ingested" warning. A refused URL still creates an empty `profile_<id>` Chroma collection (0 chunks), because the collection is opened before the fetch.
- **Results:** unit suite 424 passed / 53 xfailed (baseline 375 / 53, +49 new tests, no new failures). Manual repro: `ingested_sources` returns one `portfolio` row for `https://portfolio.mieracle.com` with `chunk_count = 3`, and Chroma holds 3 `portfolio` chunks for the profile. Negative case (`http://localhost:8000`): the review completes, the log shows `portfolio_ingestion_failed ... non-public address`, and no row or chunks are saved.

