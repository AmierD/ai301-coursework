# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/2

**Verdict output**

# Grading codepath/pathreview-ai301-fa26-s3#2

Inside the scoped source, live mode, measured against today (2026-09-21).

| Check | Weight | Grade | Evidence |
|---|---|---|---|
| maintainers-active--commits-and-prs | required | pass | Aburke225 (human, COLLABORATOR, no `[bot]` suffix) authored all 5 most recent main commits; newest 2026-09-16, 5 days ago |
| maintainers-active--low-response-latency | preferred | fail | Of the 5 most recently updated issues (67, 60, 57, 62, 69), 0 have a reply from Owner/Member/Collaborator; every comment is `author_association: NONE` |
| maintainers-active--this-thread | preferred | fail | Issue has 0 comments; timeline holds only 4 maintainer label events |
| repo-in-use--not-archived | required | pass | `archived: false` |
| repo-in-use--recent-release | required | pass | 0 releases and 0 tags: releases are not in use, which the check passes |
| repo-in-use--high-adoption | preferred | fail | 2 stars, below the 200 threshold |
| issue-in-scope--contribution | required | pass | New functionality, but opened by Aburke225 with `author_association: COLLABORATOR`: maintainer-opened, which is exactly what the rubric requires for a feature request |
| issue-in-scope--bounded | required | pass | One feature across a new `ingestion/parsers/web_parser.py`, `ingestion/pipeline.py`, `api/schemas/profile.py`; no umbrella/tracking/epic label, no split request |
| issue-in-scope--low-impact | required | pass | No maintainer statement that it touches core internals or essential systems |
| issue-in-scope--clear | required | pass | 0 comments, so no unresolved design debate; body fixes the behavior ("fetch the page content, extract relevant text… include it in the vector store") |
| issue-in-scope--unabandoned | required | pass | Repo has 0 pull requests total (state=all); none referenced in the thread |
| issue-unclaimed--assignees | required | pass | `assignees: []` |
| issue-unclaimed--prs | required | pass | No linked PRs in the Development box; repo has 0 PRs at all |
| issue-unclaimed--comments | required | pass | 0 comments, so no claim of any kind |
| ai-assistance--allowed | required | pass | `docs/CONTRIBUTING.md` and `.github/PULL_REQUEST_TEMPLATE.md` state no AI restriction; no `AI_POLICY.md` exists: silence passes |
| ai-assistance--conditions-specified | preferred | fail | No AI disclosure, personal-understanding, or human-review conditions anywhere in the contribution docs |
| ai-assistance--encouraged | preferred | fail | No `AGENTS.md` or equivalent agent-instruction file (the `agent/` directory is application code, not agent instructions) |

## Verdict: accept

All 12 required checks pass. It clears 0 of 5 preferred checks, so nothing here argues for it over another accepted candidate: no maintainer has spoken in the thread, response latency across the repo is unproven, the repo has 2 stars, and the contribution docs are silent on AI.

```

{
"item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/2",
"checks": [
  {"name": "maintainers-active--commits-and-prs", "grade": "pass", "evidence": "Aburke225 (human, COLLABORATOR) authored the last 8 main commits; newest 2026-09-16, 5 days before today"},
  {"name": "maintainers-active--low-response-latency", "grade": "fail", "evidence": "0 of the 5 most recently updated issues have a reply with Owner/Member/Collaborator association"},
  {"name": "maintainers-active--this-thread", "grade": "fail", "evidence": "Issue has 0 comments"},
  {"name": "repo-in-use--not-archived", "grade": "pass", "evidence": "Repo API reports archived: false"},
  {"name": "repo-in-use--recent-release", "grade": "pass", "evidence": "No releases and no tags exist; releases are not in use"},
  {"name": "repo-in-use--high-adoption", "grade": "fail", "evidence": "Repo has 2 stars, below the 200 threshold"},
  {"name": "issue-in-scope--contribution", "grade": "pass", "evidence": "New functionality ('Allow users to submit a URL to their personal portfolio site') opened by Aburke225, author_association COLLABORATOR, and labeled enhancement by the same maintainer"},
  {"name": "issue-in-scope--bounded", "grade": "pass", "evidence": "One feature: new ingestion/parsers/web_parser.py plus pipeline and schema hookup, est. 5-8 hours; no umbrella/tracking marker or split request"},
  {"name": "issue-in-scope--low-impact", "grade": "pass", "evidence": "No maintainer statement that it touches core internals, though it adds a stage to ingestion/pipeline.py"},
  {"name": "issue-in-scope--clear", "grade": "pass", "evidence": "0 comments; body specifies fetch, extract bio/project text, store alongside GitHub and resume data"},
  {"name": "issue-in-scope--unabandoned", "grade": "pass", "evidence": "Repo has 0 pull requests total (state=all); none mentioned in the thread"},
  {"name": "issue-unclaimed--assignees", "grade": "pass", "evidence": "assignees: []"},
  {"name": "issue-unclaimed--prs", "grade": "pass", "evidence": "No linked PRs; repo has 0 PRs total"},
  {"name": "issue-unclaimed--comments", "grade": "pass", "evidence": "0 comments; timeline shows only maintainer label events"},
  {"name": "ai-assistance--allowed", "grade": "pasRIBUTING.md and the PR template contain no AIrestriction; no AI_POLICY.md exists"},
  {"name": "ai-assistance--conditions-specified", ": "No AI disclosure, review, or testing conditionsstated in any contribution doc"},
  {"name": "ai-assistance--encouraged", "grade": "NTS.md or equivalent agent-instruction file in therepo"}
],
"verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**
run 1: 
categories: claimed 4/4  clear-accept 5/8  dead-repo 3/3  policy 1/1  scope 4/4
agreement: 17/20 scored items  (bar: 18/20: below the bar)
run 2: 
categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4
agreement: 20/20 scored items  (bar: 18/20: PASS)

**Issue analysis**
issue-01, my rubrics decision was accept, the gold label is accept.
The reasoning that produced my rubrics result is that it passes all the families of checks necessary for a healthy repo, issue, and maintainer activity.

**Check rationale**
| issue-in-scope--unabandoned | linked PRs, PRs mentioned in comments | fewer than 2 unmerged PRs in history or comments | required |

The reasoning behind this check is that if there are more than 1 closed, unmerged PRs, this issue is likely very difficult.

**Trade-offs**

I re-ran with --only issue-01,issue-09,issue-19 and issue-09 now passes.

---

## Selection rationale

**Selection rationale**

1. I am very interested in working with backend web dev and working on a piece of the rag pipeline (vector store).
2. The verditct identified the health of the repo and issue, I weighed the interest that I have in it and whether or not I am able to complete it.
3. I anticipate that this will somewhat difficult but definitely possible.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
