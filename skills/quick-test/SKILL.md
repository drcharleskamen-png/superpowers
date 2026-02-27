---
name: quick-test
description: Run tests and report results with clear pass/fail summary
disable-model-invocation: true
argument-hint: "[test path or pattern]"
---

## Task Tracking
Before starting, create a task list using TodoWrite with the major steps below. Mark each completed as you finish it.

Run tests for: $ARGUMENTS

## Steps

1. Detect the test framework:
   - Look for `pytest.ini`, `pyproject.toml [tool.pytest]`, `jest.config.*`, `vitest.config.*`, `Cargo.toml`
   - Default to pytest for Python, jest/vitest for JS/TS

2. Run the tests:
   - If $ARGUMENTS is a specific file/path, run just those tests
   - If $ARGUMENTS is empty, run the full test suite
   - Capture stdout and stderr

3. Report results:
   - Total tests: passed / failed / skipped
   - For failures: show the test name, expected vs actual, and the relevant source line
   - Execution time

4. If tests fail:
   - Read the failing test and the code it tests
   - Diagnose the root cause
   - Suggest a fix (but don't apply it without asking)

## Next Steps
If tests fail, use the `systematic-debugging` skill for root cause analysis before attempting fixes.
