# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| environment-info | the repro report's environment record, the repo-facts block (or the repo's docs in live mode) | pass if a stranger could recreate the setup: it names the OS (with version, unless it is a rolling release like Arch), the language runtime version (not needed for a compiled binary), and the exact project state tested (a commit SHA, release tag, or exact installed release version like `3.2.4 (pip)`; "latest" or bare "main" fails). Wherever the tested environment differs from what the issue reports or targets in a way that could affect the behavior, the report must call out the difference. A project-version difference always counts; an OS difference counts only if the issue is platform-specific; config and flag differences count only if the issue depends on them. For a feature request, the project state must be current `main` (or the difference is called out) | required |
| clear-repro-steps | the repro report's steps, read from the stated starting state to the last step | pass if a stranger starting from a fresh clone could run every step without guessing and the last step reaches the behavior shown. For example, for a bug, the trigger that produces the failure. For a feature request, the code path where the requested capability would run. Fail if any step says "set up" or similar without saying how | required |
| actual-behavior-matches-issue | the repro report's artifacts, the issue description | First classify the issue: a bug report or a feature request. Bug: pass if the artifacts show the same wrong behavior the issue describes, not an adjacent failure. Feature: pass if the artifacts show what the system does today at the point where the requested capability would act, that output visibly lacks the capability, and the report states what the issue expects instead. Cannot-reproduce: pass if the report explicitly says it could not reproduce, its artifacts show the correct behavior at the exact point the issue names, and it names the environment differences that might explain the gap; a report that claims "reproduced" is never graded under this clause. Either kind: fail if the report only asserts the gap or failure with no artifact, or shows it in a different part of the system than the issue names | required |
| honest-outcome | the repro report's stated outcome, its own artifacts | pass if the stated outcome claims no more than the artifacts show: every "reproduced" is backed by an artifact showing the issue's behavior, and every cause is either backed by a file:line or output or marked as a suspicion. An evidenced cannot-reproduce (or, for a feature request, an evidenced "already exists") passes. Fail if the report narrates a result its artifacts do not show | required |
| ai-disclosure | the repo-facts block (or, in live mode, the repo's CONTRIBUTING, PR template, and AI policy docs), the claim comment and the repro report | First decide whether the repo's policy explicitly requires disclosing AI use (e.g. "must be disclosed"). A policy that only governs AI use (e.g. comments must be written by a human) is not a disclosure policy: pass. Policy exists: pass only if the comments disclose AI use in the way the policy asks (or state that no AI was used) | required |
| specific-modest-claim | the claim comment, the issue description | pass if the claim comment names something specific to this issue that could not be pasted onto a different issue unchanged, and promises nothing the package does not back up. Fail if it is a bare request to be assigned or generic boilerplate | required |
| control-run | the repro report's artifacts | pass if the report shows a control: the same steps with the triggering input or condition changed, where the issue's behavior does not appear (for a feature request, a path where the equivalent capability does work, if one exists) | preferred |

## Verdict rule

Accept only if every required check passes. Preferred checks never change
the verdict. A failed preferred check is reported as a note only.

Unclear counts as fail for every check. Except `ai-disclosure` if the repo
states no disclosure policy, or none can be found in the sources the check
names, the check passes.

Claim-only drafts (live mode): checks graded "not yet applicable" are left
out of the verdict. Only `ai-disclosure` and `specific-modest-claim` are
graded, and the verdict is accept only if both pass.
