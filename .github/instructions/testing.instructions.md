# Testing Instructions

Tests prove accepted behavior and meaningful system boundaries.

- Start from specification acceptance criteria and explicit risk.
- Cover happy paths, invalid input, boundaries, authorization, failure, recovery, idempotency, and concurrency when relevant.
- Keep unit tests focused and deterministic.
- Add integration or contract coverage when behavior depends on persistence, serialization, networking, external systems, or production composition.
- Add browser or workflow checks when users consume a real UI or end-to-end path.
- Do not rely on fakes as the only evidence for a public or cross-process contract.
- Keep test data safe, minimal, and reproducible.
- Record command, execution context, result, failures, skips, and evidence in the relevant testing document.
- Treat `RED-EXPECTED` as a pre-implementation baseline only; unexpected failures are regressions.
