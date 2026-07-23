# Security Policy

## Reporting a vulnerability

Please **do not** open a public issue for security problems.

Report privately via [GitHub Security Advisories](https://github.com/lifekit-hq/lifekit-stack/security/advisories/new)
on the relevant repository, or by opening a private report. You can expect an
acknowledgement within a few days.

## Scope

This stack is **self-hosted and single-user by design**: loopback-only network
posture by default, no public ingress, no multi-tenancy. Secrets and personal
data never live in these repositories — configuration is templated and values
are supplied at deploy time.
