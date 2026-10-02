# lifekit-hq/.github

Org-level defaults and standards for every `lifekit-hq` repository.

| File | Role |
|---|---|
| [`REPO-STANDARD.md`](REPO-STANDARD.md) | **The repo gold standard** — the one checklist every lifekit repo is audited against (metadata, labels, planning, git, releases, CI gates, agent harness, platform contract). Living reference implementation: [lifekit-common](https://github.com/lifekit-hq/lifekit-common). |
| [`profile/README.md`](profile/README.md) | The organization's public profile page |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | Default contribution ground rules (a repo's own copy wins) |
| [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) · [`SECURITY.md`](SECURITY.md) · [`SUPPORT.md`](SUPPORT.md) | Default community health files inherited by all public repos |

## How the standard propagates

1. `REPO-STANDARD.md` here is the single source — it never forks silently.
2. Each repo carries its own independent harness (`CLAUDE.md`, hooks, CI) that must agree with the standard.
3. Adoption and drift are handled through per-repo "Adopt the repo gold standard" issues; genuine divergence is noted in the repo's `CLAUDE.md`, never skipped silently.
