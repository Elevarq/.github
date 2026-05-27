# Security Policy

This is the organization-wide default security policy for
[Elevarq](https://elevarq.com) repositories. Individual repositories may
publish their own `SECURITY.md`, which takes precedence over this one.

## Reporting a vulnerability

If you discover a security vulnerability in any Elevarq project, please
report it responsibly:

1. **Do not** open a public GitHub issue.
2. Email `security@elevarq.com` with:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
3. We will acknowledge receipt within 48 hours.
4. We will provide a fix timeline within 5 business days.

## Supply chain

Elevarq release artifacts are built only in CI and are published with an
SBOM and Cosign keyless signatures; container images additionally carry
build provenance. Verification steps live in each repository's release
documentation. Our engineering practices are designed for auditability
and aligned with SOC 2 and ISO 27001 controls; we do not claim
certification.
