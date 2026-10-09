# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

AmierD

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/2#issuecomment-6069995539

Plan

Steps

add a new WebParser in ingestion/parsers/web_parser.py that turns html into text using html.parser
add a new ingest portfolio method in the IngestionPipeline class that fetches the page, parses, chunks, embeds, and stores it in the vector store
add validation for the portfolio_url in api/schemas/portfolio.py to ensure only http and https URLs are accepted
update review_service.py's _run_ingestion_pipeline()
Testing:

Unit tests: tests/unit/test_web_parser.py, tests/unit/test_ingest_portfolio.py, and schema tests. I'll also rerun tests/unit/test_review_service.py.
Repro rerun: steps 1–7 in the repro report above. The ingested_sources query should return one portfolio row with source_url = https://portfolio.mieracle.com and chunk_count > 0 instead of (0 rows), and Chroma should hold portfolio chunks for that profile.
Negative case: with portfolio_url=http://localhost:8000, the review still completes, the logs show portfolio_ingestion_failed, and no portfolio row or chunks are saved.
Out of scope:

GitHub and resume ingestion
pages rendered by JavaScript
the hardcoded RAG output, which means the review text won't change but the portfolio row and its chunks will be stored

---

## Your branch

**Branch**

fix/2-portfolio-website-ingestion

**Evidence**

Repro steps:
1. Fork and clone, cp .env.example .env.
2. docker compose up -d (Postgres on port 5433 per .env).
3. make setup, then make run.
4. Open Swagger at http://localhost:8000/docs, click Authorize, and log in as user1@example.com / password1 (seeded account).
5. POST /profiles with only portfolio_url=portfolio.mieracle.com. github_username and resume_file left empty, "Send empty value" unchecked for both. Returned id <profile-id> with "github_username": null.
6. POST /reviews with {"profile_id": "<profile-id>"}. Returned review_id <review-id>, "status": "pending".
7. GET /reviews/<review-id>, then from the repo folder:
docker compose exec db psql -U pathreview -d pathreview_dev -c "select source_type, source_url, chunk_count from ingested_sources;"


Before:
```
 source_type | source_url | chunk_count
-------------+------------+-------------
(0 rows)
```

After:
```
 source_type |           source_url           | chunk_count
-------------+--------------------------------+-------------
 portfolio   | https://portfolio.mieracle.com |           3
(1 row)
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

run 1:
```
categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4
agreement: 20/20 scored items  (bar: 18/20: PASS)
```

**Package analysis**

**pkg-04** (junegunn/fzf#4260, category `thread-convention`). My rubric decided **reject** and the
gold label is **reject**, so they agree.

The candidate plan says: "Scope: documentation only. In scope: the man page and the README
examples for `execute` bindings; a new FAQ entry. Not in scope: any change to fzf's input
handling code." But the thread had already moved past a workaround: the owner wrote "This seems
to be the culprit", pointing at `src/tui/light_windows.go` (lines 70-84), and "posted a patched
test binary from commit 8916cbc", which the reporter said "pretty much solves it". The repro
evidence also sets the target: "Expected: keys reach `less` without a redirection workaround."

My rubric reads that as two required fails. `thread-repo-conventions` takes the comment thread
as evidence, and the plan comment ignores the direction the maintainer already set. `issue-match`
passes only "if proposed work directly addresses the reported/reproduced behavior and expected
result." The plan is well formed, with a bounded scope, a concrete approach, and an observable
test, so `scope`, `approach`, and `test` by themselves would have accepted it. The rejection
comes from reading the plan against the thread and the repro's expected result.

**Check rationale**

| scope | proposed changes and in/out-of-scope statements in the plan and plan comment | passes if the plan (1) names where the change goes — a file, function, module, or code path specific enough to find, exact file paths not required — (2) states at least one thing it will not touch, and (3) limits the change to what the issue needs; fails if it bundles refactors, rewrites, new options/features, dependency bumps, or fixes for other symptoms | required |

Before it was not as specific and would pass things that included things for other symptoms.

**Trade-offs**

The clause "exact file paths not required" trades strictness for fewer false rejects. Several
clear-accept packages name a code path rather than a file: pkg-13 scopes to "the erase-scrollback
path" and pkg-14 to "the reattach handshake on Unix clients". A check that demanded a file path
would hold good plans like these. The cost is that a vaguer plan can now pass `scope` if it names
a plausible-sounding path. I accept that miss because two other parts of the rubric still catch
plans that are too vague: `approach` fails when "the location or the change is left undecided",
and the scope check still fails bundled refactors and features. The final run backs this up:
"clear-accept 7/7" with "scope-creep 4/4" and "unbuildable 3/3", so loosening the location rule
did not let a scope-creep or unbuildable package through.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
