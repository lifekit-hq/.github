# lifekit Repo Gold Standard

The one definition of what a lifekit-hq repository looks like. **Reference
implementation: [lifekit-common](https://github.com/lifekit-hq/lifekit-common)** —
when this document and that repo disagree, fix one of them, never fork the standard
silently. Each repo carries its own harness (CLAUDE.md, hooks) so it works
standalone; CI calls pinned reusable workflows from
[lifekit-hq/.github](https://github.com/lifekit-hq/.github). This document is what
those per-repo copies must agree on.

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
      where the repo publishes several) + generated `CHANGELOG.md`, via the
      `release-please.yml` reusable workflow pinned from
      [lifekit-hq/.github](https://github.com/lifekit-hq/.github)
- [ ] Weekly Release cron (Mondays 08:00 UTC) merges the pending release PR, via the
      `weekly-release.yml` reusable workflow; `workflow_dispatch` = "release now".
      Both templates authenticate as a release bot GitHub App (a short-lived
      installation token minted per run), never `GITHUB_TOKEN` — a `GITHUB_TOKEN`
      push fires no workflows, so a release PR pushed with it never gets the
      required checks and can never merge. An app-token merge is a real push, so
      release-please's own tag/publish trigger fires without an explicit dispatch
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
- [ ] Secret scanning (gitleaks or equivalent) on every PR — the `secrets-scan.yml`
      reusable workflow where the toolchain allows it

**CI conventions:**

- [ ] Concurrency is the caller's job — every workflow file that runs on `push` or
      `pull_request` carries:
      ```yaml
      concurrency:
        group: ${{ github.workflow }}-${{ github.ref }}
        cancel-in-progress: ${{ github.event_name == 'pull_request' }}
      ```
- [ ] Dependency caching uses the toolchain's built-in flag where one exists
      (`cache: npm` for `actions/setup-node`, `cache: pip` for
      `actions/setup-python`); .NET/NuGet caches explicitly:
      ```yaml
      - uses: actions/cache@v4
        with:
          path: ~/.nuget/packages
          key: nuget-${{ runner.os }}-${{ hashFiles('**/*.csproj', '**/Directory.Packages.props') }}
          restore-keys: nuget-${{ runner.os }}-
      ```
- [ ] Docker builds: copy manifests before sources, never rely on cache-mount
      contents across `RUN` steps, and write the gha buildx cache from the default
      branch only
- [ ] Public-repo PR jobs run on hosted runners (`ubuntu-latest`); a private caller
      passes its own runner labels through the template's `runner` input — never
      hardcode private runner labels as a template default
- [ ] The repo setting "Allow GitHub Actions to create and approve pull requests"
      is irrelevant once release automation runs on the app token, not
      `GITHUB_TOKEN`

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

## 9. Platform contract

Every repo that ships a running service meets the same platform contract, so the
platform (owned by `lifekit-stack`, by no product) can run, observe, and secure it
without knowing what it does. Conformance is checked in the stack's deploy, not by
convention.

- [ ] Health and readiness endpoints
- [ ] `/metrics` in Prometheus format
- [ ] JSON logs carrying the trace id
- [ ] OTLP traces, with one `traceparent` propagated end to end
- [ ] Sits behind the edge: TLS and identity are the edge's job, and the service trusts
      the forwarded OIDC identity instead of running its own login. Authorization stays
      the service's own
- [ ] Owns its topics: events it publishes are on topics it owns, and events are the only
      cross-product data path - never another product's database or tables
- [ ] Runs alone: its own repo, image, database, CI and release cadence, needing only a
      stub identity provider. Shared code arrives only as versioned `lifekit-common`
      packages, and the consumer chooses when to bump

## 10. Module rule (products)

Inside a product, modules follow the same separation as products do:

- [ ] Each module owns its schema
- [ ] Cross-module reads go through read ports, never into another module's tables
- [ ] Extractability: a module can be lifted into its own process in one PR that touches
      only its folder plus one port adapter. Enforce it with an architecture test in CI

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
