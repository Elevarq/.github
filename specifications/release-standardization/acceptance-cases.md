# Acceptance Cases: Release Standardization

Derived from `spec.md`. Each case maps to a gate or invariant. Cases are
language-neutral; a repository may realize them as workflow-job
assertions, shell tests, or release-checklist sign-offs. Each case is
verifiable from public artifacts (workflow runs, release assets, image
attestations) or from the release record.

Tags: `[gate-A..F]` `[invariant]` `[failure]`.

## AC-01: Tests gate the release — normal  `[gate-A]`

**Given** a release workflow run on a tagged commit
**When** the test job runs
**Then** the workflow fails if unit/integration tests fail, and the
published artifact is built within the same pipeline (not uploaded
manually).

## AC-02: Built artifact is verifiable — normal  `[gate-E] [invariant]`

**Given** a published container image at `<image>@<digest>`
**When** a consumer runs `cosign verify` with the GitHub OIDC issuer and an
`Elevarq/*` identity
**Then** verification succeeds, and `cosign verify-attestation --type
spdxjson` and `--type slsaprovenance` both succeed.

## AC-03: SBOM is published — normal  `[gate-E]`

**Given** a release
**Then** an SPDX SBOM exists as an OCI attestation on the image; for
binary releases an `sbom.spdx.json` asset is attached to the GitHub
Release.

## AC-04: Critical vulnerability blocks release — failure  `[gate-C] [failure]`

**Given** a Trivy or secret scan reports an unresolved CRITICAL finding (or
a detected secret)
**When** the release pipeline runs
**Then** the pipeline stops and no artifact is published.

## AC-05: Version consistency — boundary  `[gate-F]`

**Given** the version-bearing files of a repository (e.g. `VERSION`,
`Chart.yaml`, `go.mod`/`package.json`, Dockerfile refs) and the proposed
tag
**When** the release-prep check runs
**Then** all versions agree, or the release is blocked with the
mismatch identified.

## AC-06: No floating base tags in release artifacts — invalid  `[gate-D] [invariant]`

**Given** a release Dockerfile
**Then** no base image uses a floating `latest` tag; bases are pinned to a
specific tag or digest.

## AC-07: Actions are pinned — invalid  `[gate-D]`

**Given** the release/CI workflows
**Then** every `uses:` reference is pinned to a major-version tag or a
commit SHA (never a moving branch such as `@master`).

## AC-08: Clean worktree — invariant  `[gate-D] [invariant]`

**Given** a release build
**Then** it is produced from a clean, committed state (no dirty worktree).

## AC-09: CHANGELOG updated — boundary  `[gate-F]`

**Given** a new release version
**Then** `CHANGELOG.md` contains an entry for that version before the tag
is cut.

## AC-10: Conformance matrix accuracy — invariant  `[invariant]`

**Given** a change to a gate's enforcement in a repository (e.g. CI job
added/removed)
**Then** the corresponding row of the conformance matrix in `spec.md` is
updated in the same PR — a gate is never described as CI-enforced unless a
workflow actually fails on it.
