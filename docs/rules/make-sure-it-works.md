---
trigger: always_on
description: Evidence over claims. Verify changes by running the project's lint/build/test/demo before declaring success. "Should work" without evidence is a violation.
sources_consulted:
  - portfolio-website/.windsurf/rules/make-sure-it-works.md (own, primary — heavily adapted)
  - portfolio-website/.windsurf/rules/local-demo-before-push.md (own, secondary — spirit absorbed)
  - obra/superpowers/skills/verification-before-completion (MIT, refs/superpowers/skills/verification-before-completion) — closest upstream analog; adapted naming and scope
  - mattpocock/skills (browsed, no direct rule analog)
  - HORUS PR #20 (May 2026, own — empty-PDF episode) — precedent for the `## Artifact review before push` subsection added below
adapted_for:
  - L1 global rule (was L2 portfolio-only with stack-specific commands)
  - Windsurf rule frontmatter
  - Stack-agnostic: project-defined verification commands, not hardcoded `make lint/build`
  - Absorbed `local-demo-before-push.md` so we have one rule for "verify before declaring done", not two
  - Extended (May 2026) with the `## Artifact review before push` subsection, forcing-function paired with `@release-manager` step 4 (mirrors the `no-terminal-oneline-scripts` + `/commit` rule+skill pairing pattern)
---

# Make sure it works

**Evidence over claims.** Before declaring any change complete:

- **Run the project's verification commands.** What "verification" means is project-defined: tests, linter, type-checker, build, manual demo, or a combination. Each L3 template documents its own (e.g., python-ml-uv → `pytest` + `ruff` + `mypy`; future nextjs-app → `pnpm lint` + `pnpm build` + browser demo).
- **Document outcomes.** Commit messages or chat summaries name what was verified and the result. "Tests pass" without specifying which is insufficient.
- **No "should work" without evidence.** If you cannot run verification (sandbox limitation, missing tooling, etc.), say so and provide the exact command for the user to run.
- **No hardcoding** of paths, IDs, credentials, constants, or user-facing strings. Use config files, environment variables, or i18n keys per the L3 template's conventions.
- **For UI work**: validate at expected breakpoints; check theme variants if the project supports them. Specific breakpoints/themes are L3-defined.
- **Iterate until acceptance criteria are met.** "It compiles" is a checkpoint, not a finish line.
- **Artifact review before push.** For branches producing tangible artifacts (PDFs, images, generated files, demos, HTML, scaffolds, CLI output that is part of the deliverable): surface each artifact for human-eye review BEFORE invoking `/branch-push-and-pr`. Automated checks verify structural correctness; they do not verify product-fitness. `@release-manager` step 4 enforces this via the artifact-review gate. See `## Artifact review before push` below for the full procedure + precedent.

## Artifact review before push

Tests check structural correctness. Human-eye review checks product-fitness. **Both are required.** The two checks are complementary, not substitutable — a result can be structurally correct (tests pass, validators say OK) yet product-useless (the artifact does nothing the project needs).

### When this clause fires

Any branch whose commits produce tangible artifacts visible to a human:

- **Generated documents** — PDFs, images, plots, charts, diagrams, exported reports
- **Demo / UI artifacts** — HTML demos, screenshots, UI states, compiled CSS previews
- **Generated configs / scaffolds** — files produced by a `/start-project` / template-expansion flow that land in the user's tree
- **CLI output that is part of the deliverable** — when the deliverable IS the output (e.g., a data-conversion script whose result file is the artifact)

The clause does NOT fire for **structural** changes: refactors with no observable behavior change, docs-only PRs, dependency bumps without behavior change, internal-only utility additions. Default is gate-on — erring toward showing artifacts is the safe failure mode.

### What Cascade must do (the 4-step procedure)

Before invoking `/branch-push-and-pr`:

1. **Enumerate** every artifact produced or modified by this branch's commits (diff + new files under `data/`, `artifacts/`, `dist/`, `out/`, or any L3-template-declared output directory).
2. **Surface** each artifact in the conversation:
   - Text / config → paste content inline
   - Binary (PDFs, images) → show path + size + a one-line visible-content summary; reference a preview method (`open <path>` on macOS; Chrome DevTools for HTML; IDE preview for images)
   - Interactive (HTML demos, UIs) → reference the preview command (Playwright / Chrome DevTools navigation)
3. **Ask explicitly**: "These are the artifacts produced by this branch. Acceptable to proceed to `/branch-push-and-pr`?"
4. **Wait for explicit user acknowledgment** before delegating to the push workflow.

### Precedent (don't repeat)

**HORUS PR #20** (M2D.5 step 2, May 2026). `make zugferd-smoke` produced a Factur-X PDF that passed `factur-x`'s built-in XSD + Schematron AND Mustang's independent cross-tool Schematron (27 rules fired, 0 failures, `<summary status="valid"/>`). Every automated check was green. Cascade declared completion, authored an ADR with the validation evidence, committed, pushed, opened a PR, attached labels + milestone, reported "implementation complete".

The PDF visual layer was blank — a `pypdf.PdfWriter.add_blank_page` artifact. For HORUS specifically (a VLM-based document extraction thesis), a blank PDF is useless: the VLM has nothing to look at. The embedded CII XML was structurally valid; the visual carrier was product-useless.

User caught the gap post-push by opening the PDF. The toolchain (`factur-x` + Mustang dual-track) was genuinely proven; the artifact was not fit for HORUS's actual purpose. Automated checks were correct AND incomplete.

**The lesson**: a 30-second human-eye review of the PDF would have caught the gap before push. No test could have — the tests checked the XML, not the pixels. Only a human eye (or a VLM probe, which is what HORUS is ultimately building) can check "does this artifact do what the project needs?".

### Forcing function

`@release-manager` skill step 4 (artifact review gate) is the active enforcement mechanism. The rule-alone defeat mode is "Cascade forgets the rule mid-flow"; the skill step makes the gate load-bearing by blocking the push-workflow invocation until step 4 completes.

This rule + skill pairing mirrors the proven precedent from Sprint 1 hardening:

- `no-terminal-oneline-scripts` (rule) + `/commit` (skill/workflow) — ADR-013 precedent
- `branch-and-pr-required` (rule) + `@release-manager` (skill) — ADR-018 precedent
- `make-sure-it-works` § Artifact review (rule clause) + `@release-manager` step 4 (skill step) — this addition

Rule-as-text alone proved insufficient in every case; the active forcing function is what converts the discipline into invariant.

### Related

- `@release-manager` SKILL.md step 4 — the active gate
- Concise form in `~/.codeium/windsurf/memories/global_rules.md` `## make-sure-it-works` — one-sentence mirror pointing to `@release-manager` step 4
- ADR-013 (commit workflow forcing function) — original rule+skill precedent
- ADR-018 (release-discipline cluster) — rule+skill precedent that this extends

This rule reinforces the legacy `~/.windsurf/rules.md` entry "Always verify before assuming". No conflict.

Source: portfolio-website (own — distilled from `make-sure-it-works.md` + `local-demo-before-push.md`; portfolio-specific verification commands removed). Upstream cross-check: superpowers `verification-before-completion` confirms the principle is well-established. Artifact-review subsection (May 2026): HORUS PR #20 empty-PDF episode (own, new precedent).
