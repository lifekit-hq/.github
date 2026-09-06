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
- [ ] Dependency advisories + license allowlist (`pip-audit`, `npm audit` /
      dependency-review, or the toolchain's equivalent). Every ignored advisory carries
      a reason and a revisit condition in the config, never a bare ignore
- [ ] Secret scanning (gitleaks or equivalent) on every PR

## 7. Agent harness (in-repo, independent)

- [ ] `CLAUDE.md` at the root: what the repo is, how to run it, the mandatory gates,
      and these conventions restated repo-specifically. An agent landing in the repo
      cold must find everything it needs there
- [ ] Pre-commit hook (husky + lint-staged or equivalent) enforcing the same gates
      locally that CI enforces remotely — agents and humans hit the same wall
- [ ] Markdown at the repo root is `README.md`, `CLAUDE.md`, and — where devclaw
      manages the repo — its machine-maintained onboarding set (`AGENTS.md` pointer,
      `ARCHITECTURE.md`), which devclaw's onboarding writes at the root and its
      staleness signals watch there. `LICENSE` and the release-please-managed
      `CHANGELOG.md` also belong at the root; tool/config dotfiles are fine.
      Everything else: durable docs live in `docs/`, specs in `specs/` — session
      artifacts don't get files
- [ ] `SECURITY.md` at the root, with GitHub private vulnerability reporting enabled:
      state the boundary (what is and is not defended) and the reporting channel.
      Promise no response time nobody is on call for
- [ ] Machine-facing contracts (env vars, exit codes, event schemas, tool lists) each
      get a reference doc under `docs/reference/` pinned by a doc-sync test - a contract
      no test can see rots silently
- [ ] Runbooks are shaped as failure modes: for each, one distinguishing check and
      one action

## 8. README

The README is the product narrative, in this order. A section that does not apply is
omitted, never left as a stub:

- [ ] What it is, in one paragraph, then how to run it (install / quick start) - before
      any architecture
- [ ] Honest status: every capability claim carries its evidence tier where the claim
      is made - **production** (exercised live), **experimental** (built, not
      load-bearing), **paper** (specified, not built)
- [ ] What it deliberately excludes / is NOT - the non-goals, so scope creep by
      osmosis stays visible
- [ ] Docs map (a link to `docs/INDEX.md` or the list itself) and license
- [ ] No process history in the README (which issue closed what, incident war
      stories) - that lives in the changelog and the commits. No superlative a reader
      cannot verify from the repo
- [ ] A repo that executes untrusted code (agent output, third-party scripts) also
      ships `docs/threat-model.md`: what the boundary defends against, what it does
      not, and how the boundary is verified from inside

## Adoption

Amended 2026-09-06: supply-chain gates (6), `SECURITY.md` + reference docs + runbook
shape (7), and the README section (8) - borrowed after a code-level read of
[shleder/vetto](https://github.com/shleder/vetto)'s repo hygiene; each repo's adoption
issue below re-audits against them.

Audit a repo against the checklist, file one issue per repo titled
"Adopt the repo gold standard", fix the gaps in one or two PRs, link the issue here.
Divergence is allowed where a repo genuinely differs (e.g. no published packages →
no publish job) — note the divergence in the repo's CLAUDE.md rather than silently
skipping.

Per-repo adoption issues (filed 2026-08-25, implementation 2026-08-29;
[lifekit-common](https://github.com/lifekit-hq/lifekit-common) is the reference and
needs none):

- [devclaw#695](https://github.com/lifekit-hq/devclaw/issues/695)
- [finance-sentry#470](https://github.com/lifekit-hq/finance-sentry/issues/470)
- [lifekit-dashboard#84](https://github.com/lifekit-hq/lifekit-dashboard/issues/84)
- [lifekit-stack#130](https://github.com/lifekit-hq/lifekit-stack/issues/130)
- [career-kit#42](https://github.com/lifekit-hq/career-kit/issues/42)
- [lifekit#2](https://github.com/lifekit-hq/lifekit/issues/2)
- [lifekit-health#2](https://github.com/lifekit-hq/lifekit-health/issues/2)
- [openclaw-config#2](https://github.com/lifekit-hq/openclaw-config/issues/2)
