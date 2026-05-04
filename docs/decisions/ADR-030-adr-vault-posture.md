# ADR-030: ADR ↔ vault posture — Posture B (unified discovery via project-namespaced summary cards)

**Status**: Accepted
**Date**: 2026-05-04
**Milestone**: [M_C2.2](https://github.com/ReebalSami/cascade-system/issues/64) — Sprint 3 Vertical C2 (vault write path)
**PRD**: `docs/prompts/stages/m_c2-1-prd.md` §7 + §11 Slice 2 (Posture B + Shape C + lazy-backfill locked)
**Brainstorm**: `docs/prompts/stages/m_c2-1-brainstorm.md` Q2 + Q2a + Q2b (design tree walked, alternatives eliminated)
**Gap analysis**: `~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` §Q6 (three-posture enumeration; this ADR closes the gap)
**Handoff**: `docs/handoffs/cascade-c2-vault-write-path.md` §4 M_C2.2 AC

## Context

Sprint 2 Vertical C shipped the vault READ path (ADR-022 Obsidian CLI selection + ADR-023 vault layout v3 + ADR-024 `@vault-research` skill + ADR-025/026/027/028 integration edits). The READ path deliberately scoped-out one architectural question: **how does cascade-system's own knowledge — ADRs, retros, queue — relate to the vault?** The gap was surfaced in `post-m2c6-strategic-gap-analysis-f832e8.md` §Q6 as a latent-but-not-bleeding problem: "works fine today; becomes noise later when the vault has content and two independent knowledge graphs drift".

Concretely, when M_C2.4 distills the 4 Karpathy clippings at `raw/clippings/` into `wiki/concepts/` (first real `wiki/` content), those concept cards will want to cite ADRs — at minimum ADR-022 / ADR-023 / ADR-024 (the READ-path foundations). Without a posture decision, the options are:

- Ugly repo-relative paths inside `wiki/` markdown (`../../../cascade-system/docs/decisions/ADR-023-...`) — breaks when `wiki/` content is copied anywhere
- Plain URLs to the repo — no Obsidian native wikilink resolution, no backlinks graph
- Duplicate ADR bodies inside the vault — violates `_meta/AGENTS.md` §6 one-canonical-home rule

Vertical C2's M_C2.1 decision sprint (brainstorm Q2 + Q2a + Q2b → PRD §7) walked the full design tree and locked the architecture. This ADR ratifies the decision, codifies the card shape, and ships with a self-referential dogfood card as the first real `wiki/` write.

**Pre-decision state**:

- 29 ADRs exist at `cascade-system/docs/decisions/` (ADR-001 through ADR-029; this one is ADR-030).
- `wiki/sources/` has sub-stubs but zero content.
- `wiki/mocs/MOC - home.md` is a 134-byte stub.
- No ADR of any project has been carded into the vault.
- `_meta/AGENTS.md` §5 defines `wiki/sources` frontmatter fields (`source_url`, `source_author`, `source_date`, `extracted_concepts`, `extracted_entities`) but no `source_type: adr` subtype.

## Decision

**Adopt Posture B — unified discovery via project-namespaced summary cards.**

The canonical home of every ADR stays at `<repo>/docs/decisions/ADR-NNN-<slug>.md` per ADR-003 (strict docs structure) and AGENTS.md §6 one-canonical-home rule. The vault adds **summary cards** — one per ADR — at `wiki/sources/adrs/<project-slug>/` with typed links back to the canonical file plus `[[wikilinks]]` to extracted concepts/entities.

### Card location + basename (Shape C)

- **Path**: `wiki/sources/adrs/<project-slug>/<project-slug>_adr-<NNN>-<kebab-slug>.md`
- **Example**: `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md`
- **Wikilink basename**: `[[<project-slug>_adr-<NNN>-<kebab-slug>]]` (self-identifying; survives paste-and-carry + file moves)
- **Separator**: `_` between project slug and `adr-NNN` (matches existing vault convention — date-slug separator `2026-05-04_<title>.md` already uses `_`)

### `source_type: adr` frontmatter spec

Extends `_meta/AGENTS.md` §5 `wiki/sources` subtype family. Every ADR card carries:

```yaml
---
type: source
source_type: adr
project: <project-slug>            # e.g., cascade-system | portfolio-website | thesis
adr_id: "<NNN>"                    # zero-padded string for sort stability (e.g., "030" not 30)
title: "<ADR title>"
date: YYYY-MM-DD                   # ADR authoring date
source_path: <repo-relative path>  # e.g., cascade-system/docs/decisions/ADR-030-adr-vault-posture.md
status: <Accepted|Proposed|Superseded by ADR-MMM|Deprecated>
supersedes: []                     # wikilink basenames of superseded ADR cards (if any)
superseded_by: null                # wikilink basename if this card's ADR is later superseded
linked_adrs: []                    # cross-project ADR citations as [[wikilinks]]
extracted_concepts: []             # wiki/concepts/<domain>/<sub>/*.md backrefs
extracted_entities: []             # wiki/entities/<type>/*.md backrefs
tags: [source, adr, <project-slug>]
---
```

### Backfill policy: forward-only + lazy-backfill + debt-trigger

- **Forward-only**: from ADR-030 onward, every new ADR ratification PR ships its vault card (filesystem-only edit; vault is not git-versioned per ADR-023). Card authoring becomes part of the ratification ritual.
- **Lazy-backfill of ADR-001 through ADR-029**: no upfront effort. Backfill happens when a `wiki/` card wants to cite a specific older ADR — the citing PR backfills the ADR card as a prerequisite edit. The graph grows where used.
- **Debt-trigger escalation**: if un-carded-ADR-citation backlog crosses ~10 ADRs across open `wiki/` work, the authoring Cascade appends a `## [YYYY-MM-DD] vault-backfill-debt | <project>` entry to `cascade-system/queue/pending-review.md` listing the pending ADR citations + their intended wikilink targets. `@sync-github` drains at the next milestone close, or `@sprint-review` promotes a batch-backfill PR.
- **Unresolved-wikilink tolerance**: Obsidian renders unresolved `[[wikilinks]]` gracefully (marked "unresolved", no breakage). A card may ship with `linked_adrs: [[[cascade-system_adr-023-vault-layout-v3]]]` pointing at an un-yet-created card; the link activates when the target card is backfilled.

### Scope

- **This ADR applies to ADR cards only** — generalized project-namespacing across all `wiki/sources/<type>/` subtypes (papers, courses, voice memos, READMEs) is explicitly deferred per PRD §10 O19. A future ADR cites ADR-030 as precedent when the second source type hits the same pressure.
- **All projects participate** the moment they ship their first ADR card — `cascade-system/`, future Vertical B (python-ml-uv L3 template), future Vertical D (thesis), and any further projects each get a `<project-slug>/` sub-directory as they need it.

## Alternatives considered

### Posture choice (three candidates from gap analysis §Q6)

| Posture | Shape | Verdict |
|---|---|---|
| **A — Separation** (current default) | cascade-system `docs/decisions/` canonical; vault never references ADRs | Rejected. Works while `wiki/` is empty; becomes noise/drift as `wiki/` fills. Forces ugly relative paths or URL citations inside `wiki/` markdown. The very *next* vertical (M_C2.4 Karpathy clippings → concepts) will cite ADR-022/023/024. |
| **B — Unified discovery** (adopted) | `docs/decisions/` canonical + `wiki/sources/adrs/<project>/` summary cards with `[[wikilinks]]` + `source_path:` back-pointer | **Adopted**. Preserves AGENTS.md §6 one-canonical-home rule while giving the vault reachability. Enables `@vault-research` composite ranking (ADR-024) to surface ADR cards alongside other source cards. Clean dogfood: ADR-030 ratifies the posture and ADR-030's own card is the first instance. |
| **C — Vault-first** | ADRs authored inside vault; exported to `docs/decisions/` for git history | Rejected. Highest tooling cost (bidirectional sync). Breaks `document-as-you-go` rule's expectation that ADRs live in the repo (git is the audit trail per `@release-manager` / ADR-018). Treating vault as source of truth when only one Cascade writes it is over-engineering. |

### Card-shape choice (three candidates from brainstorm Q2a)

| # | Shape | Path + Wikilink | Verdict |
|---|---|---|---|
| A | Subdirectory only | `wiki/sources/adrs/cascade-system/adr-023-vault-layout-v3.md` → `[[adr-023-vault-layout-v3]]` | Rejected. User constraint was *"categorize somehow to which project, also in the names"* — Shape A puts project in the path but NOT in the basename. Pasted wikilink loses project scope; file move orphans the identifier. |
| B | Filename prefix, flat dir | `wiki/sources/adrs/cascade-system_adr-023-vault-layout-v3.md` → `[[cascade-system_adr-023-vault-layout-v3]]` | Rejected. Basename carries project scope (good) but Obsidian tree-view conflates all projects into one flat list (bad for navigation as projects multiply). |
| **C — Both** (adopted) | `wiki/sources/adrs/cascade-system/cascade-system_adr-023-vault-layout-v3.md` → `[[cascade-system_adr-023-vault-layout-v3]]` | **Adopted**. Belt-and-suspenders: project scope encoded in both the structural path AND the lexical basename. Pasted wikilink is self-identifying; file move stays unambiguous; Obsidian tree-view groups by project sub-directory. Separator `_` matches existing vault practice. |

### Backfill-scope choice (three candidates from brainstorm Q2b)

| # | Shape | Cards in ADR-030's PR | Verdict |
|---|---|---|---|
| **1 — Forward-only + lazy** (adopted) | 1 (ADR-030 itself) | **Adopted**. ADR-030's atomic change is *"declare posture"*. Backfilling 15 or 30 cards bundles declaration with implementation — two logical changes, violating the `@update-horizontal` bundle anti-pattern. Obsidian handles unresolved wikilinks gracefully. Usage-driven backfill (next paragraph) is more honest than theoretical cross-cutting taxonomy. |
| 2 — Immediate full backfill | 30 (030 + 001–029) | Rejected. Bundle-anti-pattern strain + 30-file review surface + bundles two logical changes. |
| 3 — Immediate selective backfill (~15 cross-cutting) | 16 | Rejected. Requires upfront cross-cutting-vs-feature taxonomy, itself a sub-decision distracting from the posture decision. |

### Separator choice (`_` vs `-`)

ADR-card basenames use `_` between the project slug and the `adr-NNN-<slug>` portion. Rejected alternative: `-` separator. Reason: ADR slugs themselves contain `-` (e.g., `adr-023-vault-layout-v3`); using `-` as the project/ADR boundary creates parsing ambiguity (*"is `cascade-system-adr` the project, or is `cascade` the project and `system-adr` part of the ADR slug?"*). `_` resolves the ambiguity and matches existing vault convention.

## Consequences

### Enables

- **`wiki/sources/adrs/<project>/` becomes a navigable subgraph** — each card wikilinks to extracted concepts + entities + related ADRs. The ADR corpus stops being a flat filesystem list and becomes a connected knowledge graph.
- **Cross-project ADR discovery** — thesis-project ADRs, future portfolio-website ADRs, cascade-system ADRs all live under one `wiki/sources/adrs/` tree with namespacing; `@vault-research` composite ranking (ADR-024) surfaces ADR cards alongside other source cards using the same structural signal (recency × tag-overlap × MOC-distance).
- **Clean citations in `wiki/` markdown** — `[[cascade-system_adr-023-vault-layout-v3]]` instead of `../../../cascade-system/docs/decisions/ADR-023-vault-layout-v3.md`. Works in Obsidian render, survives export to any platform with basic wikilink resolution.
- **`@to-prd` §13 Sources integration** (per ADR-026) now has a concrete target for ADR citations — `[[cascade-system_adr-NNN-<slug>]]` is the canonical citation form for any PRD that references an ADR.
- **Lazy-backfill keeps the entry bar low** — no 30-file upfront backfill blocks this ADR from landing. The graph grows where used.

### Constrains

- **Every new ADR ratification** (post this one) includes a card-creation step in the PR lifecycle. The card is a filesystem-only edit (vault is not git-versioned per ADR-023); the PR description acknowledges the outside-repo edit per ADR-008 M-2 trade-off.
- **ADR supersession** (per ADR-011) must update both the canonical file AND the vault card's `superseded_by` field. Missing the card update = stale graph. Future retros should flag this.
- **Debt-trigger monitoring** — the `~10 un-carded ADR` threshold is a shape, not a count (per `no-quantity-over-shape`). Calibrate empirically as usage accumulates.
- **`_meta/AGENTS.md` §5 gets a new subtype** (`source_type: adr`) — schema growth requires a co-evolution signal per AGENTS.md §13. Applied in this ADR's filesystem edits.
- **`_meta/.preview/` is reserved** for `@vault-distill` tempdir previews (per PRD §7 architecture concern 4). Documented in AGENTS.md §2 routing table as transient / dot-prefix-ignorable / auto-GC-after-7-days. No other use.

### Dogfood

**First real `wiki/` content**: `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md` — this ADR's own summary card, authored alongside this file. Demonstrates the namespacing pattern self-referentially. Filesystem-only edit; lands adjacent to this PR, not inside it.

### `_meta/AGENTS.md` extensions (filesystem-only edits, adjacent to this PR)

Two updates to the vault schema — ship alongside this ADR but land outside the PR (vault-not-git-versioned per ADR-023 §Consequences):

1. **§2 routing table**: add row for `_meta/.preview/` — transient tempdir for `@vault-distill` previews, dot-prefix-ignorable, auto-GC after 7 days, never hand-authored content.
2. **§5 frontmatter contract**: add `wiki/sources` sub-subtype `source_type: adr` with the full spec above (13 fields).

These are the minimum AGENTS.md changes required to honor this ADR without breaking AGENTS.md §13 co-evolution discipline.

## Sources consulted

Per `adapt-from-all` rule:

- **ADR-022** (Obsidian CLI selection) — the CLI this posture assumes for vault mutations. `obsidian files path=wiki/sources/adrs/<project>` is how cards are verified to exist; the running-app-index access model (ADR-022 §4) is what makes vault ops reliable under iCloud symlink.
- **ADR-023** (Vault layout v3) — AGENTS.md §6 one-canonical-home rule, §2 routing convention, `wiki/sources/` subtree, §5 frontmatter contract. This ADR extends §2 + §5 minimally.
- **ADR-024** (`@vault-research` skill) — the READ bookend. ADR-030's schema enables ADR cards to be surfaced by `@vault-research`'s composite ranking (recency × tag-overlap × MOC-distance). Without this posture, `@vault-research` cannot rank ADRs.
- **ADR-025, ADR-026, ADR-027** (integration edits) — `@to-prd` §13 Sources wikilink discipline now has a concrete ADR citation target.
- **`~/.windsurf/plans/post-m2c6-strategic-gap-analysis-f832e8.md` §Q6** — three-posture enumeration; this ADR resolves the gap.
- **`docs/prompts/stages/m_c2-1-brainstorm.md`** Q2 (posture choice) + Q2a (card shape) + Q2b (backfill scope) — approved brainstorm walked the design tree and locked every sub-decision; this ADR ratifies.
- **`docs/prompts/stages/m_c2-1-prd.md`** §7 (solution overview) + §11 Slice 2 (PR shape) — approved PRD specifies the frontmatter spec + path + AGENTS.md extensions.
- **Karpathy LLM wiki gist** (`https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f`) — `wiki/sources/` as "one card per ingested source" is Karpathy's type-first ontology. ADR card is a subtype specialization of Karpathy's source pattern.
- **4 Karpathy clippings** at `~/Projects/obsidian/second-brain/raw/clippings/2026-05-04_*.md` — canonical clipping (self-evolving Claude-Code memory via Karpathy's LLM wiki) + 3 derivatives. Browsed during brainstorm; direct influence on Posture B rationale (why unified discovery matters once `wiki/` has content).
- **`refs/superpowers/skills/`** — browsed. No direct prior art for ADR-vault-card pattern; document-structure skills gave general shape conventions.
- **`refs/claude-skills/engineering/`** — browsed. No direct prior art; reinforced "one ADR per logical change" anti-bundling discipline.

## Related ADRs

- **ADR-003** (strict docs structure) — `docs/decisions/` canonical home preserved; vault card is a non-canonical summary, not a new home.
- **ADR-009** (NNN reserve-in-INDEX-first) — followed: ADR-030 row reserved in `INDEX.md` before this file was authored.
- **ADR-011** (supersession over deletion) — card supersession uses back-pointer pattern (`superseded_by:`); plain delete forbidden. Same discipline as the canonical ADRs.
- **ADR-017** (`@propose-extension` intake channel) — this ADR was authored as part of the M_C2.2 execution scoped by M_C2.1's `@to-prd`-approved PRD; qualifies as a planned extension within the chartered vertical, so routing is through the vertical's milestone lifecycle rather than a fresh `@propose-extension` session.
- **ADR-018** (release-discipline cluster) — this ADR lands via `@release-manager` single-PR shape: `/branch-start docs/c2-adr-030` → INDEX reserve + ADR body + ADR-023 back-link → `/commit` → `/branch-push-and-pr` → `/ci-watch` → `/branch-merge-and-cleanup`.
- **ADR-022** (Obsidian CLI selection) — vault interaction mechanism; no change required in ADR-022 itself.
- **ADR-023** (Vault layout v3) — `_meta/AGENTS.md` §2 + §5 extensions ship adjacent to this ADR. §Deferred list updated with a back-link to this ADR (the "ADR ↔ vault relationship" latent item, identified in gap analysis §Q6 after ADR-023 authoring, is now resolved).
- **ADR-024** (`@vault-research` skill) — consumes ADR cards as a new `source_type` in its composite ranking. No change required in ADR-024 itself; extends organically via the shared schema.
- **ADR-026** (`@to-prd` vault sources integration) — §13 Sources wikilinks now have a concrete target for ADR citations; no ADR-026 change required, just usage.

## Notes

- **Why no separate PR for the vault mutations**: per ADR-023 §Consequences, the vault is not git-versioned. The dogfood card at `wiki/sources/adrs/cascade-system/cascade-system_adr-030-adr-vault-posture.md` and the `_meta/AGENTS.md` §2 + §5 extensions are filesystem-only edits. The PR description acknowledges them per ADR-008 M-2 trade-off; outside-repo state reconstruction depends on vault backup (user-directive per ADR-023).
- **Why not bundle ADR-030 with the `@vault-distill` SKILL.md** (M_C2.3): per the `@update-horizontal` bundle anti-pattern — one ADR per logical change. ADR-030 = *"declare posture"*. M_C2.3 = *"ship the WRITE skill"*. Two logical changes, two PRs. Discipline held despite the temptation to combine.
- **Relationship to `@vault-distill`'s ADR-card generation** (M_C2.3): the skill's distillation engine produces ADR cards in the shape defined here. ADR-030 provides the *schema*; the skill provides the *mechanism*. Schema-first + mechanism-second lets the skill reference a stable target rather than inventing shape inline.
- **First-mover dogfood risk**: the dogfood card is ADR-030's own summary card, which must be authored BEFORE ADR-030 is merged (so the card's `source_path` reference is valid the moment the merge happens). Handled by authoring the card in this same execution sequence; the minor temporal inversion (card references not-yet-merged ADR) stabilizes immediately on merge.
- **Cross-project coordination**: no active cross-project demand at ratification time (only `cascade-system/` exists as a project with ADRs). Vertical B (python-ml-uv L3 template) and Vertical D (thesis) will gain their own `wiki/sources/adrs/<project>/` sub-directories as they ratify their first ADRs. Namespacing is ready day-1 per G4.
