# Archive — Sprint 4 entries resolved during the horizontal prelude (M4A.x)

> Started 2026-09-06 at M4A.1 (ADR-037 apply, `ReebalSami/cascade-system#114`). Entries here were **RESOLVED** — the proposed L1 change landed in a PR — and are moved out of the live queue with a resolution note, per the queue-archive policy (`queue/archive/sprint-1-5.md` precedent). M4A.2 (ADR-038 apply, `#115`, 2026-09-18) appended the M2B.3/M2B.6 token-substitution entry and the 2026-09-05 client-repo placement entry.

---

## RESOLVED — Sprint 2 — cascade-system — Cascade B — M2B.3 (`/start-project` token substitution for `<pkg>` placeholder)

**Resolution date**: 2026-09-18 (M4A.2, PR for #115 — ADR-038)
**Decision**: RESOLVED — Option A landed as `/start-project` step 6c with the M2B.6-refined dual-token vocabulary (`your-pkg` → `<slug-kebab>`, `your_pkg` → `<slug-snake>`; web pair `your-site` / `your_site` added for `nextjs-marketing-site`), normative in `~/.windsurf/contracts/phase-taxonomy.md` §10.1 (additive, no version bump). Implementation is `grep -rIl` + `sed -i ''` for contents and `find -depth` + `mv` for path names, applied after step 6b so the L3 skill copies in `.agents/skills/` are substituted too; `--dry-run` substitutes as well and `scripts/dry-run.sh` asserts that no literal token remains. Evidence: dry-run tree `src/python_ml_uv_test/`, `name = "python-ml-uv-test"`, `uv sync && make test && make lint && make typecheck` green. python-ml-uv README "Post-bootstrap rename" replaced by a "Naming" note; `__init__.py` docstring and the two L3 skills' guard clauses rewritten in `<slug>` metasyntax (a literal token in prose is itself substituted). The paired B11 follow-on (autodetect latest-stable Python) was **not** bundled — still a separate `/start-project` enhancement.
**Original entry text**:

- **Insight**: M2B.3 (`python-ml-uv/scaffold/` authoring) hit a load-bearing gap: `/start-project` does plain `cp -R ~/.windsurf/templates/<type>/scaffold/. <parent>/<name>/` (workflow steps 3–4) with **no token substitution**. The python-ml-uv scaffold ships `src/__pkg__/__init__.py` (and references `__pkg__` from `tests/test_smoke.py`'s imports + the `pyproject.toml` `[project] name = "__pkg__"` field) because the scaffold cannot know the consumer's package slug at L3-template-authoring time. Without token substitution, every consumer must manually rename `src/__pkg__/` → `src/<their_slug>/` + edit `pyproject.toml` + grep-replace imports — three mechanical steps that should be automated. The brainstorm carry-forward table line 292 wrote `src/<pkg>/__init__.py` using metasyntactic `<...>` placeholders; the implementation chose the concrete sentinel `__pkg__` (valid Python identifier, double-underscore obvious-placeholder convention) which `/start-project` could `sed`-replace at copy time.
- **Source**: `~/.windsurf/templates/python-ml-uv/scaffold/{pyproject.toml, src/__pkg__/, tests/test_smoke.py, README.md}` (M2B.3 output — all `__pkg__` usages); `~/.codeium/windsurf/global_workflows/start-project.md` steps 3–7 (no substitution mechanism); `docs/prompts/stages/02-brainstorm-python-ml-uv.md` line 292 (brainstorm metasyntax `<pkg>`); README.md "Post-bootstrap rename" section (manual steps documented as workaround).
- **Proposed L1 changes** (re-evaluate at M2B.6 dry-run integration test OR at next `@sprint-review`):
  - **Option A — sed-substitution step in `/start-project` (recommended)**: Add a step between the current step 4 (apply `<type>/scaffold/`) and step 5 (copy `phases.yaml`) that `sed`-replaces a small set of well-known tokens. Initial token set: `__pkg__` → `<project-slug-snake-case>`, `__PKG__` → `<PROJECT_SLUG_SNAKE_UPPER>`, `__pkg-name__` → `<project-slug-kebab>`. Also rename any directory whose name contains a token. Token vocabulary lives in the `phase-taxonomy` contract or a sibling `template-tokens` contract for future extension.
  - **Option B — Jinja2 / cookiecutter-style templating**: Heavier; pulls in templating dependency. Defeats the file-based-scaffolds-are-auditable principle (ADR-004 alternatives considered). Rejected without a stronger trigger.
  - **Option C — leave manual; document in scaffold README only (status quo)**: Cheapest; but every consumer pays the rename tax. Deteriorates as the L3 template surface grows. Not recommended for any nontrivial template.
  - Weak recommendation: **Option A**, scoped to a small token vocabulary (~3 tokens). Routed via `@propose-extension` → `@update-horizontal` per ADR-017. The token convention should land as a contract update so multiple L3 templates can rely on it.
- **Severity**: Medium. Friction is per-consumer (one-time rename per bootstrap), but the manual step undermines the "bootstrap from a clean-slate `/start-project python-ml-uv <slug>` invocation" promise (brainstorm Mission line 17). Concretely: M2B.6 dry-run will produce a tree with literal `__pkg__` directory names, demonstrating the gap to the user without ambiguity.
- **Pairs with**: B11 follow-on (`/start-project` autodetect-latest-stable Python — also `/start-project`-side enhancement; both could ship as one bundled L1 enhancement covering "things `/start-project` should do at scaffold-copy time"). ADR-017 (intake routing). ADR-004 (`_shared/scaffold/` two-pass — token substitution would apply post-second-pass so type-specific files override _shared/ first, then both get substituted).
- **Trigger for promotion**: M2B.6 dry-run integration test will surface the gap concretely (the test will produce `python-ml-uv-test/src/__pkg__/...` literally). After that empirical demonstration, route via `@propose-extension`. Alternative trigger: explicit user request OR Vertical D's first `/start-project <thesis-name> python-ml-uv` invocation (which would then need to do the rename manually).
- **M2B.6 update (2026-05-07)**: M2B.6 dry-run validation surfaced a placeholder design defect — `__pkg__` is **invalid PEP 503** (`uv sync` failed: *"Names must start and end with a letter or digit and may only contain -, _, ., and alphanumeric characters"*). Refined the placeholder convention to a **dual-token** pattern that mirrors real PyPI projects' kebab/snake split:
  - `your-pkg` (kebab-case) — for `pyproject.toml [project] name` (PEP 503 valid)
  - `your_pkg` (snake_case) — for `src/your_pkg/` directory + Python imports + module docstrings
  Mirrors `scikit-learn` (PyPI) → `sklearn` (import) and similar real packages. Updated 11 files across scaffold + 2 L3 skills' references. Re-ran `uv sync` → succeeded; `make test` → 4/4 pass; `make lint` → clean; `make typecheck` → clean (after a separate fix; see next queue entry). The L1 enhancement proposal above stands, with the **token vocabulary refined**: `your-pkg` → `<project-slug-kebab>` (the user-supplied `<name>` arg from `/start-project`); `your_pkg` → `<project-slug-snake>` (kebab→snake conversion: `s/-/_/g`). Trigger now satisfied; route via `@propose-extension` at next `@sprint-review`.

---

## RESOLVED — 2026-09-05 — cascade-system — Vertical E (M4E.1 brainstorm) — client-repo placement guidance for `/start-project`

**Resolution date**: 2026-09-18 (M4A.2, PR for #115 — ADR-038)
**Decision**: RESOLVED — `/start-project` input 4 (GitHub org / owner) now prints the hint *"client work → a free organisation (transferable repos, collaborator/billing separation); never a second personal account (GitHub ToS allows one free personal account per person)"*; input 3 records that there is no `--no-remote` mode (brainstorm N7). The `@handover` transfer checklist is L3 work for `nextjs-marketing-site` (M4E.4b, lazy-authored when Vertical F reaches `handover`) — not part of this resolution.
**Original entry text**:

- **Insight**: the user believed multiple private repos were unavailable on his GitHub account and considered one GitHub account per client. Verified facts: GitHub Free (personal and organisation) includes unlimited private repos with unlimited collaborators (since 2020-04-14); the Terms of Service forbid more than one free personal account per person; org renames redirect repo URLs; user→org repo transfers keep issues/PRs/settings. Decision (brainstorm §S3): private repos under the personal account now, one free org after the business rename, transfer then.
- **Source**: docs.github.com "GitHub's plans", "GitHub Terms of Service" §Account Requirements, "Renaming an organization", "Transferring a repository" (all accessed 2026-09-05).
- **Proposed L1 change**: `/start-project` skill's owner prompt (input 4) gains a one-line hint: *"client work → a free organisation (transferable, collaborator/billing separation); never a second personal account (ToS)"*; `@handover` (L3) carries the transfer checklist. Small edit; bundle into ADR-038 apply (M4A.2) if convenient.
- **Trigger for promotion**: M4A.2, or the org creation after the rename.

---

## RESOLVED — 2026-09-05 — Devin rule-loading semantics for `description`-only rule files

**Resolution date**: 2026-09-06 (M4A.1, PR for #114)
**Decision**: RESOLVED — clause landed exactly as proposed. `docs/rules/l1-canonical-paths.md` gained a "Loading facts that shape authoring" section ("every rule file declares `trigger:` explicitly — a `description`-only file is agent-decided in Devin and always-on in nothing"); `@verify-l1` step 6 checks `trigger:` presence on every `docs/rules/*.md`, `_shared/scaffold/.devin/rules/*.md`, and `<type>/rules/*.md`; `@propose-extension` Q5 and `/start-project` step 6a repeat the clause at authoring / deploy time. Concise `global_rules.md` unchanged (cap).
**Original entry text**:

- **Insight**: portfolio-website's `.devin/rules/*.md` files carry only `description:` (no `trigger:`). Devin lists them as "available rules — read when relevant" (agent-decided), not as always-on. So the same file behaves differently across tools depending on whether `trigger:` is explicit. Rule authors should always set `trigger:` explicitly; `description`-only is an accidental `model_decision`.
- **Source**: observed in the 2026-09-05 Devin session (`<available_rules>` block listing 19 portfolio rules by description); Devin docs `extensibility/rules.mdx` §"Rule Activation Types".
- **Proposed L1 change**: one clause in `docs/rules/l1-canonical-paths.md` (long-form) or in `@write-skill`/`@propose-extension` rule-authoring steps: *"every rule file declares `trigger:` explicitly; `description`-only files are treated as agent-decided by Devin and always-on by nothing."* Also a `@verify-l1` check for rule files without `trigger:`. Not a new rule (cap).
- **Trigger for promotion**: ADR-037 apply (M4A.1) is the natural moment since `@verify-l1` is touched anyway; otherwise next horizontal `@sprint-review`.

---

## RESOLVED — Sprint 3 — Cascade C2 — M_C2.3 author (`@write-skill` step 9 inaccuracy — missing `docs/skills/INDEX.md` update)

**Resolution date**: 2026-09-06 (M4A.1, PR for #114)
**Decision**: RESOLVED — `@write-skill` step 9 now reads "If L1: add a row to `docs/skills/INDEX.md` (activation + concise description + canonical path) AND bump the skill count + add a row in `docs/cheat-sheet.md` §1". Trigger fired as predicted: M4A.1 added ten L1 skills and had to perform exactly the index updates step 9 claimed did not exist. While updating, `docs/skills/INDEX.md` was found to be missing four further skills (`begin`, `kickoff`, `handoff-to-coding-session`, `handoff-to-thinking-session`) — also fixed. The `@update-horizontal` propagation-table sub-question (a row for "new L1 skill") is folded into step 9 itself rather than a separate row; `@docs-refresh` remains the regenerator.
**Original entry text**:

- **Insight**: Authoring `@vault-distill` at M_C2.3 surfaced a drift in `@write-skill` SKILL.md step 9 ("Update indexes / cross-refs"). The step currently reads: *"If L1: no global index file yet (M1.11 may add one); no action needed"*. This is inaccurate — `docs/skills/INDEX.md` exists since Sprint 1.5.4 (PR #35) and `docs/cheat-sheet.md` has a skill count that must be bumped when a new L1 skill lands. During M_C2.3 authoring, both indexes had to be updated manually; the SKILL.md gave no instruction to do so. Future skill-authoring sessions (B, D, and subsequent cascade-system skills) will hit the same friction unless the step is corrected.
- **Source**: ADR-031 §Consequences (future-work row); `~/.codeium/windsurf/skills/write-skill/SKILL.md` step 9 (lines 168–173); `docs/skills/INDEX.md` (exists since Sprint 1.5.4); `docs/cheat-sheet.md` §1 Skills count.
- **Proposed L1 change** (re-evaluate at M_C2.5 retro or on next invocation of `@write-skill`):
  - Update `@write-skill` SKILL.md step 9 to: *"If L1: update `docs/skills/INDEX.md` (add row for new skill with activation + concise description + canonical path) AND `docs/cheat-sheet.md` §1 (bump skill count + add row)"*. Route via `@update-horizontal` per ADR-017.
  - Also consider: does `@update-horizontal` step 8 Propagate table need a matching row for "new L1 skill" specifying the two index updates? Currently only "Skill description / behavior change" is listed — adding a skill isn't the same as modifying one.
- **Severity**: Low. Friction is noticeable (manual index bumps) but not blocking — human authoring caught it. Higher severity if an automated flow ever invokes `@write-skill` and relies on its step 9 being complete.
- **Trigger for promotion**: Next invocation of `@write-skill` for a new L1 skill (after M_C2.3); OR M_C2.5 retro; OR any future `@sprint-review` processes a queue of skill-authoring friction and this is one of them.
