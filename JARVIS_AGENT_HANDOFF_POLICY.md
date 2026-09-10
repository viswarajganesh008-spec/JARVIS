# JARVIS — AI Agent Persistence & Handoff Policy

**Status: mandatory, applies to every phase, every sub-unit, every AI agent
(Claude, ChatGPT, Gemini, or any other coding agent) working on this project.**

## The rule

> No phase may depend on the memory of a particular AI conversation.
> GitHub + `CONTINUATION_STATE.md` must contain everything required for
> another AI agent to continue the project — with no prior context, and
> without the user re-explaining anything.

A conversation ending, a context limit being hit, or a session switching
from one AI agent to another must never be able to lose work or lose
context. The repository is the single source of truth. A chat log is not
a deliverable and is never where project state lives.

## Why sub-unit granularity, not phase granularity

Documenting progress is not the same as securing it. If Phase 3 has five
units and an agent stops mid-way through unit C:

```
Phase 3
├── A ✅ done, committed
├── B ✅ done, committed
├── C 🔄 70% done — stopped here
├── D ⏳ not started
└── E ⏳ not started
```

...then only work that is **committed and pushed** is guaranteed to
survive. Anything written to disk but not committed can be lost the
moment the session ends. Therefore:

> **Every meaningful completed sub-unit must be committed to GitHub, not
> merely documented.**

"Meaningful" means: it runs, it's tested, and it doesn't break anything
that was working before. A commit is the unit of durability — not the
phase, not the session.

## The mandatory chain, per sub-unit

```
Work on a sub-unit (e.g. Phase 3.3 — Tool selection)
        ↓
SAVE the code
        ↓
TEST (full suite must stay green, plus new tests for the sub-unit)
        ↓
UPDATE CONTINUATION_STATE.md (what's done, what's in progress, what's next)
        ↓
COMMIT
        ↓
PUSH
        ↓
                    GitHub (source of truth)
                         ↓
              Next AI agent, any session, any model
                         ↓
        Reads CONTINUATION_STATE.md + latest commits + code
                         ↓
              Continues exactly where the last agent stopped
```

This repeats for every sub-unit, not once per phase. A phase is just the
label under which its sub-units are grouped; the commit boundary is what
actually protects the work.

## What a handoff requires the repository to contain

For Agent 2 to take over Phase 3C without the user re-explaining
anything, the repository must let them reconstruct, from the repo alone:

1. What A and B completed (commit history + `CONTINUATION_STATE.md`)
2. Exactly what C had completed and what C still needs (in-progress
   section of `CONTINUATION_STATE.md`, plus the actual partial code —
   never described as "working" if it isn't tested)
3. Which tests already pass, and how to run them
4. Known problems / honest limitations (not hidden, not glossed over)
5. The exact next action — specific enough to start coding immediately,
   not "continue Phase 3"

`CONTINUATION_STATE.md` is the index; the git history and the code are
the evidence. Neither one alone is sufficient — a state file without
matching commits is a claim nobody can verify, and commits without a
state file force the next agent to reverse-engineer intent from diffs.

## What this means in practice, every session

- Work is broken into sub-units small enough to commit individually —
  not so small that commits are noise, not so large that a session limit
  could erase real progress.
- `CONTINUATION_STATE.md` is updated **at each sub-unit boundary**, not
  only at the end of a session. If a session is cut off mid-unit, the
  last update should reflect the last *fully committed* sub-unit, and
  honestly mark the in-progress one as partial.
- Nothing is ever described in `CONTINUATION_STATE.md` as done unless it
  is both committed and tested. "In progress" and "done" are never
  blurred together.
- An agent picking up a project always reads `CONTINUATION_STATE.md`,
  the recent commit log, and the relevant code — in that order — before
  writing anything, rather than trusting its own assumptions about
  project state.

## Known constraint in a sandboxed environment (this session's case)

An AI agent working in an environment with no git repository present
and no network access (as in this sandbox) cannot itself commit or push.
In that case the chain becomes:

```
SAVE → TEST → UPDATE CONTINUATION_STATE.md → [package for the user]
                                                      ↓
                                    user commits + pushes from an
                                    environment that has git/network
```

The agent's responsibility doesn't shrink because it can't push — it
still must leave `CONTINUATION_STATE.md` and the code in a state where,
once the user does commit and push, any next agent can pick it up
cleanly. The agent should say so explicitly rather than implying the
work is already secured in the repository.
