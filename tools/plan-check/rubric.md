# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | reproduction report in thread by plan author | fail if diagnosis ignores or contradicts reproduced evidence | required |
| scope | proposed changes and in/out-of-scope statements in the plan and plan comment | passes if the plan (1) names where the change goes — a file, function, module, or code path specific enough to find, exact file paths not required — (2) states at least one thing it will not touch, and (3) limits the change to what the issue needs; fails if it bundles refactors, rewrites, new options/features, dependency bumps, or fixes for other symptoms | required |
| approach | approach/steps, scope, and diagnosis sections of the plan (the change may be described in prose; a numbered list is not required) | passes if the plan commits to a specific change (what will be done) at a named location, so a stranger could start without asking the author; honestly flagged unknowns about the exact function or line are fine when the change itself is decided; fails if the work is investigation-only ("investigate", "look into", "figure out", "optimize whatever shows up"), or the location or the change is left undecided | required |
| test | testing plan in plan comment | passes if test plan is present and includes observable proof that the change does what it claims | required |
| thread-repo-conventions | issue description, comment thread, repo contribution information | pass if plan follows all stated conventions or if none exist | required |
| issue-match | issue description, previous reproduction report, and plan | passes if proposed work directly addresses the reported/reproduced behavior and expected result | required |

## Verdict rule

Accept only if every required check passes. Preferred checks never change
the verdict. A failed preferred check is reported as a note only.

Unclear counts as fail for every check. 
