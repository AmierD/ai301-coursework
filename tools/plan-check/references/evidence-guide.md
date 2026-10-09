# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

- Where it lives: eval: the plan's cause statement, checked against the
  repro-evidence block. Live: the draft plan and comment, checked against
  the author's posted repro comment (or the house repro pack quoted in the
  drafts).
- What good looks like: the stated cause explains the exact behavior the
  repro shows (same command, same output or error) and cites a file/line
  or artifact. Fails if it names a cause the repro never touched or that
  contradicts the observed output.

## Scope

- Where it lives: the plan's in-scope / not-in-scope statements and the
  files or areas it names. Eval: candidate plan. Live: draft plan and
  comment.
- What good looks like: the change site is named precisely enough to
  find (a file, function, handler, or code path such as
  `AdaptDispatch::EraseInDisplay` or "the reattach handshake in
  zellij-server's client connection handling"; exact file paths are not
  required), with at least one explicit "will not touch" line. A change
  limited to what the issue needs, not a refactor of nearby code.
- What fails: bundling extras beyond the fix (rewrites, new
  abstractions, new user options, dependency upgrades, fixes for other
  symptoms "while in there"), even when every file is named.

## Executability

- Where it lives: the plan's approach or steps section.
- Where else: the change may be stated in prose inside the scope or
  diagnosis sections; a numbered list is not required.
- What good looks like: a decided change at a named location (file,
  function, or code path). A stranger could start without asking the
  author anything. Honestly flagged unknowns about the exact function or
  line ("exact clamp site may move one level", "functions to be pinned
  after tracing") are fine as long as *what* will change is decided.
- What fails: "fix the handler" with no location; investigation-only
  steps ("investigate", "look into", "profile and optimize whatever
  shows up", "not sure which layer"); or a choice left open ("upstream
  or vendored, whichever is easier").

## Test plan

- Where it lives: the plan's test plan. Map it against the repro steps.
- What good looks like: reruns the repro steps and names the expected
  output after the fix, plus a test (new or existing, by name or file)
  that fails before and passes after. "Run the tests" alone fails.

## Honesty

- Where it lives: the plan's risks, unknowns, or open questions; in live
  mode, any deviation note recorded in `plan.md`.
- What good looks like: unverified claims are hedged ("I suspect") and
  unknowns are listed. Certain wording about something the repro didn't
  show is false confidence. Deviations are recorded in the plan, not only
  in the diff.

## Comms

- Where it lives: eval: the plan comment, checked against the issue
  context (thread highlights) and the repo-facts block (templates,
  CONTRIBUTING asks, AI-use disclosure policy). Live: the draft comment,
  checked against the issue thread and the repo's CONTRIBUTING.md or docs.
- What good looks like: the comment follows every stated convention
  (branch naming, required disclosure, template sections), responds to
  what maintainers said in the thread, and links the repro. Generic text
  that could be posted on any issue fails.
