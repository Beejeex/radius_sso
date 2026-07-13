---
name: diagnosing-bugs
description: Prevent guess-based debugging with a structured 7-step loop: build feedback loop → reproduce exact symptom → minimize repro → generate falsifiable hypotheses → instrument one variable at a time → fix → convert to regression test.
---

# Diagnosing Bugs

## Trigger
Use when:
- The user reports a bug, failure, exception, regression, slow behavior, or flaky behavior.
- `review`, `validate`, tests, browser smoke, API contract checks, or acceptance evidence finds broken behavior.

## Consumers
`review` (classify and reproduce), `test-gen` (convert repros to red baselines), `appdev` and domain agents (fix only after red-capable loop), `validate` (verify original failure no longer reproduces)

## Required Workflow
1. **Build a feedback loop.** (unit/integration test, API request, CLI fixture, Playwright path, payload replay, stress loop)
2. **Reproduce the exact symptom.**
3. **Minimize the repro.**
4. **Generate 3–5 falsifiable hypotheses** when diagnosis is still unclear.
5. **Instrument one variable at a time.**
6. **Fix only after the loop is red-capable.**
7. **Convert the minimized repro into a regression test** when a correct seam exists.
8. **Remove temporary instrumentation** and report final evidence.

## Completion Criteria
- One command or clearly named test path can reproduce or prove the issue.
- The loop checks the user's exact symptom.
- The fix is validated against the original scenario.
- Regression coverage exists, or the absence of a correct seam is explicitly reported.

## Non-Goals
- Do **not** jump to a single theory before the loop exists.
- Do **not** treat unrelated failing tests as proof of the reported bug.
- Do **not** leave debug logs, scratch scripts, or temp files outside `.tmp/`.

## Conflicts
None with existing Ventus gates.

## Dependencies
- `.github/prompts/handover.prompt.md` (handoff from review to fixer)
- `docs/project/AGENT_GUIDANCE.md` (test evidence requirements)
