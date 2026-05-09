# ADR-032: `/start-project` step 11 branch-protection `gh api` schema fix

**Status**: Proposed
**Date**: 2026-05-08
**Plan**: `~/.windsurf/plans/sprint-review-cascade-d-mid-vertical-691e32.md` (Cascade D mid-vertical capture)
**Source retro**: `~/Projects/cascade-system/retros/cascade-d-mid-vertical-capture.md` §triage row M2D.0
**Source queue entry**: `2026-05-07 — cascade-system — Cascade D — M2D.0 (`/start-project` step 11 `gh api` schema bug)` (`queue/pending-review.md` line 230 pre-drain)
**Related**: ADR-018 (release-discipline cluster — branch protection rationale); `~/Projects/cascade-system/docs/rules/no-terminal-oneline-scripts.md` (file-based pattern compliance)
**Issue**: tracked in retro; no separate GitHub issue (mechanical workflow fix)

## Context

`/start-project` step 11 (`~/.codeium/windsurf/global_workflows/start-project.md` lines 146–158) enables branch protection on a freshly-bootstrapped repo's `main` branch. The current implementation:

```sh
GITHUB_TOKEN= GH_TOKEN= gh api -X PUT repos/<owner>/<name>/branches/main/protection \
  -f required_pull_request_reviews.required_approving_review_count=1 \
  -f enforce_admins=false
```

**Observed failure** (M2D.0 HORUS bootstrap, 2026-05-07): HTTP 422 *"Invalid request / not all subschemas matched"*.

**Two root causes** (both surfaced during M2D.0 debug):

1. **String-typed values** — `gh api -f` sends every value as a string. `enforce_admins=false` is sent as the string `"false"`, not the boolean `false`. GitHub's branch-protection schema strictly requires typed boolean.
2. **Missing required fields** — GitHub's `PUT /repos/{owner}/{repo}/branches/{branch}/protection` schema requires **all four** top-level fields present, even if null:
   - `required_status_checks` (object or null)
   - `enforce_admins` (boolean)
   - `required_pull_request_reviews` (object or null)
   - `restrictions` (object or null)

   Omitting any produces 422.

The current invocation supplies only 2 of 4 fields and types them all as strings — both errors.

**Workaround used at M2D.0**: write a JSON payload to `/tmp/branch-protection-<name>.json`, then `gh api -X PUT ... --input <file>`. This:

- Carries proper JSON types (booleans stay booleans, nulls stay nulls)
- Carries all 4 required fields
- Complies with `no-terminal-oneline-scripts` (file-write + single-line `gh api` command; no embedded newlines in shell args)
- Worked correctly: HTTP 200, branch protection confirmed active

The fix is mechanical and applies to every future `/start-project` bootstrap.

## Decision

**Replace the `-f` chain in `/start-project` step 11 with the `--input <tmpfile>` JSON-payload pattern.**

### Edit 1 — Step 11 invocation

Replace lines 146–158 of `~/.codeium/windsurf/global_workflows/start-project.md` with the file-based pattern:

```sh
# Write payload to tmpfile (use write_to_file from Cascade or shell heredoc-equivalent file)
cat > /tmp/branch-protection-<name>.json <<JSON
{
  "required_status_checks": null,
  "enforce_admins": false,
  "required_pull_request_reviews": {
    "required_approving_review_count": 1
  },
  "restrictions": null
}
JSON

GITHUB_TOKEN= GH_TOKEN= gh api -X PUT \
  repos/<owner>/<name>/branches/main/protection \
  --input /tmp/branch-protection-<name>.json

rm /tmp/branch-protection-<name>.json
```

**Note on the heredoc**: a shell heredoc inside a single `cat > file <<MARKER` invocation is **safe** under `no-terminal-oneline-scripts` because the rule's hazard is *embedded newlines inside quoted shell arguments* — heredocs are a different shell construct that the macOS Windsurf integrated terminal handles correctly. Alternatively, use Cascade's `write_to_file` to author the JSON, then run a single-line `gh api ... --input` command with no heredoc — both paths are crash-safe.

### Edit 2 — Failure-handling table row update

