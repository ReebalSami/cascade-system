# ADR-038: `/start-project` scaffold-time enhancements — token substitution, third-party Agent-Skills install, `--local-dir`

**Status**: Proposed (apply at M4A.2, after ADR-037; flip to Accepted when `/start-project --dry-run python-ml-uv-test python-ml-uv` is still green and a `nextjs-marketing-site` dry-run tree contains substituted names + populated `.agents/skills/`)

**Date**: 2026-09-05

**Source**: `docs/prompts/stages/02-brainstorm-nextjs-marketing-site.md` §C2, §A3, §S2; `queue/pending-review.md` entries "M2B.3 (`/start-project` token substitution for `<pkg>` placeholder)" + its M2B.6 update

**Context**:

`/start-project` (ADR-014/016; re-homed as a skill by ADR-037) copies `_shared/scaffold/` then `<type>/scaffold/` with plain `cp -R` and no substitution. The python-ml-uv vertical hit this at M2B.3/M2B.6: the scaffold ships `your-pkg` / `your_pkg` placeholders and every consumer renames by hand (README "Post-bootstrap rename"). The queue entry recorded the refined dual-token convention and a weak recommendation for a `sed`-substitution step in `/start-project`, with trigger "route via `@propose-extension` at next `@sprint-review`". The `nextjs-marketing-site` template (M4E.x) needs the same for `package.json` `"name"`, `config/site.yaml`, and README — two templates now demand it.

Two further scaffold-time needs surfaced in the M4E.1 brainstorm:

- **Third-party Agent Skills per project.** Research (2026-09-05) shows the frontend expert community ships agent guidance as installable skills — `vercel-labs/agent-skills` (`vercel-react-best-practices`, `web-design-guidelines`), the official `shadcn` skill, `anthropics/skills` `frontend-design` — installed with `npx skills add <owner/repo>` into `.agents/skills/`. A template should declare these as dependencies rather than re-author them (they track upstream; our skills carry procedures). `adapt-from-all` says "vendor into L1 — never runtime-import"; installing with `--copy` into the consumer repo *is* vendoring (files are tracked), but the rule's spirit targets L1 authoring, so the exception is documented here.
- **Non-default local directory.** saeb-group's website lives at `~/Projects/saeb-group/website/` next to a sibling tool; `/start-project` only supports `<parent>/<name>`.

**Decision**:

Extend the `/start-project` skill (post-ADR-037 form) with four scaffold-time behaviours, applied at M4A.2 in one PR:

1. **Token substitution** as a new step between "apply `<type>/scaffold/`" and "copy `phases.yaml`": replace, in file contents and in file/directory names, `your-site` → `<slug-kebab>`, `your_site` → `<slug-snake>` (`s/-/_/g`), `your-pkg` → `<slug-kebab>`, `your_pkg` → `<slug-snake>`. Vocabulary is fixed and documented in `~/.windsurf/contracts/phase-taxonomy.md` §"Template tokens" (additive, no version bump); templates opt in by using the tokens. Implementation: `find` + `sed -i ''` (macOS) over text files only, single-line commands per `no-terminal-oneline-scripts`; directory renames via `mv`. `--dry-run` performs substitution in the temp tree so the result is inspectable.
2. **Third-party skill install** from `~/.windsurf/templates/<type>/skills.yaml` (list of `{source: <owner/repo>, skills: [<name>, …]}`), executed after scaffold copy as `npx skills add <source> --skill <name> --copy -y` (one command per entry) into `<project>/.agents/skills/`. Tracked in git. **Skipped in `--dry-run`** (network) with a printed note. License files that ship with each skill are kept; the skill's `SKILL.md` provenance is not edited. Absence of `skills.yaml` = no-op (python-ml-uv unaffected).
3. **`--local-dir <absolute-path>`**: overrides `<parent>/<name>` as the checkout directory while `<name>` stays the repo/Project name. Existing-directory handling is unchanged (prompt: abort or merge-into-existing).
4. **Private repo default reaffirmed** (already `--private`); no `--no-remote` mode (brainstorm N7 — it would stall the chain at `issues`).

Regression gate: `/start-project --dry-run python-ml-uv-test python-ml-uv` still produces a tree where `uv sync && make test && make lint && make typecheck` pass and no literal `your_pkg` / `your-pkg` remains.

**Alternatives considered**:

- **Jinja2 / cookiecutter templating** — heavier; pulls in a templating dependency; defeats the auditable-file-scaffolds principle (ADR-004 alternatives). Rejected (same as the queue entry's Option B).
- **Leave renames manual, document in README** — status quo; every consumer pays the rename tax; deteriorates as templates multiply. Rejected (queue Option C).
- **Vendor the third-party skills into the L3 template `skills/`** — literal `adapt-from-all` compliance, but they would freeze at vendoring time and drift from upstream (`vercel/next.js` moved its skills into the framework repo precisely to keep them version-matched). Rejected in favour of per-project install + `skills.yaml` pins.
- **Install third-party skills globally (`~/.agents/skills/`)** — one copy for all projects, but a client repo would then depend on the developer's machine state; a future maintainer/agent needs them in the repo. Rejected.
- **`--no-remote` escape hatch** — requested as "no repos for now"; rejected because `@to-issues`, `@release-manager`, CI, and backup all need the remote; private repos are invisible and free (GitHub Free: unlimited private repos). Re-openable by explicit request.

**Consequences**:

- Templates become self-describing: tokens + `skills.yaml` are the two declarative extension points `/start-project` honours; no per-template logic in the skill.
- The M2B.3/M2B.6 queue entry closes on apply (both templates consume the tokens).
- `nextjs-marketing-site` consumers get `.agents/skills/{vercel-react-best-practices, web-design-guidelines, shadcn, frontend-design}` plus the template's own L3 skills in one directory.
- `--dry-run` output differs from a real bootstrap only by the absence of installed third-party skills (printed).
- A one-line note in `docs/rules/adapt-from-all.md` records the consumer-project dependency exception (long-form only; the concise `global_rules.md` text is unchanged — cap).

**Source refs**:

- `docs/prompts/stages/02-brainstorm-nextjs-marketing-site.md` (M4E.1) — §C2, §A3, §S2, §Carry-forward M4A.2
- `queue/pending-review.md` — "Sprint 2 — Cascade B — M2B.3 (`/start-project` token substitution)" + "M2B.6 update (2026-05-07)"
- `docs/prompts/stages/02-brainstorm-python-ml-uv.md` §B11 follow-on (autodetect-latest-stable Python — related `/start-project` enhancement, **not** bundled here)
- ADR-004 (two-pass scaffold), ADR-037 (skill form of `/start-project`)
- Research pass 1 (2026-09-05): vercel-labs/skills CLI docs (`npx skills add`, `--copy`, `--skill`, `-y`), vercel-labs/agent-skills README, shadcn skill, anthropics/skills
