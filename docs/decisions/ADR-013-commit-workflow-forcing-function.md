# ADR-013: `/commit` workflow as forcing function for safe git commits

**Status**: Accepted
**Date**: 2026-04-29
**Supersedes**: ADR-012
**Context**: ADR-012 codified the multi-paragraph git commit pattern (multiple `-m` flags or `-F <file>`) and amended `~/.windsurf/rules/no-terminal-oneline-scripts.md` to reflect it. Within the same Sprint 1 hardening session, the authoring Cascade (me) issued a `git commit -m "..." -m "..."` invocation with **embedded newlines inside the `-m` values** — exactly the pattern ADR-012 forbade. The user caught it and cancelled the command before it ran. Empirically: a rule authored minutes earlier did not prevent the same agent from violating it.

The user's framing was direct: "inliner commands will crash the terminal of windsurf on mac. this is also a big problem. I dont know how to integrate this in the system so its never ignored or forgotten."

The lesson: **rules-as-text are insufficient when the unsafe path is the path of least resistance**. The agent (any Cascade) reaching for `git commit` will produce inline commands by default — that's how `git commit` is documented everywhere. Making the safe path the **only** path requires a forcing function: a workflow the agent invokes instead of writing the command directly.

**Decision**: Author a `/commit` workflow at `~/.windsurf/workflows/commit.md` that:

1. Receives `<repo>`, `<subject>`, `<body>`.
2. Writes the message to `/tmp/cascade-commit-<unix-timestamp>.txt` via the `write_to_file` tool (NOT a heredoc, NOT inline shell content).
3. Runs `git -C <repo> commit -F /tmp/cascade-commit-<timestamp>.txt` — a single-line shell command with no embedded newlines anywhere.
4. Cleans up the tempfile.

The `/commit` workflow is **mandatory** for any commit with body content. Plain `git commit -m "subject"` remains acceptable for short subject-only commits.

The associated rule (`no-terminal-oneline-scripts.md`) is strengthened in lockstep:

- Header `description` field uses **CRITICAL / WILL CRASH** language (not "fragile") so the constraint is unambiguous.
- A **pre-flight checklist** is added at the top of the rule body — a runtime invariant the agent self-runs before every `run_command` call.
- The forbidden-patterns table now lists multi-`-m` with embedded newlines explicitly, and the decision tree routes any body-bearing commit to `/commit`.

A persistent agent **memory** is also created with the same constraint, so the rule loads into every Cascade session's working context, not only when the rule file is read.

**Alternatives considered**:

- **Strengthen ADR-012 without supersession** — rejected; the original codified the wrong primary path (multi-`-m`). Multi-`-m` is conditionally safe (each value must be single-line), and the conditional was the trap. `-F` is unconditionally safe; making it canonical removes the trap.
- **Tooling-level interception** of `run_command` — rejected; we don't have hook capability into the Cascade tool surface.
- **A pre-commit git hook that rejects commits with newlines in `-m`** — irrelevant; the crash happens before the commit reaches git, in the terminal-emulator layer.
- **Document the constraint in a separate "operating constraints" doc** — rejected; orphaned constraint docs don't load into agent context. The rule + memory + workflow triad is the right shape.

**Consequences**:

- All future commits with body content go through `/commit`. The agent's natural inclination to write `git commit -m "..." -m "..."` is intercepted by the workflow's existence and the strengthened rule.
- The crash hazard is structurally eliminated for commits — the only remaining inline-newline crash surface is non-`git-commit` invocations (heredocs, `python -c`, etc.), which the strengthened rule already forbids.
- A small ceremony cost (one `write_to_file` tool call + one cleanup) is added per multi-paragraph commit. Worth it.
- Persistent memory ensures the constraint survives across Cascade sessions, not just the one that authored the rule. This addresses the "never ignored or forgotten" requirement directly.
- ADR-012 is retained per ADR-011 retention policy; its `Status: Superseded by ADR-013` documents the lesson trail.

**Source**: Sprint 1 hardening session, in-session observation of authoring Cascade violating ADR-012 within minutes. User feedback: "inliner commands will crash the terminal of windsurf on mac" — the hard runtime constraint that motivated the forcing-function approach. Implementation:

- `~/.windsurf/workflows/commit.md` (new — the forcing function)
- `~/.windsurf/rules/no-terminal-oneline-scripts.md` (strengthened — crash-level language, pre-flight checklist)
- Persistent memory titled "Windsurf macOS terminal: embedded-newline crash" (created in this session)
- ADR-012 marked `Status: Superseded by ADR-013`
