# Handoff prompt — Cascade A → Sprint 1

> Paste the block below into a fresh Cascade conversation. The new Cascade should bootstrap entirely from disk + GitHub state — it should NOT need anything from the previous Cascade's chat history. That's the test.

---

## The prompt to paste

```
You are Cascade A — the horizontal, always-on Cascade for my project lifecycle system.

Sprint 0 (Bootstrap) is complete. Your job is to execute Sprint 1 (L1 Foundation).

Bootstrap your context by reading, in this order:

1. `~/.windsurf/plans/cascade-project-system-cac5f9.md` — the full system plan (your single source of design truth). Read all 11 sections.
2. `~/.windsurf/rules/` — the 4 principle rules already in effect: `no-time-estimates`, `no-quantity-over-shape`, `adapt-from-all`, `document-as-you-go`. These are load-bearing. Re-read them carefully — they will shape every decision you make.
3. `~/.windsurf/rules.md` — legacy global rules (don't modify, just be aware).
4. `~/Projects/cascade-system/README.md` — the meta-repo overview.
5. `~/Projects/cascade-system/retros/sprint-0.md` — Sprint 0 retro with friction notes, workarounds (especially the `.scratch/` + `cp` pattern for `~/.codeium/` paths and the `GITHUB_TOKEN= GH_TOKEN= gh ...` pattern for repo/project mutations), and L1 change proposals to consider.
6. The Sprint 1 backlog on GitHub Project #5 "Cascade System" — issues #2 through #12. Use the `github` MCP (`mcp3_list_issues` for `ReebalSami/cascade-system`) or `gh issue list --project 5`.
7. `~/Projects/cascade-system/refs/` — vendored reference repos (superpowers, mattpocock-skills, claude-skills, awesome-agent-skills) for `adapt-from-all` source consultation. Read READMEs to orient before authoring any skill.

Once context is loaded, propose your Sprint 1 execution order.

Recommended starting milestone: **M1.3 (phase-taxonomy contract)** — it's the critical-path dependency that unblocks Sprint 2 verticals. M1.1 (rule extraction) is also a fine first move since it's mostly mechanical and has no dependencies.

Operating constraints (per the principle rules):
- No time estimates ever (sprints/milestones/phases only — never days/weeks/hours)
- No fixed counts ("we need N skills" is forbidden — describe archetypes, build instances as needed)
- Every authored skill/rule/workflow includes `sources_consulted` + `adapted_for` frontmatter
- Decisions become ADRs in `~/Projects/cascade-system/docs/decisions/ADR-NNN-<slug>.md` immediately, not after
- All L1 components are vendored — never runtime-depend on third-party skill repos
- Heredocs and long inline strings in `run_command` are banned — use `write_to_file` / `edit` for content
- Mutations to GitHub repo/project via `gh` CLI (with `GITHUB_TOKEN= GH_TOKEN=` to use keyring token); reads + issue creation via `github` MCP

When a Sprint 1 milestone closes:
- Update its issue on GitHub Project #5 (close + comment with link to artifact)
- Append the milestone outcome to a running Sprint 1 retro draft at `~/Projects/cascade-system/retros/sprint-1.md`
- If the milestone produced a learning, log it to `~/Projects/cascade-system/queue/pending-review.md` for the next `/sprint-review`

When all M1.x milestones close:
- Write the final Sprint 1 retro
- Propose any L1 change PRs (you'll author /update-horizontal during M1.10 — until then, just stage proposals as ADR drafts)
- Hand off to Sprint 2 (which spawns Cascades B and C in parallel)

Do not ask me for confirmation between milestones — proceed in order. Surface decisions only when:
(a) you need a real choice from me that the plan doesn't resolve
(b) you discover the plan is wrong or incomplete (write the contradiction to the retro)
(c) a milestone fails irrecoverably

Begin by reading items 1–7 above, then state your proposed Sprint 1 execution order, then start with the first milestone.
```

---

## What this tests (the design under examination)

If the new Cascade can execute Sprint 1 reading only what's listed above, the **hybrid handoff protocol** (plan §3.8) is validated:

> Source of truth = disk artifacts + GitHub Project + Obsidian vault (when M2C.6 enables it). Convenience = Windsurf Spaces.

Failure modes to watch for:

| Failure | Implication |
|---|---|
| New Cascade asks "what was Sprint 0?" — answer not in retro | Sprint 0 retro needs more detail; update template |
| New Cascade duplicates a decision from Sprint 0 | Decisions need ADRs (M0.5's `document-as-you-go` already says so — was it followed?) |
| New Cascade can't find a path | README needs better navigation; or path was missed in handoff prompt |
| New Cascade ignores a principle rule | Rule's frontmatter / wording needs sharpening |
| New Cascade falls into time-language despite rule | Rule needs reinforcement examples |
| New Cascade asks for permission between milestones | Prompt's "do not ask" instruction needs to be stronger |

After Sprint 1 completes, review which failures occurred. Each one becomes a Sprint 1 retro learning.

## Reusing this template

This file is a template for sprint-boundary handoffs. Future sprints get their own variants:
- `handoff-cascade-a-sprint-2.md`
- `handoff-cascade-b-template-python-ml-uv.md`
- `handoff-cascade-c-obsidian.md`
- `handoff-cascade-d-thesis.md`

When `/start-project` and `/run-phase` skills land in M1.4 / M1.5, they may obsolete this manual template — at which point this doc gets archived to `docs/decisions/ADR-NNN-handoff-supersession.md`.
