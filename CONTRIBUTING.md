# Contributing to lifekit

Thanks for your interest. These defaults apply to every public repository in the
`lifekit-hq` organization; a repository's own `CONTRIBUTING.md`, if present, wins.

## Ground rules

- **One change, one repository.** The stack is a federation: if your change needs to
  touch two repos at once, open an issue first - the coupling probably belongs in a
  declaration (an env value, an MCP contract, a compose entry), not in code.
- **Behavior changes ship a test.** Name the regression test after the behavior it pins.
- **No secrets, ever.** Configuration is templated; values are supplied at deploy time.
  A PR containing a credential, token, or personal data will be closed.

## Issues

- Search existing issues first.
- Bug reports: use the bug template. Include what you ran, what you expected, what
  happened, and the version/commit.
- Feature ideas: use the feature template. Describe the problem before the solution.

## Pull requests

1. Fork, branch from `main`, keep the branch focused on one change.
2. Match the surrounding code style; each repo's linter config is the authority
   (ruff for Python, strict TypeScript where configured).
3. Fill in the PR template: what changed, what layer it touches, how it was tested.
4. CI must be green before review.

## Development setup

Each repository is independently cloneable and buildable - see its own README for
setup. There is no umbrella build.

## Security

Do not open public issues for vulnerabilities - see [SECURITY.md](SECURITY.md).
