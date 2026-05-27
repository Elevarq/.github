# Specification: Release Standardization

Status: ACTIVE
Type: Configuration decision / standardization

## Purpose

Define the **shared release gates** that every Elevarq repository producing
a releasable artifact (binary, container image, Helm chart, CLI, library,
or public website) must satisfy before release, and define how each
repository demonstrates conformance.

This specification is the org-wide standard. It does **not** replace a
repository's own release spec — it is the contract those specs implement.
Where a repository has a detailed release spec (e.g. pgAgroal's
`specifications/project-release/`), that spec is the authoritative
realization and this document is the umbrella it conforms to.

Source of truth for the gates: the **Release Protocol** in the
organization engineering standards (`CLAUDE.md`). This spec restates the
gates so they are testable and so conformance can be tracked per
repository.

## Scope

- **In scope:** the gate definitions, the conformance matrix, and the
  per-repository demonstration of each gate.
- **Out of scope:** repository-specific release mechanics (version files,
  tag formats, channel rules, changelog classes). Those live in each
  repository's own release spec/docs.

## Gates

A release is blocked at the first failing gate. Gates are normative
("MUST").

| Gate | Name | Requirement |
|------|------|-------------|
| **A** | Correctness | Specs current where STDD-governed; unit + integration tests pass; the **built artifact** is smoke-tested, not only source. |
| **B** | Lint / quality | All applicable linters pass (`go vet`/golangci-lint, `tsc`/eslint, hadolint, ShellCheck, actionlint, `helm lint`). |
| **C** | Security | Trivy (fs + config + image) and a secret scan (Gitleaks) run; **no unresolved CRITICAL**; HIGH documented and justified. |
| **D** | Supply chain | GitHub Actions pinned to major-version tags or SHAs; base images pinned (no floating `latest` in release artifacts); dependencies locked; clean committed worktree. |
| **E** | Artifact | Build the real artifact; test the real artifact; generate an SBOM; sign where practical (Cosign). |
| **F** | Release hygiene | Version consistency across all version-bearing files; CHANGELOG updated; tag matches artifact version; upgrade/docs notes where behavior changed. |

### Invariants

- **No manual production artifacts.** Release artifacts are built only in
  CI from committed source.
- **Built-artifact verification.** A release that passes source tests but
  ships an untested binary or image is not a valid release.
- **No undocumented enforcement claims.** A gate is described as
  *CI-enforced* only where a workflow actually fails the release on it;
  otherwise it is *manual* or *script-checked*. See the conformance
  matrix.

## Conformance matrix

Honest current state. `CI` = a workflow fails the release/PR on this gate;
`script` = a local/`make` script checks it; `manual` = checked by the
releaser at review; `—` = not applicable.

| Gate | Arq-Signals | pgAgroal |
|------|-------------|----------|
| A Correctness | CI (unit + integration; image built in `release.yml`) | CI (integration + resilience in `container-ci.yml`) |
| B Lint | CI (golangci-lint, `go vet`) | CI (hadolint, ShellCheck, `helm lint`) |
| C Security | CI (Trivy, Gitleaks, govulncheck, CodeQL; nightly Grype/Semgrep/OSV/Scorecard) | CI (Trivy, Gitleaks) |
| D Supply chain | CI/review (actions pinned to major tags or SHA; base images pinned) | CI/review (actions pinned; base image pinned; Dependabot) |
| E Artifact | CI (multi-arch image + Cosign keyless + SBOM + SLSA provenance; release attaches `sbom.spdx.json` + `SHA256SUMS`) | CI (multi-arch image + Cosign keyless + SBOM + SLSA provenance). **Gap:** GitHub Release does not yet link image/SBOM/provenance — tracked in Elevarq/pgAgroal#30. |
| F Hygiene | manual (CHANGELOG, version) | script (`make prepare-release` validates version triple; CHANGELOG class) + manual |

## How a repository demonstrates conformance

1. Reference this spec from the repository's release docs or release spec.
2. Keep the matrix row above accurate. When a gate's enforcement changes
   (e.g. #30 closes), update the row in the same PR.
3. Where a gate is `manual`, the release checklist
   ([RELEASE-CHECKLIST.md](../../RELEASE-CHECKLIST.md)) is the record that
   it was performed.

## Failure conditions

| Trigger | Response |
|---------|----------|
| Unresolved CRITICAL security finding | STOP — do not release. |
| Artifact not built or not tested | STOP. |
| Secrets detected | STOP. |
| HIGH finding with no upstream fix | Document and justify; CAUTION release. |
| Version-bearing files disagree | STOP until consistent. |

## Lifecycle

`DRAFT → ACTIVE`. This spec is `ACTIVE`: it documents gates already in
force per the engineering standards. Changes to the gate set follow the
behavior-change flow (update this spec first, then the acceptance cases,
then any tooling).
