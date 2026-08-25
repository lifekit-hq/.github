# lifekit Repo Gold Standard

The one definition of what a lifekit-hq repository looks like. **Reference
implementation: [lifekit-common](https://github.com/lifekit-hq/lifekit-common)** —
when this document and that repo disagree, fix one of them, never fork the standard
silently. Each repo carries its own harness (CLAUDE.md, hooks, CI) so it works
standalone; this document is what those per-repo copies must agree on.

## 1. Repo metadata

- [ ] One-sentence description that says what the repo *is*, not what it hopes to be
- [ ] Topics set (stack + domain)
- [ ] **Squash merges only**; merge commits and rebase-merge disabled
- [ ] Delete branch on merge
- [ ] Main effectively protected: all changes land via PR with CI green — including agent work

## 2. Labels

- [ ] `P1` (firm: sized, acceptance criteria) / `P2` (named, unsized fog)
- [ ] `needs-refinement` — fog that needs slicing before it can be sized
- [ ] `devclaw-ready` — sized, criteria firm, dispatchable to an agent
- [ ] Area labels as the repo needs them; no priority prefixes in titles

## 3. Planning

- [ ] Issue titles: imperative sentence, no priority prefix (priority = label)
- [ ] P1 issues: traceability first (destination / why this exists), then acceptance
      criteria, then shape (N PRs — never time estimates). P2/P3 stay one-liners
- [ ] Milestones: `M<n> — <outcome>` — named for the outcome, never a date
- [ ] Every issue traces to a destination; a backlog without a destination is a junk drawer

## 4. Git

- [ ] Branches: `<type>/<issue#>-<slug>` (conventional-commit type), via `gh issue develop`
- [ ] Conventional commits; scope = module/package/spec (`feat(ui): …`, `fix(041): …`) —
      release-please parses these into the CHANGELOG
- [ ] PR body: what + why, then a **Validation** section stating exactly what was run and green

## 5. Releases

- [ ] release-please maintains the release PR: version bump (lockstep across packages
      where the repo publishes several) + generated `CHANGELOG.md`
- [ ] Weekly Release cron (Mondays 08:00 UTC) merges the pending release PR;
      `workflow_dispatch` = "release now". GITHUB_TOKEN merges don't fire push
      workflows — the cron job must dispatch the tag/publish workflow explicitly
- [ ] Publishing (npm to GitHub Packages, images to GHCR, deploys) hangs off the
      release-created event, never off ad-hoc pushes

## 6. CI gates

Every PR runs the repo's full gate set unconditionally — no soft-fail steps:

- [ ] Lint (zero errors, zero warnings policy where the toolchain supports it)
- [ ] Format check (Prettier/formatter from the shared `@lifekit-hq/config` presets
      where the stack allows)
- [ ] Tests (with coverage floors once a suite exists — ratchet, never lower)
- [ ] Build (all shippable artifacts, including docs/catalog builds like Storybook)

## 7. Agent harness (in-repo, independent)

- [ ] `CLAUDE.md` at the root: what the repo is, how to run it, the mandatory gates,
      and these conventions restated repo-specifically. An agent landing in the repo
      cold must find everything it needs there
- [ ] Pre-commit hook (husky + lint-staged or equivalent) enforcing the same gates
      locally that CI enforces remotely — agents and humans hit the same wall
- [ ] Only `README.md` and `CLAUDE.md` at the repo root; durable docs live in `docs/`,
      specs in `specs/` — session artifacts don't get files

## Adoption

Audit a repo against the checklist, file one issue per repo titled
"Adopt the repo gold standard", fix the gaps in one or two PRs, link the issue here.
Divergence is allowed where a repo genuinely differs (e.g. no published packages →
no publish job) — note the divergence in the repo's CLAUDE.md rather than silently
skipping.
