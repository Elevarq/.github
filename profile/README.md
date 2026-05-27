# Elevarq

PostgreSQL tools for engineering teams.

Elevarq builds operational tooling focused on PostgreSQL diagnostics, reliability, and performance analysis — from lightweight telemetry collection to production incident investigation.

Website: https://elevarq.com

## Open core

Elevarq is built as an open core. The parts that collect data, package
infrastructure, and run inside your environment are open source. The
commercial product is the diagnostic reasoning layer that sits on top of
them.

The split is deliberate: collection and packaging are infrastructure you
should be able to read, run, and audit yourself; the analysis and
workflow layer is where Elevarq's commercial work lives.

### Open source

- **Arq-Signals** — read-only PostgreSQL telemetry collector designed for
  operational analysis and air-gapped environments. No outbound calls,
  no analysis, no AI — it gathers evidence and nothing else.
- **pgAgroal** — production-ready container distribution of pgagroal, the
  high-performance PostgreSQL connection pooler. Hardened image and Helm
  chart. BSD-3 licensed.

### Commercial

- **Arq** — diagnostic reasoning over the signals: correlating
  operational evidence to explain why a PostgreSQL system degrades over
  time and what to do next. Arq consumes the open collectors; it does not
  replace them.

## Engineering Approach

Our projects emphasize:
- reproducible diagnostics
- operational transparency
- secure-by-default deployments
- signed and verifiable releases
- production-focused engineering

## Security and supply chain

Release artifacts are built only in CI and published with Cosign keyless
signatures, an SBOM, and SLSA build provenance. Arq-Signals is read-only
by design, makes no outbound calls, and runs air-gapped; container images
run non-root with least-privilege defaults. Static analysis, secret
scanning, and dependency automation run continuously.

These practices are designed for auditability and aligned with SOC 2 and
ISO 27001 controls — we do not claim certification. Details and
verification commands are in
[SECURITY-POSTURE.md](https://github.com/Elevarq/.github/blob/main/SECURITY-POSTURE.md).

## Contact

frank@elevarq.com

Elevarq is a DBA of Scantr LLC.
