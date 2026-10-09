# Voice guide: how I talk upstream

## Who I am in threads

I am a third-year computer science student with a strong foundation in software
engineering practices through academics (CodePath Tech Exchange: Introduction
to Software Engineering with GenAI) and industry experience (Google
Internship). I am confident in my abilities, but aware that I still have a lot
to learn about software engineering and therefore bring a humble, open,
beginners mind to all experiences.

## Rules I write by

### Rule: no-claims-without-evidence

Any claim about what causes a bug or what the code does must point to evidence
a reader can check, like a file with a line number, a command and its output,
or a link. If I haven't reproduced it, I say "I suspect" or "it looks like,"
never "the issue is."

- Wrong: "The issue is that the cache never gets invalidated, so stale data is
returned."
- Right: "I suspect the cache isn't invalidated on update. `save()` in
`store/cache.py:88` writes to the DB but never calls `cache.clear()`. I
haven't reproduced it yet."

### Rule: list-what-i-tried

When I ask for help, I list each thing I tried, with the exact command or
change, what I expected, and what actually happened (the real error text, not
a summary of it).

- Wrong: "I couldn't get the tests to run. I tried a few things but nothing
worked."
- Right: "I can't get the tests to run locally. I tried:
  1. `pytest`: `ModuleNotFoundError: No module named 'toolkit'`
  2. `pip install -e .` then `pytest`: same error
  3. Python 3.11 instead of 3.12: same error
  Is there a setup step I'm missing?"

### Rule: do-not-over-apologize

I apologize only when I actually caused a problem (broke something, went quiet
on a claimed issue). I never apologize for asking a question or for being new.

- Wrong: "Sorry if this is a dumb question, I'm still pretty new to this, but
is the config loaded before or after the plugins?"
- Right: "Is the config loaded before or after the plugins? I traced it to
`init()` but couldn't tell from there."

### Rule: no-extraneous-details

Every sentence in a comment must help the reader understand the problem or act
on it. I should not include any backstory about how I found the repo or why I'm
interested.

- Wrong: "I've been looking for a project to contribute to for my class and
came across this repo, which looks really cool! I noticed that the CLI crashes
when the config file is empty."
- Right: "The CLI crashes when the config file is empty. Repro: `touch
config.yml && tool run` → `KeyError: 'name'`."

### Rule: transparent

When I claim or work on an issue, I state plainly that I'm a first-time
contributor to this repo and what I have and haven't verified. I don't hide
that I'm new, and I don't lead with it as an excuse.

- Wrong: "I'll take this one, should be a quick fix."
- Right: "I'd like to take this. It's my first contribution here, so I've read
CONTRIBUTING.md and reproduced the bug locally, but I haven't run the full
test suite yet."

## Things I never post

I do not make promises I cannot keep. I do not rush or bother maintainers if I
do not immediately get a response. I do not blame code or its authors.
