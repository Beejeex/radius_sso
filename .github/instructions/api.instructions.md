# API Instructions

These rules apply when the project exposes an HTTP, event, command, file, or other external contract.

- Define the contract in an accepted specification before implementation.
- Use the selected framework's routing, serialization, validation, authentication, authorization, and error primitives.
- Record methods, paths or names, versions, inputs, outputs, status or result codes, errors, pagination, idempotency, limits, and compatibility expectations.
- Treat external contracts as versioned public behavior. Do not rename or remove fields casually.
- Validate authorization and tenant or user scope before revealing resource existence.
- Keep secrets and sensitive values out of logs and error responses.
- Add contract tests for representative requests, responses, headers, errors, and boundary cases.
- When a vendor or external protocol is involved, use its authoritative documentation and keep its vocabulary at the boundary rather than leaking it into internal models.
