# Long-form rules archive

This directory holds the **full long-form** versions of the global rules with rationale, provenance (`sources_consulted`), `adapted_for` notes, and examples.

The **concise law** version lives at `~/.codeium/windsurf/memories/global_rules.md` — that's what every Cascade conversation actually reads. The 6,000-character cap on global rules forces the split: law there, commentary here.

Per `/start-project`, every new project gets these full files copied into `<project>/.windsurf/rules/` so the workspace-level loader picks them up regardless of git-root depth.

## Rules

| File | Trigger | Concise tagline |
|---|---|---|
| `adapt-from-all.md` | always_on | When authoring L1: consult multiple sources, cite provenance, vendor — never runtime-depend. |
| `l1-canonical-paths.md` | always_on | Windsurf reads global skills/workflows from `~/.codeium/windsurf/`; workflows in `global_workflows/`. Never `~/.windsurf/`. |
| `anti-laziness-core-principles.md` | always_on | No shortcuts. One path, justified. Repair over discard. Production-quality always. |
| `be-honest-direct-critical.md` | always_on | 100% honest/direct/critical. Cite facts. No flattery, no hedging. Concise. |
| `bidirectional-learning-pipe.md` | always_on | Capture insights to queue at the moment they appear; `@sprint-review` drains. |
| `branch-and-pr-required.md` | always_on | Never push to `main`. All changes via `@release-manager` → branch → PR → CI → squash-merge. Cold-start exception: first push after `gh repo create`. |
| `clean-project-structure.md` | always_on | Every artifact in its canonical directory. New top-level paths require an ADR. |
| `context7-and-docs-first.md` | model_decision | Consult current docs via `context7` MCP before implementing any external dependency. |
| `document-as-you-go.md` | always_on | ADRs at the moment of decision. Reserve NNN in INDEX before authoring. Supersede, never delete. |
| `know-your-hardware.md` | model_decision | M1 Pro 16GB Metal/no-CUDA. On heavy compute (training, large datasets, GPU/CUDA libs, AWS): surface need + budget + alternatives, get user approval. **Workspace-deployed only** (not in `global_rules.md`); copied via `/start-project` step 6a. |
| `make-sure-it-works.md` | always_on | Evidence over claims. Run lint/build/test/demo before declaring done. |
| `no-half-knowledge.md` | always_on | Read files fully. Map with grep, then full reads. No `head`/`tail`/snippets as understanding substitute. |
| `no-quantity-over-shape.md` | always_on | No fixed counts ("build 5 skills"). Archetypes + instances as work demands. |
| `no-terminal-oneline-scripts.md` | always_on | **CRITICAL.** No embedded newlines in `run_command` quoted args (Windsurf macOS terminal crash). Use `/commit` for commits with body. |
| `long-running-foreground.md` | always_on | Long-running commands stay foreground with live streaming output. No background-then-poll, no `/dev/null` suppression, no buffering pipes. Sibling to `no-terminal-oneline-scripts` (crash-safety vs observability). |
| `no-time-estimates.md` | always_on | No time-based language. Event-based only: sprint, milestone, phase, "when X holds". |
| `plan-drift-watcher.md` | model_decision | Surfaces drift between PRD slices, GitHub state, and commits. Recommends `/recalibrate`. |
| `sprint-review-prompt.md` | model_decision | Suggests `@sprint-review` when a GitHub milestone closes. |
| `obsidian-context-priming.md` | model_decision | Primes Cascade on the Obsidian vault at session start when the active project has vault co-location (`phases.yaml` `obsidian://` paths or `linked_software:<repo>` vault hits). Three-tier load; privacy guardrails from prompt-injection literature. **Workspace-deployed only** (per `know-your-hardware` precedent); copied via `/start-project` step 6a; no concise entry in `global_rules.md`. ADR-028. |
| `model-selection-advisor.md` | model_decision | Emits one-line advisory when phase-typed signals suggest the active session would benefit from a different model — Sonnet 4.6 1M for code-heavy work, Opus 4.7 for decision-heavy work. Points to `@handoff-to-coding-session` / `@handoff-to-thinking-session` skill pair. Never auto-switches; never blocks; per-session suppression after one decline. **Workspace-deployed only** (per `know-your-hardware` + `obsidian-context-priming` precedent); copied via `/start-project` step 6a; no concise entry in `global_rules.md`. ADR-034. |

## Notes

- The originals lived at `~/.windsurf/rules/` until 2026-04-30 when ADR-014 relocated them — that path is **deprecated** because Windsurf does not scan it. See ADR-014 for the migration record.
- `@verify-l1` skill validates that the concise versions in `global_rules.md` stay byte-bounded and that this archive matches the rule set.
- When updating a rule: edit BOTH the long-form here AND the concise version in `global_rules.md`. The two are siblings, not source/derived.
- **Workspace-only rules** (`know-your-hardware.md` as of ADR-018): the long-form lives here AND is copied to per-project `.windsurf/rules/` by `/start-project` step 6a, BUT has no concise entry in `global_rules.md`. Per Windsurf docs, only workspace rules support `model_decision` activation; global rules are always-on regardless of annotation. ADR-018 §"Brainstorm drift correction" explains the architecture.
