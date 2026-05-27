# Release checklist

Copy this checklist into the release issue or PR for any Elevarq
repository that produces a releasable artifact. It is the operational
form of
[specifications/release-standardization/spec.md](specifications/release-standardization/spec.md);
the gates are normative and a release is blocked at the first failing one.

Repository-specific mechanics (version files, tag/channel rules, changelog
classes) live in the repository's own release spec/docs — follow those for
the details; this checklist confirms the shared gates.

```
Release: <repo> <version>

Gate A — Correctness
- [ ] Specs current (STDD-governed repos)
- [ ] Unit + integration tests pass in CI
- [ ] Built artifact (binary/image) smoke-tested, not just source

Gate B — Lint / quality
- [ ] All applicable linters pass (vet/golangci-lint, tsc/eslint,
      hadolint, ShellCheck, actionlint, helm lint)

Gate C — Security
- [ ] Trivy (fs/config/image) clean of unresolved CRITICAL
- [ ] Secret scan (Gitleaks) clean
- [ ] Any HIGH findings documented and justified

Gate D — Supply chain
- [ ] Actions pinned to major tags or SHAs (no moving branches)
- [ ] Base images pinned (no floating `latest` in release artifacts)
- [ ] Dependencies locked; worktree clean and committed

Gate E — Artifact
- [ ] Real artifact built in CI (no manual artifacts)
- [ ] Real artifact tested
- [ ] SBOM generated (release asset and/or OCI attestation)
- [ ] Signed with Cosign (keyless) where practical
- [ ] Verification commands published / linked

Gate F — Release hygiene
- [ ] Version consistent across all version-bearing files
- [ ] CHANGELOG updated for this version
- [ ] Tag matches artifact version
- [ ] Upgrade / docs notes where behavior changed

Verdict: GO / CAUTION (HIGH documented) / STOP
```
