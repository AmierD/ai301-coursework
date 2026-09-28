# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives**

- Eval: repro report's environment block. Compare to issue context and
  repo-facts block.
- Live: draft's environment section. Compare to issue body, README/
  CONTRIBUTING, and the repo's releases/default branch.

**What good looks like**

- Names OS (+ version unless rolling, e.g. Arch), runtime version (skip for
  compiled binaries), and a SHA, tag, or exact installed version like
  `3.2.4 (pip)`. "latest" or bare "main" fails.
- Differences that could affect the behavior are stated, not left for the
  reader to find: project version always; OS only if the issue is
  platform-specific; config/flags only if the issue depends on them.
- Feature request: tested on current `main`, or says why not.

## Steps

**Where it lives**

- Eval: repro report's numbered steps. Compare to the issue's own steps
  and the repo-facts block (install/build commands).
- Live: draft's steps. Compare to the issue body and README/CONTRIBUTING
  setup instructions.

**What good looks like**

- Starts from a stated state (fresh clone at a SHA) and every step is a
  runnable command or exact action; "set up"/"configure" with no how fails.
- The last step triggers the behavior: the failing input for a bug, the
  code path where the feature would run for a feature request.

## Behavior shown

**Where it lives**

- Eval: repro report's artifacts (output, logs, tracebacks, screenshots).
  Compare to the issue description.
- Live: output/logs pasted in the draft. Compare to the issue body and
  any output the reporter posted.

**What good looks like**

- Bug: artifact shows the same wrong behavior (same error, message, or
  value) in the same part of the system the issue names.
- Feature: artifact shows today's output lacking the capability, and the
  report states what the issue expects instead.
- Cannot-reproduce: says so outright, artifacts show correct behavior at
  the exact spot the issue names, and names the environment differences
  that might explain it.
- Control (preferred): same steps with the trigger changed, and the
  behavior does not appear.

## Honesty

**Where it lives**

- Eval: repro report's stated outcome and any cause it names. Compare to
  its own artifacts.
- Live: the draft's outcome and cause lines. Compare to what the draft
  actually pastes.

**What good looks like**

- Every "reproduced" points to an artifact showing the issue's behavior.
- Every cause cites a file:line or output, or is labeled a suspicion.
- Cannot-reproduce or "already exists" passes when backed by output.

## Comms

**Where it lives**

- Eval: the claim comment and repro report. Compare to the issue and the
  repo-facts block (templates, contribution and AI policy).
- Live: the draft comment. Compare to the issue thread and the repo's
  CONTRIBUTING, PR template, and AI policy docs.

**What good looks like**

- Claim names something only true of this issue and promises nothing the
  package doesn't back; "can I work on this?" alone fails.
- Only a policy that explicitly requires disclosure (e.g. "must be
  disclosed") triggers the check; then the comments disclose as asked.
  Rules like "comments written by a human" are not disclosure rules: pass.
