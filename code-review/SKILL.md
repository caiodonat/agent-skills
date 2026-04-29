---
name: code-review
description: Structured code review workflow. Evaluate correctness, security, performance, readability, and test coverage. Use when asked to review code, open a PR review, or audit a file or function.
license: MIT
---

# Code Review

A structured workflow for reviewing code with consistent, actionable feedback.

## When to Use

Auto-activates on: "review", "code review", "review PR", "review pull request", "review this code", "audit code", "check code", "review file", "LGTM", "approve PR".

## Review Checklist

Go through each category. Flag issues by severity: 🔴 **Critical** · 🟡 **Major** · 🔵 **Minor** · 💡 **Suggestion**.

### 1. Correctness

- [ ] Does the code do what it's supposed to do?
- [ ] Are edge cases handled? (null, empty, boundary values)
- [ ] Are errors handled and surfaced appropriately?
- [ ] Are there race conditions or concurrency issues?
- [ ] Does it match the spec / ticket requirements?

### 2. Security

- [ ] No secrets, tokens, or credentials in code
- [ ] Input is validated and sanitized before use
- [ ] No SQL/command/template injection vectors
- [ ] Authentication and authorization are checked where needed
- [ ] Sensitive data is not logged
- [ ] Dependencies are not introducing known vulnerabilities

### 3. Performance

- [ ] No obvious N+1 queries
- [ ] No unnecessary loops over large datasets
- [ ] Caching used where appropriate
- [ ] No blocking operations on the hot path
- [ ] Memory allocations are reasonable

### 4. Readability & Maintainability

- [ ] Names (variables, functions, classes) are clear and intention-revealing
- [ ] Functions are single-purpose and reasonably short
- [ ] No unnecessary complexity or premature abstraction
- [ ] Comments explain *why*, not *what*
- [ ] Dead code is removed
- [ ] Consistent style with the rest of the codebase

### 5. Tests

- [ ] New code has corresponding tests
- [ ] Tests cover the happy path and key edge cases
- [ ] Tests are readable and well-named
- [ ] No tests were deleted without good reason
- [ ] Test coverage is not degraded

### 6. API & Interface Design

- [ ] Public API is intuitive and follows project conventions
- [ ] Breaking changes are identified and documented
- [ ] Return types and error types are explicit
- [ ] Parameters are not excessive (≤ 4 preferred; use an object for more)

## Feedback Format

Structure feedback as:

```
**[Severity] Category — File:Line**

Observation: <what you see>
Problem: <why it's an issue>
Suggestion: <how to fix it>

Example (if helpful):
\`\`\`
// before
...

// after
...
\`\`\`
```

**Severity guide:**
- 🔴 **Critical** — Must fix before merge (correctness, security, data loss)
- 🟡 **Major** — Should fix before merge (performance, reliability, maintainability)
- 🔵 **Minor** — Nice to fix (style, naming, small improvements)
- 💡 **Suggestion** — Optional improvement, no action required

## What to Look for by File Type

### TypeScript / JavaScript

- `any` types used where a specific type is possible → 🟡
- Async functions without error handling (no try/catch or `.catch()`) → 🟡
- Promises not awaited → 🔴
- `console.log` left in production code → 🔵
- `== null` vs `=== null` / `=== undefined` → 🔵

### Python

- Bare `except:` clauses that swallow exceptions → 🟡
- Mutable default arguments (e.g. `def f(x=[])`) → 🔴
- Missing type hints on public functions → 🔵
- `import *` usage → 🔵

### SQL / Database

- Queries built by string concatenation → 🔴 (injection risk)
- SELECT * in application code → 🟡
- Missing indexes on filtered/joined columns → 🟡
- Transactions missing around multi-step writes → 🔴

### Infrastructure / Config

- Hardcoded environment-specific values → 🟡
- Secrets in config files → 🔴
- Missing health checks or resource limits → 🟡

## Positive Feedback

Always acknowledge what was done well. Point out:
- Clever solutions worth noting
- Good test coverage
- Clean abstractions
- Well-named functions

A review with only negatives is demoralizing and unhelpful.

## Review Summary Template

After going through the checklist, summarize:

```
## Review Summary

**Overall:** [Approve / Request Changes / Comment]

**Critical issues:** <count> (must fix before merge)
**Major issues:** <count> (should fix)
**Minor / suggestions:** <count>

**Highlights:** <what was done well>

**Key changes needed:**
1. ...
2. ...
```

## Self-Review (before opening a PR)

Before requesting review, the author should:

1. Re-read the diff top to bottom
2. Run the full test suite locally
3. Run the linter and fix all errors
4. Remove all debug statements and TODOs (or convert to issues)
5. Write a clear PR description: what changed, why, and how to test it