Update the existing row 11 in step 11's failure table (`/start-project` lines 240–248):

| Step | Failure | Recovery |
|---|---|---|
| 11 | Branch-protection 403 (free-tier private) | Skip-with-warning per ADR-018; continue. |
| 11 | Branch-protection **422** (schema validation) | **Should not occur post-ADR-032**. If it does, surface the `gh api` error verbatim — likely a GitHub schema change requiring payload update. |
| 11 | Branch-protection other failure | Surface the `gh api` error; continue (agent-side rule remains load-bearing). |

### Edit 3 — Provenance bullet

Add to `~/.codeium/windsurf/global_workflows/start-project.md` Provenance section:

- M2D.0 HORUS bootstrap (2026-05-07) — empirical evidence the original schema failed; `--input <file>` workaround proven.

## Consequences

**Positive**:

- Branch protection now applies on every `/start-project` invocation (currently silently fails for every bootstrap).
- Server-side branch protection adds defence-in-depth beyond `branch-and-pr-required` rule (the agent-side forcing function).
- File-based pattern remains `no-terminal-oneline-scripts`-compliant.

**Negative / risks**:

- Adds a `/tmp/` artifact per invocation — mitigated by explicit `rm` after the `gh api` call (or by using `mktemp` for guaranteed-unique names if multiple bootstraps run concurrently — concurrent `/start-project` is a non-real scenario but the pattern costs nothing).
- Anyone debugging step 11 needs to know to inspect the payload, not just the `gh api` flags. Mitigated by the explicit `rm` placement (payload exists only during the `gh api` call).

**Neutral**:

- The 4-field JSON payload is GitHub's documented schema; no novel API usage. Stable.

## Alternatives considered

### Alt 1: Convert `-f` to `-F` flags (typed values)

`gh api -F enforce_admins=false` does send a proper boolean. But this still doesn't address root cause #2 (missing required fields). The schema requires all 4 — adding `-F required_status_checks=null -F restrictions=null` would chain extra flags but `null` via `-F` is unreliable (gh CLI treats it as the string `"null"` in some versions). File-based JSON is the canonical pattern.

**Rejected** — half-fix.

### Alt 2: Ignore the failure (accept silent skip)

Branch protection is "nice to have"; agent-side `branch-and-pr-required` rule is the load-bearing forcing function. Could leave step 11 broken with a "skip on 422" handler.

**Rejected** — `make-sure-it-works` rule. The fix is small; silent skip undermines defence-in-depth claim.

### Alt 3: Defer to a separate `/configure-branch-protection` workflow

Carve protection out of `/start-project` into its own workflow invoked optionally.

**Rejected** — every project should have protection (per ADR-018); making it optional weakens the discipline. The bootstrap step should just work.

## Apply via

`@update-horizontal` (per ADR-017 intake routing). Edits are scoped to:

- `~/.codeium/windsurf/global_workflows/start-project.md` (steps 11 + failure table + Provenance)
- `~/Projects/cascade-system/docs/decisions/INDEX.md` (flip ADR-032 row to `Status: Accepted` post-merge)

The fix lands at next `@update-horizontal` invocation by Cascade A or whoever next owns L1 changes. No upstream rebase needed; affects no consumer until next `/start-project` invocation.

## Provenance

Sources consulted:

- `queue/pending-review.md:230` (M2D.0 queue entry, 2026-05-07)
- `~/.codeium/windsurf/global_workflows/start-project.md` lines 146–158 (current broken invocation)
- GitHub REST API docs `PUT /repos/{owner}/{repo}/branches/{branch}/protection` (4-field schema requirement)
- `gh api --help` (`-f` vs `-F` vs `--input` flag semantics)
- `~/Projects/cascade-system/docs/rules/no-terminal-oneline-scripts.md` (heredoc safety + file-based pattern)
- ADR-018 §"Branch-protection on `main`" (rationale for protection in the first place)

Adapted for: `/start-project` workflow context, `no-terminal-oneline-scripts` compliance, ADR-018's "branch protection enforces only on Pro+ private OR public repos" caveat (preserved unchanged in this ADR — the schema fix is independent of the 403 free-tier-private skip path).
