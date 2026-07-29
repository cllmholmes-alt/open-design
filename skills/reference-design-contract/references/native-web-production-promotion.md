# Native + web production promotion pattern

Use this reference when one Open Design source must remain reusable across a
web codebase and a native mobile codebase.

## Required stages

1. **Baseline/export** — package all canvases with stable IDs, not just the
   currently edited screen.
2. **Quarantine** — reject path traversal, absolute paths, symlinks, missing
   manifest references, undeclared extensionless files, unsupported types, and
   size/count overflow. Hash every relative path plus file byte stream.
3. **Active-content review** — flag external scripts/forms, network calls,
   dynamic code execution, and process access. Findings never execute.
4. **Implementation map** — require each screen ID, token source, and interaction
   source to map to existing files inside platform-specific roots. Reject stale
   maps when the package hash changes.
5. **Local gates/build** — run deterministic platform tests and unsigned/local
   builds only through pinned toolchains.
6. **Promotion handoff** — emit a non-executing release plan. Production deploy,
   signing, TestFlight/App Store, and provider mutations require their own
   approval and evidence authority.

## ADHD-OS reference implementation

- Policy: `config/opendesign/workflow.json`
- Baseline exporter: `scripts/design/create-opendesign-baseline.mjs`
- Quarantine/mapping/release-plan CLI: `scripts/design/opendesign-workflow.mjs`
- Web screen targets: `config/opendesign/web-screen-targets.json`
- iOS screen targets: `config/opendesign/ios-screen-targets.json`
- Operator guide: `docs/opendesign/README.md`
- Regression tests: `src/__tests__/opendesign-workflow.test.ts`

The ADHD-OS pattern keeps generated packages and receipts local/ignored, while
committing the workflow, target registries, tests, and policy. The current
canonical baselines contain 30 website screens and 54 iOS screens. Native iOS
archive preparation uses the pinned `mise.toml` toolchain and performs no
signing or upload.

## Failure modes

- Treating one HTML file as one screen and silently deduplicating canvases.
- Mapping every canvas to one generic shell rather than its actual route/native
  implementation.
- Letting a configurable command/config path turn a local verifier into an
  arbitrary process runner.
- Claiming a successful native release because an HTML preview renders.
- Combining design import with production deploy or App Store mutation.
- Reusing an implementation review after the export hash changes.
