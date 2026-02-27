---
name: techdebt
description: Scan the codebase for technical debt, duplicated code, unused imports, inconsistencies, and improvement opportunities. Run at end of every session.
disable-model-invocation: true
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

**Tip:** For large codebases, add "use subagents" to your prompt to keep the main context clean. This scan generates substantial output.

Perform a thorough technical debt audit of the project source directory.

## 1. Duplicated Code
Search for patterns that appear in multiple files and could be extracted:
- Similar JSON parsing logic across agents
- Repeated prompt construction patterns
- Duplicate error handling blocks

## 2. Dead Code
Find:
- Imported but unused modules
- Functions/methods defined but never called
- Config values defined but not referenced

## 3. Inconsistencies
Check for:
- Inconsistent naming conventions (snake_case vs camelCase)
- Mixed patterns for the same operation (e.g., different JSON extraction methods)
- Agents that don't follow the same interface pattern

## 4. Missing Error Handling
Look for:
- `await` calls without try/except
- JSON parsing without fallback
- File operations without existence checks

## 5. Type Safety
Find:
- Uses of `dict` or `Any` where a Pydantic model could be used
- Missing return type annotations
- Untyped function parameters

## 6. Test Coverage Gaps
Identify modules with no corresponding test file.

## 7. TODOs and FIXMEs
Grep for `TODO`, `FIXME`, `HACK`, `XXX`, `TEMP` comments.

## Output
Present findings as a prioritized list:
- **P0 (Fix now)**: Bugs, broken patterns, security issues
- **P1 (Fix soon)**: Inconsistencies, missing error handling
- **P2 (Fix later)**: Refactoring opportunities, nice-to-haves

End with a count: "Found X issues: Y P0, Z P1, W P2"
