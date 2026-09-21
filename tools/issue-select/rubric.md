# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainers-active--commits-and-prs | recent default-branch commits, recent prs | commits authored by human maintainers (not bots, noted by a username ending in [bot]) within the last 90 days or prs authored by humans merged by maintainers or bots within the last 90 days | required |
| maintainers-active--low-response-latency | 5 most recently updated issues | first reply from a maintainer (owner, member, or collaborator badge) within 7 days in at least 2 of 5 | preferred |
| maintainers-active--this-thread | comments | comments on this issue from a maintainer | preferred |
| repo-in-use--not-archived | "archived:" on the repo line | repo is not archived | required |
| repo-in-use--recent-release | releases box | release within a year, pass if releases are not in use | required |
| repo-in-use--high-adoption | repo star count | is the star count of the repo higher than 200 | preferred |
| issue-in-scope--contribution | issue body | issue asks for a change (code, docs, tests) or a bug fix, not a support or usage question; a request for new functionality must be opened by a maintainer, labeled by a maintainer, or approved in a maintainer comment; docs, test, and bug-fix issues need no approval | required |
| issue-in-scope--bounded | issue body, labels, comments | fail only if the issue is labeled or described as an umbrella, tracking, epic, or meta issue, or the body or a maintainer says the items should become separate issues or PRs; a list of steps, causes, or suggestions for one change does not fail | required |
| issue-in-scope--low-impact | issue body, comments | maintainer does not say issue touches core internals or essential systems | required |
| issue-in-scope--clear | issue body, comments | no unresolved design debate | required |
| issue-in-scope--unabandoned | linked PRs, PRs mentioned in comments | fewer than 2 unmerged PRs in history or comments | required |
| issue-unclaimed--assignees | assignees | no assignees | required |
| issue-unclaimed--prs | linked prs | no open pr linked | required |
| issue-unclaimed--comments | comments | no comments stating that the issue is claimed within 30 days or referencing a linked open pr | required |
| ai-assistance--allowed | general contribution policy documentation (like `CONTRIBUTING.MD` in the repo root or `.github/`, or any external contributor docs linked), specific AI policy documentation (like `AI_POLICY.md` or `AI_USAGE_POLICY.md`) | no explicit ban on assistive AI use | required |
| ai-assistance--conditions-specified | general contribution policy documentation, specific AI policy documentation | conditions for submitting AI-assisted work is present | preferred |
| ai-assistance--encouraged | `AGENTS.md` file (or similar) | existence of a file detailing instructions for AI coding agents | preferred |

## Verdict rule

Accept if every required check passes. Otherwise reject.

Preferred checks never change the verdict. An issue can fail every preferred check and still be accepted, given that all required checks pass. Unclear counts as fail.
Accepted issues are ranked by number of preferred checks passed.

Unclear *required* checks depend on what the check is hunting for:

- Proof checks (hunting for evidence something good is true):
  unclear counts as fail.
  - maintainers-active--commits-and-prs
  - repo-in-use--not-archived
  - repo-in-use--recent-release
  - issue-unclaimed--assignees
  - issue-unclaimed--prs
- Red-flag checks (hunting for evidence something bad is true):
  unclear counts as pass.
  - issue-in-scope--contribution
  - issue-in-scope--bounded
  - issue-in-scope--low-impact
  - issue-in-scope--clear
  - issue-in-scope--unabandoned
  - issue-unclaimed--comments
  - ai-assistance--allowed

Example: if no contribution policy exists, ai-assistance--allowed
passes (no ban found). If the only recent commits are from [bot]
accounts and their merged PRs can't be traced to a human,
maintainers-active--commits-and-prs fails (no proof of active humans).

Dates: recency thresholds are measured against the capture date in
eval mode, and against today in live mode.
