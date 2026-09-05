# `nextjs-marketing-site` L3 template — Brainstorm

**Phase**: brainstorm (M4E.1)
**Phase set**: cascade-system meta-repo verticals (no `phases.yaml`; M4A.x horizontal prelude + M4E.x milestones = phase sequence)
**Status**: Approved (user approved the design + implementation plan on 2026-09-05 after a whole-picture review pass; grill ran in four waves inside a Devin CLI plan-mode session)
**Date**: 2026-09-05
**Milestone**: [M4E.1 — Brainstorm `nextjs-marketing-site` design space](https://github.com/ReebalSami/cascade-system/issues/112)
**Handoff**: authored at M4E.8 (`docs/handoffs/cascade-f-loom-remodelers.md`, `cascade-g-saeb-group-website.md`, `cascade-h-own-services-site.md`)
**Parent plan**: `~/.devin/plans/plan-d4643c2d1ce68383.md` (Devin plan-mode file; §6 lists the review corrections folded into this artifact)
**Grill artifact**: this file
**Next phase**: M4A.1 — ADR-037 Devin runtime adoption cluster (horizontal prerequisite), then M4E.2 — `phases.yaml`

---

## Mission

Author a **reusable L3 template for multilingual marketing / lead-generation websites** (`nextjs-marketing-site`) so that three concrete sites — the developer's own services site (Aetherwerk, being renamed), a cleaning company in the Netherlands (saeb-group), and a remodeling/repair company in the USA (loom-remodelers) — bootstrap from one `/start-project <slug> nextjs-marketing-site` invocation, run one shared 9-phase lifecycle, and inherit the frontend discipline proven by portfolio-website. The template is Devin-first (Windsurf-compatible), host-agnostic, and deliberately under-opinionated on the two contested per-client choices (hosting-account ownership, CMS).

## Scope

This brainstorm decides the **design space** for `nextjs-marketing-site` and the **horizontal prerequisites** the platform migration (Windsurf → Devin CLI) forces before any L3 work can run. The template ships at `~/.windsurf/templates/nextjs-marketing-site/`:

- `phases.yaml` — phase chain (W1; authored at M4E.2)
- `scaffold/` — type-specific bootstrap files (W2, W4, W6, W7, W8, A4; authored at M4E.3)
- `skills.yaml` — third-party skill sets installed at bootstrap (A3; consumed by ADR-038's `/start-project`)
- `skills/` — L3 skills (A2; authored at M4E.4a/M4E.4b)
- `rules/` — L3 rules (A1; authored at M4E.5)

Horizontal prerequisites (approved as part of this brainstorm, authored as ADR-037 + ADR-038, applied at M4A.1–M4A.3) are **not** template content; they live at L1.

Cross-cutting assets (`docs/` structure, `strict-docs-placement`) stay in `_shared/scaffold/` per ADR-004.

## Goals

- **G1** — Generic across the lead-gen brochure archetype. Acceptance test: would this bootstrap a US single-locale trades site, a NL/EN cleaning-company site, AND a DE/EN consulting site? All three must pass.
- **G2** — Works in Devin CLI end-to-end (bootstrap, phases, commits, PRs) while staying readable by Windsurf where that costs nothing.
- **G3** — Content-first client workflow: client inputs, approved copy, and account handover are explicit phases with gates, not afterthoughts.
- **G4** — Under-opinionated on contested per-client tooling: hosting account ownership, hosting target, CMS, analytics provider are decided per site at the phase where the information exists (`discovery` / `launch`), never baked in.
- **G5** — Compliance and conversion baseline built in: per-country legal slots, WCAG 2.2 AA, cookie-less analytics default, click-to-call / WhatsApp / quote-form patterns, local-SEO structured data.
- **G6** — Framework knowledge comes from version-matched sources (installed `node_modules/next/dist/docs/`, managed `AGENTS.md`, `context7`), never from authored skill prose that goes stale.
- **G7** — Small always-on surface: `AGENTS.md` ≤ ~40 lines; L3 rules are the six that encode real invariants; everything procedural is an on-demand skill.
- **G8** — Maintenance burden small: caret pins, lockfile generated at bootstrap, latest-stable pins refreshed only when a major ships.

## Non-goals (v1, explicit out-of-scope)

- **N1** — CMS / client self-editing UI. Content lives in `config/site.yaml` + `src/messages/*.json` (+ MDX if needed). Keystatic → Sanity is the documented upgrade path, chosen per client if asked (W5).
- **N2** — Blog / MDX pipeline, chatbot, auth, payments, database. Neither client asked; portfolio-website is the reference implementation when a consumer wants a blog later.
- **N3** — A generic `nextjs-app` template (CMS/DB apps à la HafenHut). Stays deferred until a project demands it (cheat-sheet §5 discipline).
- **N4** — Baking in a hosting provider. The build is `output: "standalone"`; Cloudflare Workers + OpenNext and Vercel Pro are documented targets, chosen at `launch` (W3).
- **N5** — Shipping `ui-ux-pro-max` into client repos. It becomes an L1 global skill (developer's design engine; 704K third-party data without a LICENSE file).
- **N6** — Promoting `ghost-writing` to an L1 always-on rule. `global_rules.md` is at 5995/6000 chars; the voice guidance lives in `@site-content` references instead.
- **N7** — `--no-remote` bootstrap. It would stall every site at the `issues` phase; private repos at bootstrap are the recommendation (S3). Re-openable as a small ADR-038 addition if the user still wants zero remotes for a period.
- **N8** — Devin plugins as the L3 vehicle. Closed beta; the plugin layout (`skills/`, `rules/`, `AGENTS.md`) matches the L3 overlay shape and is a future direction, not a v1 dependency.

## Constraints

- **C1 — Generic acceptance test.** Every decision must pass *"would `nextjs-marketing-site` bootstrap loom (US, `en`), saeb (NL, `nl`+`en`), and the own site (DE, `de`+`en`)?"* — verified per question in the acceptance table.
- **C2 — Client-agnostic template.** No client names, brands, or copy in template files. Placeholders use the `your-site` / `your_site` token pair (mirrors python-ml-uv's `your-pkg` / `your_pkg`).
- **C3 — Devin runtime facts** (verified in the on-disk Devin docs, `/Applications/Devin.app/.../share/devin/docs/`): workflows in `~/.codeium/<channel>/global_workflows/` and `.windsurf/workflows/` are **not** imported; skills are slash commands (`/name`); `triggers: [user]` = manual-only; `.devin/rules/*.md` honours `trigger:` frontmatter; project skills are read from `.agents/skills/`, `.devin/skills/`, `.windsurf/skills/`; global skills from `~/.codeium/windsurf/skills/`, `~/.config/devin/skills/`, `~/.agents/skills/`; `~/.codeium/windsurf/memories/global_rules.md` is loaded.
- **C4 — Devin-first, Windsurf-compatible** (F1). Nothing Windsurf-only is added; global L1 skills stay at `~/.codeium/windsurf/skills/` (both tools read it). Known trade-off: project-level `.agents/skills/` is not read by Windsurf.
- **C5 — `_shared/` boundary (ADR-004).** Only type-specific files in `nextjs-marketing-site/scaffold/`; type-specific `AGENTS.md` overrides the shared one (documented last-wins).
- **C6 — L1 canonical paths (ADR-014).** Skills at `~/.windsurf/templates/nextjs-marketing-site/skills/<name>/SKILL.md`; rules at `.../rules/<name>.md`. Consumer-side deploy targets change to `.devin/rules/` + `.agents/skills/` via ADR-037.
- **C7 — Phase-taxonomy contract v1.** `phases.yaml` validates (unique names, acyclic `next`, one terminal). Per-project location moves to `.devin/phases.yaml` with legacy fallback (ADR-037; location, not field → no version bump).
- **C8 — `global_rules.md` cap.** 5995/6000 chars. No new always-on L1 rule anywhere in this work; the `l1-canonical-paths` rewrite must pass `wc -m` ≤ 6000.
- **C9 — Release discipline (ADR-018).** Every `main`-bound change via `@release-manager`; separate PRs per milestone unless artifacts live outside the repo (M2B.4 bundling precedent).
- **C10 — Bootstrap paradox.** Until ADR-037 lands, `@release-manager` / `/commit` run in Devin by reading the workflow markdown and following it manually.

## Approaches considered

Resolution order followed the dependency chain: F1 → F2 → F3 → F4 (frame) → W1 … W8 (template design) → A1 … A4 (artifacts) → S1 … S4 (sequencing). F2–F4 and W2–W8 were resolved by four parallel research passes (Provenance) rather than by asking; the user decided F1, W1 (chain confirmation), W3-ownership, W5, S1, S3, and approved the L1-mutation bundle.

### F1 — Runtime target

| # | Shape | Trade-offs |
|---|---|---|
| **A** | Devin-first, Windsurf-compatible | `.devin/` in scaffolds; skills at paths both tools read; nothing Windsurf-only added |
| **B** | Devin CLI only | Move globals to `~/.config/devin/skills/`; breaks Windsurf sessions |
| **C** | Both, full parity | Maintain workflows for Windsurf and skills for Devin — two surfaces, highest drift risk |

**Chosen**: **A** (user). Evidence: this session runs in Devin; HORUS and portfolio-website already renamed `.windsurf/` → `.devin/`; the L1 surface at `~/.codeium/windsurf/` is read by both tools.

### F2 — Workflow-layer gap

| # | Shape | Trade-offs |
|---|---|---|
| **A** | Convert all workflows → skills (`triggers: [user]`, same `/name`); old files → redirect stubs | Single content home; identical invocation in Devin; Windsurf `/name` keeps working via stub |
| **B** | Thin `run-workflow` wrapper skill | Cheapest; awkward UX (`/run-workflow start-project`); keeps two mental models |
| **C** | Defer; bootstrap by hand this round | Fastest start; loses `/commit` crash safety and branch discipline exactly when three repos start |

**Chosen**: **A**. Rationale: the ecosystem already converged — Claude Code merged `commands/` into skills (v2.1.3, 2026-01-09), Cursor retired `.cursor/commands/` with `/migrate-to-skills`, and the Agent Skills standard (`agentskills.io`) is supported by Devin, Claude, Cursor, Codex, Copilot. `triggers: [user]` + ADR-006 `activation: manual` preserve ADR-015's "manual-only deterministic procedure" semantics; descriptions are written narrowly so Windsurf never auto-activates them. No Devin built-in slash command collides with `/commit`, `/review`, `/start-project`, `/run-phase`, etc. → ADR-037.

### F3 — Deliverable shape

| # | Shape | Trade-offs |
|---|---|---|
| **A** | One L3 template + own-site retrofit | Reuse across three consumers; python-ml-uv precedent |
| **B** | L3 template + copy-in L2 kit for existing sites | Two artifacts to maintain |
| **C** | Per-project L2 only | Fast today; triplicates 19 rules / 26 skills; no reuse |

**Chosen**: **A**. Three consumers is exactly the trigger the cheat-sheet §5 row set ("defer until first project demands it"); the community equivalent is "starter + installed skills".

### F4 — Archetype + slug

| # | Shape | Trade-offs |
|---|---|---|
| **A** | `nextjs-marketing-site` (lead-gen brochure) | Honest, narrow scope: hero / services / proof / contact / legal, i18n, local SEO, lead capture |
| **B** | Generic `nextjs-app` (contract §8 sketch) | Forces CMS / DB / auth decisions none of the three sites need |
| **C** | Both now | Second template has no consumer (violates defer-until-demanded) |

**Chosen**: **A**. Research: experts start marketing sites from `create-next-app` + shadcn and prune SaaS boilerplate; `next-forge` is explicitly overkill; marketing-site starters are a distinct category; HafenHut-style CMS/DB apps are a different archetype.

### W1 — Phase chain

| # | Shape | Trade-offs |
|---|---|---|
| **A** | `discovery → brainstorm → spec → issues → design → content → build → launch → handover` | Each client approval (inputs, copy, accounts) is a gate; content-first is agency practice; `wireframe` folds into `design` |
| **B** | Fold `content` into `build` (per-slice copy) | Components built on placeholder text; no copy-approval gate |
| **C** | Fold `handover` into `launch` | Mixes deploy work with the client-action checklist |

**Chosen**: **A** (user). `build` maps to `@build-slice` (L3), **not** `@tdd`: a UI slice = component + i18n keys + config + demo gate; TDD applies to logic inside slices via Vitest. Own site closes `discovery` (self-interview) and `handover` trivially. Milestones: `inputs-collected`, `design-tree-resolved`, `prd-approved`, `backlog-populated`, `design-system-approved`, `content-approved`, `site-built`, `live`, `handed-over`.

### W2 — Stack pins

Verified against the installed Next.js docs (`node_modules/next/dist/docs/01-app/02-guides/upgrading/version-16.md`) and current official pages:

| Decision | Chosen | Why |
|---|---|---|
| Next.js | latest stable at M4E.3 authoring (16.3+ expected; verify via `context7`) | `proxy.ts` replaces `middleware.ts` (Node runtime only); Turbopack default; async `params`; 16.3 adds `next/root-params` + auto-upsert of the managed `AGENTS.md` block |
| Output | `output: "standalone"` | Contact-form route handler needs a server; static export drops route handlers, proxy, image loader |
| Cache Components / React Compiler | off | Mostly-static YAML-driven site; opt-in later |
| Styling | Tailwind 4 CSS-first, `@theme inline`, semantic tokens in `:root`/`.dark`, brand scalars from `config/site.yaml` | One template → three brands without per-brand config files |
| Components | shadcn `base-nova` (Base UI default since 2026-07) | `render` prop, never `asChild`; `nativeButton={false}` when rendering non-buttons |
| i18n | next-intl 4 (`routing/request/navigation`, `[locale]`, `generateStaticParams`, `localePrefix: 'always'`) | Static rendering; hreflang; `next/root-params` once on 16.3+ |
| Motion | `motion` (`motion/react`) | `framer-motion` was renamed; v12 API unchanged; `<MotionConfig reducedMotion="user">` |
| Validation / config | `zod/v4` + `yaml`, Zod-validated `config/site.yaml` | portfolio + aetherwerk precedent (`config-yaml-first`) |
| Tooling | pnpm (latest stable, `packageManager` pinned) + Makefile; ESLint 9 flat + Prettier; Vitest 4 + RTL; Playwright + `@axe-core/playwright`; `knip`; Lighthouse CI | Biome rejected: loses `eslint-config-next` rules; async Server Components need Playwright, not Vitest |

### W3 — Hosting + ownership

| # | Shape | Trade-offs |
|---|---|---|
| **A** | Host-agnostic `standalone`; documented targets Cloudflare Workers + `@opennextjs/cloudflare` and Vercel Pro; choice at `launch` | Mirrors python-ml-uv's tracker-agnostic stance; both targets verified |
| **B** | Bake in Cloudflare | Cheapest ($0–5/mo; Turnstile, Web Analytics, Images, DNS bundled) but `proxy.ts` support is experimental (1.20.3, `nodejs_compat`) |
| **C** | Bake in Vercel Pro | First-party, zero adapter risk, $20/mo per team; **Hobby is prohibited for any commercial site, including the own consulting site** (Fair Use Guidelines) |
| **D** | Reuse portfolio's AWS CDK | Ops-heavy; awkward handover to non-technical clients |

**Chosen**: **A**, with Cloudflare as the documented default for client sites (proxy-free root-redirect routing is the Cloudflare path) and Vercel Pro as fallback; AWS CDK documented as a third option for own sites only. **Ownership**: client-owned hosting account + domain + billing by default (developer is a member, removed at handover); dev-managed hosting available per client under a maintenance arrangement (user).

### W4 — i18n posture

Always-on `[locale]` routing with locales from `config/site.yaml` (a single-locale site still uses `[locale]`; `en` only for loom), hreflang + `x-default`, RTL driven by config (default off), `i18n.formality` per locale (`u`/`je` for NL, `Sie` for DE) consumed by `@site-content`. Alternative (opt-in i18n) rejected: two code paths for one template.

### W5 — CMS / client editing

| # | Shape | Trade-offs |
|---|---|---|
| **A** | None in v1 (YAML/JSON/MDX in repo, edits via maintenance) | $0; Git-tracked, transcreation-friendly; Keystatic documented as upgrade |
| **B** | Keystatic in template | Git-based UI shipped everywhere; adds setup + auth per site |
| **C** | Sanity Free | Strongest editing UX; hosted content lake + GROQ dependency |

**Chosen**: **A** (user), with B/C as per-client options decided in `discovery`. Research: 5–10-page trades sites are rarely edited by the client.

### W6 — Lead capture

Resend (free tier 3,000/mo) + Cloudflare Turnstile (free, host-agnostic) + honeypot; sticky mobile `tel:` CTA (≥48 px targets); WhatsApp CTA for NL (98% penetration), phone/SMS primary for US; quote form 3–7 fields, multi-step when ≥6; TCPA consent checkbox when `legal.country = us` and `form.smsConsent` is on. Config keys: `cta.primary`, `contact.phone`, `contact.whatsapp`, `business.serviceAreas[]`, `form.*`. Alternatives (Formspree-style form backends, reCAPTCHA) rejected: vendor lock-in or weaker free tiers.

### W7 — SEO / legal / analytics baseline

- JSON-LD type from `business.schemaType` (`CleaningService` | `HomeAndConstructionBusiness` | trade subtypes), `Service`, `FAQPage`, `BreadcrumbList`; service + service-area pages; GBP embed by `place_id`; **no** self-serving review stars (Google review-snippet policy).
- `sitemap.ts` / `robots.ts` / `opengraph-image.tsx`; hreflang with `x-default`.
- Legal footer slots by `legal.country`: NL (KVK number, BTW-id, BW 3:15d colofon), DE (§5 DDG Impressum, two-click reachability), US (privacy policy, TCPA opt-in). WCAG 2.2 AA baseline (EAA micro-enterprise exemption noted; US ADA litigation trend makes AA the defensible floor).
- Analytics: `analytics.provider` default **`none`**; chosen at `launch` between Plausible (paid per account, host-agnostic) and Cloudflare Web Analytics (free on CF) — neither needs a consent banner in NL/DE (Autoriteit Persoonsgegevens names Plausible/Umami/Matomo explicitly); consent component only when GA4 / marketing pixels are configured.

### W8 — Quality gates

GitHub Actions on PR: lint → typecheck → test → build → axe → Lighthouse CI; platform git integration for preview deployments; `local-demo-before-push` as the human gate; `make` targets for every recurring task. Alternative (no CI, python-ml-uv B10=C) rejected: deploy pipelines and preview URLs are load-bearing for client review.

### A1 — L3 rules

**Ship six** (generalised from portfolio-website): `config-yaml-first`, `i18n-from-start` (locales from config, not "all 4"), `component-driven`, `use-pnpm-makefile`, `motion-reveal-once` (renamed from `scroll-reveal-once`; `motion` package), `local-demo-before-push` (UI flavour of the L1 artifact-review gate). **Dropped**: `skill-by-skill` (covered by `/run-phase` + issues), `github-project-management` (covered by `issue-project-assignment-required` + `@sync-github`). `clean-project-structure` text in the L3 copy points to `.devin/` + `.agents/`.

### A2 — L3 skills

**One skill per template-specific phase** (the B6 discipline): `@site-discovery` (+ `references/intake-questionnaire.md`), `@design-system` (composes `ui-ux-pro-max` + `frontend-design` + `web-design-guidelines` as review checklist), `@site-content` (brief → source copy with `references/voice-anti-ai-tells.md` distilled from `ghost-writing` → glossary → transcreate with `i18n.formality` → `transcreation-audit` → client gate → `messages/*.json`), `@build-slice` (generalised `new-skill` workflow: slice = component/section/page with i18n + config + demo gate), `@launch` (a11y/SEO/legal/perf audit → deploy to chosen target → DNS/email cut-over), `@handover` (accounts, secret rotation, runbook, access removal). Workflow-turned-skills copied in: `/new-component`, `/add-translation`, `/review`. **Lazy authoring**: `@site-content`, `@launch`, `@handover` are authored when Vertical F reaches those phases (contract-sanctioned forward references, as `@tdd` / `@writeup` in python-ml-uv).

### A3 — Third-party skills at bootstrap

Installed per project via `npx skills add --copy -y` into tracked `.agents/skills/` (skipped in `--dry-run`): `vercel-labs/agent-skills` (`vercel-react-best-practices`, `web-design-guidelines`), the official `shadcn` skill, `anthropics/skills` `frontend-design`. Licenses verified at M4E.3. Deliberate, documented exception to `adapt-from-all` (consumer-project dependency, not L1 authoring). Research: Vercel's eval (2026-01-27) shows a tiny always-on `AGENTS.md` beats skills for framework facts — so authored skills carry procedures, `AGENTS.md` + installed docs carry framework knowledge.

### A4 — Scaffold

`package.json` (`your-site` token, caret pins, `packageManager`), `.node-version`, `next.config.ts`, `tsconfig.json`, `eslint.config.mjs`, `.prettierrc`, `postcss.config.mjs`, `components.json` (base-nova), `src/app/[locale]/{layout,page,not-found}.tsx`, `src/app/page.tsx` (root redirect, proxy-free path), `src/app/{sitemap,robots}.ts`, `src/app/[locale]/opengraph-image.tsx`, `src/i18n/{routing,request,navigation}.ts`, `src/proxy.ts` (optional; removed for the Cloudflare target), `src/messages/en.json`, `config/site.yaml` (`site` / `business` / `contact` / `cta` / `legal` / `analytics` / `i18n` / `features`) + `src/lib/config.ts` + `src/types/config.ts`, `src/components/{ui,layout,sections,shared}/`, `src/app/api/contact/route.ts`, `Makefile`, `.env.example` (`RESEND_API_KEY`, `TURNSTILE_SECRET_KEY`, `NEXT_PUBLIC_TURNSTILE_SITE_KEY`, `CONTACT_TO_EMAIL`), `.gitignore` (+`client-input/`; `.devin/`, `.agents/`, `AGENTS.md` **tracked** — portfolio's gitignoring of them was a local hack), `tests/` (Vitest smoke, Playwright a11y), `.github/workflows/ci.yml`, `AGENTS.md` (Next.js managed block + ≤8 guardrails + skill pointers), `CLAUDE.md` (`@AGENTS.md`).

### S1 — Dogfood order

**loom (US) → saeb (NL) → own site** (user). loom exercises the single-locale path and the simplest legal case first; saeb exercises `nl`+`en`, KVK/BTW, hreflang; the own site is a retrofit (`git init` + `_shared`/template overlays without touching `src/`), entering at `brainstorm` because the rename is a repositioning. **The order only decides which site validates the bootstrap path first**: the three verticals are independent repos with independent `phases.yaml` copies and may run in parallel sessions once the template exists (user requirement, 2026-09-05); only the template itself (M4E.2–M4E.6) is a shared prerequisite.

### S2 — Client materials

`client-input/` gitignored in every consumer; sanitised `docs/prompts/stages/01-discovery.md` is the tracked artifact. saeb bootstraps with `--local-dir ~/Projects/saeb-group/website` (repo `saeb-group-website`); loom merges into its existing directory (logo → `client-input/`).

### S3 — GitHub placement

| Fact (verified 2026-09-05) | Consequence |
|---|---|
| GitHub Free — personal and organisation — includes unlimited private repos with unlimited collaborators | "I can't have multiple private repos" is outdated; private repos cost nothing |
| ToS: one person or legal entity may maintain no more than one free account | **No per-client GitHub accounts** |
| Org rename: repo URLs + remotes auto-redirect; repo transfer user→org keeps issues, PRs, settings, secrets; Projects v2 are owner-scoped | Create repos now, move later without loss |

**Chosen**: private repos at bootstrap under `ReebalSami`; one free organisation when the new business name is final; transfer the client repos then (re-link Project boards via `@sync-github`). The PM discipline — issues, milestones, Project v2, PRs, CI, backup — needs the remote from day one.

### S4 — Sessions

Per ADR-033/034: this session = brainstorm (thinking); ADR-037/038 apply + template authoring in coding sessions, one per milestone; `@handoff-to-coding-session` bundle written at the end of M4E.1.

## Acceptance test (C1 generic check)

| Decision | loom (US, `en`) | saeb (NL, `nl`+`en`) | own site (DE, `de`+`en`) | Pass |
|---|---|---|---|---|
| W1 chain | `discovery` = client intake; `handover` = client accounts | same | `discovery` = self-interview; `handover` trivial | ✓ |
| W2 stack | single-locale `[locale]/en` | two locales, `u`-formality | two locales, `Sie`-formality | ✓ |
| W3 hosting | Cloudflare in client account (or Vercel Pro) | same | Cloudflare or Vercel Pro (Hobby prohibited) or AWS CDK | ✓ |
| W4 i18n | `locales: [en]`, hreflang trivial | `[nl, en]`, `x-default` → `nl` | `[de, en]`, `x-default` → `de` | ✓ |
| W5 CMS | none unless asked | none unless asked | none | ✓ |
| W6 lead capture | phone/SMS primary, TCPA checkbox | WhatsApp + phone | phone + form | ✓ |
| W7 legal/SEO | privacy + TCPA; `HomeAndConstructionBusiness`; GBP | KVK + BTW-id + colofon; `CleaningService` | §5 DDG Impressum; `ProfessionalService` | ✓ |
| W7 analytics | chosen at launch, no banner | chosen at launch, no banner | chosen at launch, no banner | ✓ |
| A1 rules | apply unchanged | apply unchanged | apply unchanged | ✓ |
| A2 skills | all six phases real | all six phases real | discovery/handover trivial | ✓ |
| S3 GitHub | private repo, org transfer later | same | same (rename = config + repo rename) | ✓ |

✓ All decisions pass C1.

## Maintenance principles

- **Caret pins, lockfile at bootstrap**: `next`, `react`, `next-intl`, `motion`, `zod`, dev tools use `^` lower bounds set to latest stable at M4E.3 (verified via `context7`); `pnpm-lock.yaml` is generated by `/start-project`, never shipped.
- **Template refresh only on majors**: Next 17, Tailwind 5, next-intl 5, or a shadcn base change trigger a chore PR; minors ride on `^`.
- **Installed docs over authored prose**: skills never restate Next.js API facts; they point to `node_modules/next/dist/docs/` and `context7`.
- **Third-party skill refresh**: `npx skills add` re-run per project when the upstream sets change; `skills.yaml` pins the sources.
- **Rule count is shape, not target**: a seventh L3 rule needs a new invariant that no skill can carry.

## Carry-forward to downstream milestones

| Milestone | Carries from M4E.1 |
|---|---|
| **M4A.1** (ADR-037 apply) | F2 → 10 workflows → skills (`triggers: [user]`, `activation: manual`, `argument-hint`; redirect stubs); C3/C6/C7 → 19-file `<project>/.windsurf/` → `.devin/` + `.agents/skills/` sweep with legacy fallback in readers; step 6a copies only the 6 `model_decision` rules; `_shared/scaffold/AGENTS.md` + `CLAUDE.md`; ADR-006/014/015/016 amendments; `l1-canonical-paths` rewrite (`wc -m` ≤ 6000); cheat-sheet / manual / `@verify-l1`. |
| **M4A.2** (ADR-038 apply) | C2 + A3 → token substitution (`your-site`/`your_site`; python-ml-uv `your-pkg` too), `npx skills add --copy -y` from `<type>/skills.yaml`, `--local-dir`; regression: python-ml-uv dry-run still green. |
| **M4E.2** (`phases.yaml`) | W1 → 9 phases mapped: `discovery` → `@site-discovery`, `brainstorm` → `@grill-me`, `spec` → `@to-prd`, `issues` → `@to-issues`, `design` → `@design-system`, `content` → `@site-content`, `build` → `@build-slice`, `launch` → `@launch`, `handover` → `@handover`; milestones per W1; artifacts `docs/prompts/stages/01-discovery.md` … `docs/handover.md`. |
| **M4E.3** (scaffold) | W2, W4, W6, W7, W8, A4 → file list above; `skills.yaml`; `pnpm install && make lint && make typecheck && make test && make build` green in a dry-run tree; third-party licences verified. |
| **M4E.4a** (skills, first wave) | A2 → `@site-discovery`, `@design-system`, `@build-slice` via `@write-skill`; `ui-ux-pro-max` → L1 (`~/.codeium/windsurf/skills/`, provenance + licence check in frontmatter); copy-ins `/new-component`, `/add-translation`, `/review` (adapted: `motion`, locales-from-config). |
| **M4E.5** (rules) | A1 → six rules at `~/.windsurf/templates/nextjs-marketing-site/rules/`. |
| **M4E.6** (dry-run) | `/start-project --dry-run` from a fresh Devin session; `.agents/skills/` populated; `phases.yaml` validates; a11y + Lighthouse smoke. |
| **M4E.7** (retro) | Short per-vertical retro `retros/sprint-4-vertical-e.md`; each F/W/A/S decision verified or revised. |
| **M4E.8** (handoffs) | `docs/handoffs/cascade-f-loom-remodelers.md`, `cascade-g-saeb-group-website.md`, `cascade-h-own-services-site.md` — modelled on `cascade-b-template-python-ml-uv.md`; no client content pre-loaded. |
| **Vertical F** | `@site-content`, `@launch`, `@handover` authored just-in-time (M4E.4b) when loom reaches those phases. |
| **M4A.3** (after F launches) | `glossary-brandbook` + `transcreation-audit` → `~/.codeium/windsurf/skills/` via `@propose-extension` L1-mutation route. |

## Open / deferred items

- **`.agents/skills/` Windsurf visibility** — project-level skills are not read by Windsurf; revisit if Windsurf adopts the Agent Skills project path.
- **OpenNext-Cloudflare `proxy.ts`** — experimental since `@opennextjs/cloudflare` 1.20.3; the template documents the proxy-free path; promote proxy usage on Cloudflare when support is marked stable.
- **`--no-remote`** — not built (N7); reopen only on explicit user request.
- **`@tdd` L1 promotion** — unchanged; this template does not consume `@tdd`.
- **Devin plugins** — closed beta; when public, evaluate packaging L3 overlays as a plugin (N8).
- **Rule-copy duplication** — ADR-037 fixes `/start-project` step 6a; existing consumers that copied 15 `always_on` long-forms into `.devin/rules/` (HORUS historically, portfolio) are not swept here — queue entry filed.
- **Business rename** — org creation + repo transfer scheduled after the name is final (S3).

## Next phase

**M4A.1 — Apply ADR-037** (horizontal prerequisite, coding session): convert the 10 workflows, sweep the 19 path references, fix step 6a, add `_shared` `AGENTS.md`/`CLAUDE.md`, rewrite the concise `l1-canonical-paths` rule within cap, update cheat-sheet/manual/`@verify-l1`. Verification: `devin skills list` shows the re-homed skills; a fresh Devin session runs `/commit` and `/start-project --dry-run python-ml-uv-test python-ml-uv`; Windsurf stubs resolve; dead-path grep finds zero `<project>/.windsurf/` in active L1 files.

Then **M4E.2 — `phases.yaml`** per the carry-forward table. Transitions are user-explicit per `@grill-me` step 9.

## Provenance

- **Grill session**: Devin CLI plan-mode session, 2026-09-05 — four waves (frame; template design; artifacts + L1 bundle; sequencing + GitHub), then a whole-picture review pass whose ten corrections are folded into this artifact (`~/.devin/plans/plan-d4643c2d1ce68383.md` §6).
- **Predecessor shape**: `docs/prompts/stages/02-brainstorm-python-ml-uv.md` (M2B.1) — adapted for shape; `docs/handoffs/cascade-b-template-python-ml-uv.md` — precedent for M4E.8.
- **Related ADRs cited during grill**: ADR-004 (`_shared/scaffold/`), ADR-006 (SKILL.md schema), ADR-008 (clean tree), ADR-009 (reserve-in-INDEX), ADR-011 (supersession), ADR-013 (`/commit`), ADR-014/016 (paths), ADR-015 (invocation syntax), ADR-017 (intake), ADR-018 (release cluster), ADR-032 (branch protection), ADR-033 (token economy), ADR-034 (model selection), ADR-036 (issue → Project).
- **Platform sources (on disk)**: Devin CLI docs at `/Applications/Devin.app/Contents/Resources/app/extensions/windsurf/devin/share/devin/docs/` — `reference/configuration/read-config-from.mdx` (workflows not imported), `extensibility/skills/{overview,creating-skills}.mdx`, `extensibility/rules.mdx`, `extensibility/plugins/overview.mdx`, `reference/commands.mdx`; installed Next.js docs at `portfolio-website/node_modules/next/dist/docs/` (`02-guides/ai-agents.md`, `upgrading/version-16.md`, `static-exports.md`, `json-ld.md`, `03-file-conventions/proxy.md`).
- **Research passes (four parallel subagents, 2026-09-05; sources dated in their reports)**: (1) agent-skills ecosystem — agentskills.io spec, vercel-labs/skills + agent-skills, Claude Code v2.1.3 release notes, Cursor skills docs, Vercel "AGENTS.md outperforms skills" (2026-01-27), Next.js 16.3 blog; (2) Next 16 architecture — installed docs, next-intl routing docs + `next/root-params` post, Tailwind v4 theme docs, shadcn Tailwind-v4 / Base-UI changelog, motion.dev upgrade guide, Playwright a11y docs, Lighthouse CI; (3) hosting/handover — Vercel Fair Use Guidelines + Pro plan + project transfer docs, Cloudflare Workers Next.js guide + pricing + Turnstile/Images/Web Analytics, Netlify/Render/Railway/Fly/Amplify/SST pricing pages, GitHub transfer docs, Google Workspace / M365 MX docs, Resend/Postmark/SES pricing; (4) lead-gen/legal/CMS — 2026 CRO guides for home-service sites, schema.org `LocalBusiness` subtypes, Google review-snippet policy, Autoriteit Persoonsgegevens cookie guidance, KVK/BW 3:15d, §5 DDG, EAA micro-enterprise exemption, US ADA litigation stats, state privacy thresholds, TCPA, Plausible/Umami/Vercel/Cloudflare analytics pricing, Keystatic/Tina/Payload/Sanity/Storyblok/Contentful tiers, hreflang guides, NL/DE formality.
- **Direct verifications**: `@opennextjs/cloudflare` PR #1309 / release 1.20.3 (Node.js middleware = `proxy.ts`, experimental, `nodejs_compat`); GitHub docs "GitHub's plans" + Terms of Service (one free account) + "Renaming an organization" + "Transferring a repository".
- **Local state read**: `docs/cheat-sheet.md`, `docs/decisions/INDEX.md`, `queue/pending-review.md` (L3-trigger entries), `~/.windsurf/templates/{_shared,python-ml-uv}/`, `~/.windsurf/contracts/phase-taxonomy.md`, `~/.codeium/windsurf/{skills,global_workflows}/`, portfolio-website `.devin/{rules,skills,workflows}`, `~/.claude/skills/{glossary-brandbook,transcreation-audit}`, `~/Projects/{aetherwerk,loom-remodelers,saeb-group}/`, HafenHut `package.json`.
- **User context**: solo developer, Hamburg; Devin CLI primary, Windsurf still installed; three sites to build next; services business being renamed; GitHub personal account `ReebalSami` only (plus `neuefische` org).
