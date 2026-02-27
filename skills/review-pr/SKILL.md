---
name: review-pr
description: Review a pull request like a staff engineer — check for bugs, security issues, design problems, and suggest improvements
disable-model-invocation: true
argument-hint: "[PR number or URL]"
---

**Tip:** PR reviews are ideal for subagent offloading — the review context stays isolated and only the verdict flows back.

Review PR $ARGUMENTS as a staff engineer.

## 1. Get PR Context
```bash
gh pr view $ARGUMENTS
gh pr diff $ARGUMENTS
gh pr view $ARGUMENTS --comments
```

## 2. Review Checklist

### Correctness
- Does the code do what the PR description says?
- Are there edge cases not handled?
- Any off-by-one errors, null pointer risks, or race conditions?

### Security
- Any user input that's not validated/sanitized?
- Secrets or credentials exposed?
- SQL injection, XSS, or command injection risks?
- Proper auth checks on new endpoints?

### Design
- Does this fit the existing architecture?
- Is there unnecessary complexity?
- Could existing utilities/patterns be reused instead?
- Will this be easy to maintain and extend?

### Performance
- Any N+1 queries or unbounded loops?
- Missing indexes for new queries?
- Large allocations or memory leaks?

### Testing
- Are there tests for the new behavior?
- Do tests cover edge cases and error paths?
- Are tests readable and maintainable?

## 3. Output Format

Start with a 1-line verdict: APPROVE, REQUEST_CHANGES, or NEEDS_DISCUSSION.

Then list findings as:
- **P0 (Must fix)**: Bugs, security issues, data loss risks
- **P1 (Should fix)**: Design issues, missing tests, unclear code
- **P2 (Nit)**: Style, naming, minor improvements

Be specific — reference file:line for each finding.
Be constructive — suggest fixes, not just problems.
