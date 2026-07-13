---
name: codebase-design
description: Shape a maintainable codebase from accepted requirements and constraints.
---

# Codebase Design

Use when the project needs boundaries, module responsibilities, dependency direction, or a first structure.

1. Identify the behavior and constraints.
2. Separate domain, transport, persistence, infrastructure, and user-interface concerns where useful.
3. Prefer the selected framework's composition and extension points.
4. Define ownership, dependencies, failure behavior, and test boundaries.
5. Record decisions that future work must not rediscover.

Do not add layers for ceremony. Return a small design with alternatives, tradeoffs, and the next document or implementation owner.
