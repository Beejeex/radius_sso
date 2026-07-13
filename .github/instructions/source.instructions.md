# Source Instructions

- Read the accepted specification, ADRs, schema, and test plan before changing behavior.
- Follow the existing project structure and naming conventions.
- Keep domain behavior independent from transport, persistence, framework, and infrastructure concerns where the design calls for it.
- Prefer small, composable units and explicit dependencies.
- Validate user-controlled input at trust boundaries.
- Keep error handling, logging, retries, timeouts, cancellation, and resource ownership explicit.
- Do not add abstractions without a concrete reduction in complexity or a documented boundary.
- Add comments only for non-obvious reasoning, invariants, or intentionally unusual constraints.
- Run the narrowest useful checks first, then broaden validation in proportion to risk.
