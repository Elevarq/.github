# Security posture

This document describes the security and supply-chain practices that
apply across Elevarq's public projects. Every item here is verifiable
from the public repositories — workflow files, release assets, image
attestations, and Dockerfiles — not from internal policy.

Elevarq's engineering practices are **designed for auditability and
aligned with SOC 2 and ISO 27001 controls**. We do not claim
certification.

For vulnerability reporting, see [SECURITY.md](SECURITY.md)
(`security@elevarq.com`).

## Build and release integrity

Applies to **pgAgroal**.

- **CI-only builds.** Release artifacts are built by GitHub Actions from
  committed source, not from a developer machine.
- **Cosign keyless signatures.** Published container images are signed
  with [cosign](https://github.com/sigstore/cosign) using GitHub OIDC and
  the Sigstore public-good infrastructure — no long-lived signing keys.
- **SBOM.** Builds produce an SPDX SBOM attached as an OCI attestation.
- **SLSA provenance.** Image builds attach build provenance
  (`provenance: mode=max`) as an OCI attestation.

### Verifying a release

The pgAgroal image is published to Docker Hub and can be verified
directly:

```sh
cosign verify docker.io/elevarq/pgagroal:<version> \
  --certificate-identity-regexp 'https://github.com/Elevarq/.*' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com

cosign verify-attestation --type spdxjson      docker.io/elevarq/pgagroal:<version>
cosign verify-attestation --type slsaprovenance docker.io/elevarq/pgagroal:<version>
```

The repository's release notes carry the exact image reference and digest.

## Static analysis and scanning

**pgAgroal** (container / Helm) runs in CI:

- hadolint (Dockerfile) and ShellCheck (entrypoint)
- Trivy
- Gitleaks secret scanning
- `helm lint`

## Runtime hardening

**pgAgroal**

- Non-root runtime (UID 1000), all Linux capabilities dropped,
  `allowPrivilegeEscalation: false`, `seccompProfile: RuntimeDefault`,
  read-only root filesystem, `automountServiceAccountToken: false`.
- Credentials injected via environment variables / Kubernetes Secrets,
  never baked into the image.
- Multi-stage build: build tools are not present in the runtime image.

## Dependency hygiene

- Base images are pinned to specific tags — no floating `latest` in
  release artifacts.
- Dependabot keeps GitHub Actions and dependencies current (see each
  repo's `.github/dependabot.yml` where present).
- Dependency manifests are locked.

## Organization controls

- Two-factor authentication is **required for all members** of the
  Elevarq GitHub organization.
- Repositories are created with secret scanning, push protection, and
  Dependabot security updates enabled by default.

## Scope

This summary covers the public projects. Per-repository policies provide
the authoritative detail:

- [pgAgroal SECURITY.md](https://github.com/Elevarq/pgAgroal/blob/main/SECURITY.md)
