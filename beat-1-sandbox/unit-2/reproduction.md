# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Your identity upstream

**GitHub username**

AmierD

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/2#issuecomment-5865664456

Hi, I'd like to claim this portfolio website ingestion issue! This will be my first contribution to the repo. I will be sending a reproduction report soon.

`portfolio_url` is already saved on the profile, but at review time `core/services/review_service.py:231-239` only builds the placeholder string `"Portfolio data from <url>"`, and nothing fetches the page.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/2#issuecomment-5865699574

### Environment

- macOS 26.6.2
- Python 3.14.7, Node v24.6.0, npm 11.19.1
- Docker 29.8.0, Docker Compose v5.5.1
- Repo at `2f4e82f`, which is current `upstream/main` as of 2026-09-28

### Steps

1. Fork and clone, `cp .env.example .env`.
2. `docker compose up -d` (Postgres on port 5433 per `.env`).
3. `make setup`, then `make run`.
4. Open Swagger at `http://localhost:8000/docs`, click Authorize, and log in as `user1@example.com` / `password1` (seeded account).
5. `POST /profiles` with only `portfolio_url=portfolio.mieracle.com`. `github_username` and `resume_file` left empty, "Send empty value" unchecked for both. Returned `id` `79a377c3-ca25-4f83-ae18-27356493e389` with `"github_username": null`.
6. `POST /reviews` with `{"profile_id": "79a377c3-ca25-4f83-ae18-27356493e389"}`. Returned `review_id` `67e15347-c9eb-4f21-8c9f-084270ca1421`, `"status": "pending"`.
7. `GET /reviews/67e15347-c9eb-4f21-8c9f-084270ca1421`, then from the repo folder:
   `docker compose exec db psql -U pathreview -d pathreview_dev -c "select source_type, source_url, chunk_count from ingested_sources;"`

### Expected

The portfolio page is fetched, its bio and project text is extracted, and that text is stored in the vector store alongside GitHub and resume data, so it can inform the review.

### Actual

The review completes, but none of its text comes from the site:

```json
"status": "complete",
"sections": [
  { "section_name": "Technical Skills",
    "content": "Detailed feedback on technical skills based on portfolio analysis", ... },
  { "section_name": "Project Experience",
    "content": "Detailed feedback on project experience and impact", ... },
  { "section_name": "Career Growth",
    "content": "Feedback on career progression and development", ... }
],
"overall_score": 0.81,
"error_message": null
```

These sections match the hardcoded return value of `_run_rag_retrieval_generation` in `core/services/review_service.py`. No portfolio source is recorded:

```
 source_type | source_url | chunk_count
-------------+------------+-------------
(0 rows)
```

At the point where the feature would run, `review_service.py:231-239` builds the placeholder string `"Portfolio data from <url>"` instead of fetching the page. `ingestion/pipeline.py` isn't called from the app: only files in `tests/unit/` import `ingestion.*`.

In the pipeline itself, `IngestionPipeline` has `ingest_resume()` (`pipeline.py:54`), `ingest_readme()` (`:130`), and `ingest_repo_metadata()` (`:207`), but nothing for a portfolio URL, and `ingestion/parsers/` has no web parser.

### Conclusion

Reproduced at `2f4e82f`. A review for a profile with a portfolio URL contains no content from that page, and no portfolio source is stored. The portfolio branch builds a placeholder (`review_service.py:231-239`) instead of fetching the page.

## Eval iterations

**Run history**

1. `--limit 3`: agreement 1/3
2. `--only pkg-01,pkg-02,pkg-03,pkg-20`: agreement 3/4
3. `--only pkg-03,pkg-06,pkg-10,pkg-16,pkg-20`: agreement 4/5
4. `--only pkg-02,pkg-03,pkg-06,pkg-10,pkg-16,pkg-20`: agreement 6/6
5. Full run (`eval-run.txt`): agreement: 20/20 scored items (bar: 18/20: PASS)

**Package analysis**

`pkg-03` (ripgrep#2779). Gold: accept. My rubric first decided reject, failing
`environment-info`: "Report calls out the version difference (13.0.0 vs 15.2.0) but never
mentions the OS difference (issue: Kubuntu 23.10; report: Arch Linux)". The rubric required
every OS difference to be called out, and read it literally. But a line-numbering bug in the
printer doesn't depend on the OS because the gold note says "version delta acknowledged" is enough.
It also failed `ai-disclosure` on an earlier run because my rubric treated ripgrep's
"comments must be written by humans" rule as a disclosure policy.

**Check rationale**

> Wherever the tested environment differs from what the issue reports or targets in a way
> that could affect the behavior, the report must call out the difference. A project-version
> difference always counts; an OS difference counts only if the issue is platform-specific;
> config and flag differences count only if the issue depends on them.

It originally required calling out any difference in "(version, OS, config, flags)". That
failed pkg-03 over Kubuntu vs Arch on a bug unrelated to the OS. I scoped it to differences
that could affect the behavior, but kept version differences mandatory so silent version
drift still fails.

**Trade-offs**

Loosening the difference rule risked passing a silent deviation, so I re-ran
with `--only` canaries: `pkg-16` still rejected because version differences
always count, and `pkg-06` (Windows-specific issue, no environment record)
still rejected. The case I accept it may miss is an OS or config difference
that matters but the issue doesn't flag as platform-specific.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
