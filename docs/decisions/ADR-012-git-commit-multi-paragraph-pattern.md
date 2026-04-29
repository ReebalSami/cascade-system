# ADR-012: `git commit` multi-paragraph pattern — use multiple `-m` flags, not embedded newlines

**Status**: Superseded by ADR-013 (same-day, before first commit — see Revision note)
**Date**: 2026-04-29
**Superseded the same day** by ADR-013 (`/commit` workflow as forcing function), after in-session observation that this rule alone was insufficient — the authoring Cascade violated it within minutes of writing it. ADR-013 introduces a workflow-level forcing function that makes the safe path the only path. This ADR is retained per ADR-011 retention policy.
**Context**: Sprint 1 review (`docs/reviews/sprint-1-review.md` §3.3 m-4) found that `/start-project` (M1.4)'s scaffolded commit-message snippet used `git commit -m "subject\n\nbody line 1\nbody line 2"` with embedded newlines inside a single `-m` flag. The user had a saved memory flagging this as fragile in the Windsurf integrated terminal — the pattern can crash or mis-quote in some shells and terminal emulators. The memory existed but was never promoted into L1, so `/start-project` shipped the anti-pattern. This is a specific case of the broader `no-terminal-oneline-scripts` discipline, but that rule didn't call out the git-commit case explicitly.

**Decision**: Extend `~/.windsurf/rules/no-terminal-oneline-scripts.md` with an explicit "Git commit messages" section documenting two acceptable patterns:

1. **Multiple `-m` flags** — each `-m` produces one paragraph, joined with blank lines automatically:
   ```sh
   git commit -m "subject" -m "paragraph 1" -m "paragraph 2"
   ```
2. **`-F <file>`** — for very long messages, write to a file and reference it:
   ```sh
   git commit -F /tmp/commit-msg.txt
   ```

And explicitly forbid embedded newlines in a single `-m`:
```sh
# ✗ Fragile in integrated terminal
git commit -m "subject

body paragraph"
```

`/start-project`'s step-9 snippet is amended to use multiple `-m` flags.

**Alternatives considered**:

- **A new dedicated rule `multi-paragraph-git-commits`** — rejected; granular rules fragment the `no-terminal-oneline-scripts` discipline that already covers this space. Extension is cleaner.
- **Leave as a memory** — rejected; memories are per-user and don't propagate to authored L1 artifacts. The review found the memory didn't prevent the mistake, so promotion is warranted.
- **Forbid multi-paragraph commits entirely (subject-only)** — rejected; informative commit bodies are valuable, and the review commits themselves benefit from multi-paragraph detail.

**Consequences**:

- `/start-project`'s commit snippet becomes robust across shells and terminal emulators.
- Any future authored skill/workflow that scripts a git commit inherits the pattern.
- The first sprint-hardening commit (this batch) uses the pattern — dogfooding confirms it works.
- Downstream: the `_shared/scaffold/` README and any authoring guidance point at the rule.

**Source**: Sprint 1 review report `docs/reviews/sprint-1-review.md` §3.3 m-4 + user's saved memory on `git commit -m` multi-line usage. Amendments land in `~/.windsurf/rules/no-terminal-oneline-scripts.md` + `~/.windsurf/workflows/start-project.md` in the same hardening batch.
