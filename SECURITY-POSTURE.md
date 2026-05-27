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

Applies to **Arq-Signals** and **pgAgroal**.

- **CI-only builds.** Release artifacts are built by GitHub Actions from
  committed source, not from a developer machine.
- **Cosign keyless signatures.** Published container images are signed
  with [cosign](https://github.com/sigstore/cosign) using GitHub OIDC and
  the Sigstore public-good infrastructure — no long-lived signing keys.
- **SBOM.** Builds produce an SPDX SBOM. Arq-Signals also attaches
  `sbom.spdx.json` to its GitHub releases alongside `SHA256SUMS`.
- **SLSA provenance.** Image builds attach build provenance
  (`provenance: mode=max`) as an OCI attestation.

### Verifying a release

Container image signature (keyless):

```sh
cosign verify <image>@<digest> \
  --certificate-identity-regexp 'https://github.com/Elevarq/.*' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

SBOM / provenance attestations:

```sh
cosign verify-attestation --type spdxjson      <image>@<digest>   # SBOM
cosign verify-attestation --type slsaprovenance <image>@<digest>  # provenance
```

Each repository's release documentation carries the exact image
references and commands.

## Static analysis and scanning

**Arq-Signals** (Go) runs, in CI and on a nightly schedule:

- CodeQL
- `govulncheck` (Go vulnerability database)
- Trivy (filesystem) and Grype (against the generated SBOM)
- Semgrep and OSV-Scanner
- Gitleaks secret scanning (full-history nightly; staged + current-commit
  on push)
- OpenSSF Scorecard

**pgAgroal** (container / Helm) runs in CI:

- hadolint (Dockerfile) and ShellCheck (entrypoint)
- Trivy
- Gitleaks secret scanning
- `helm lint`

## Runtime hardening

**Arq-Signals**

- Read-only PostgreSQL access enforced by three independent layers:
  startup SQL linting (DDL/DML aborts the process), a session set to
  `default_transaction_read_only=on`, and per-query `BEGIN ... READ ONLY`.
- No outbound network calls except to the PostgreSQL targets it collects
  from — no telemetry, no analytics, no AI provider calls.
- Runs air-gapped. Credentials are read at connection time and never
  written to disk or included in exports.
- Container runtime is non-root with no shell or compilers in the
  production image.

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
- Dependency manifests are locked (`go.sum`, etc.).

## Organization controls

- Two-factor authentication is **required for all members** of the
  Elevarq GitHub organization.
- Repositories are created with secret scanning, push protection, and
  Dependabot security updates enabled by default.

## Scope

This summary covers the public projects. Per-repository policies provide
the authoritative detail:

- [Arq-Signals SECURITY.md](https://github.com/Elevarq/Arq-Signals/blob/main/SECURITY.md)
- [pgAgroal SECURITY.md](https://github.com/Elevarq/pgAgroal/blob/main/SECURITY.md)
