---
name: debug
description: Systematic debugging workflow. Reproduce, isolate, hypothesize, verify, and fix bugs. Use when investigating errors, crashes, unexpected behavior, or failing tests.
license: MIT
---

# Debug

A systematic approach to finding and fixing bugs efficiently.

## When to Use

Auto-activates on: "debug", "fix bug", "error", "crash", "not working", "broken", "failing test", "investigate", "why is", "unexpected behavior", "exception", "stack trace".

## The Debugging Loop

```
1. REPRODUCE → confirm you can reliably trigger the issue
2. ISOLATE   → narrow down where the problem is
3. HYPOTHESIZE → form a specific theory about the cause
4. VERIFY    → test the hypothesis (don't guess-and-patch)
5. FIX       → make the minimal correct change
6. CONFIRM   → verify the fix and check for regressions
```

Never skip steps. "Guess and patch" creates new bugs.

## Step 1 — Reproduce

Before touching any code:

- Can you reproduce the issue consistently?
- What are the exact steps to trigger it?
- Does it happen in all environments or just one?
- When did it start? (check recent commits: `git log --oneline -20`)
- Does it happen for all users or just specific ones?

If you cannot reproduce it, you cannot confirm a fix.

## Step 2 — Isolate

Narrow the failure surface:

```
Binary search principle: divide the code in half, check which half has the bug, repeat.
```

Techniques:
- **Comment out code**: disable sections until the bug disappears
- **Add logging**: print state at key points to trace data flow
- **Write a failing test**: capture the exact input/output that fails
- **Minimal reproduction**: create the smallest possible code that triggers the issue
- **Diff against working version**: `git diff <last-working-commit>`

Questions to ask:
- What is the last state where things worked correctly?
- What changed between working and broken?
- Is the bug in my code, a library, or infrastructure?

## Step 3 — Hypothesize

Form **one specific hypothesis** at a time:

```
"I believe the bug is caused by X, because Y, which would explain Z."
```

Examples:
- "The null check on line 42 is missing, because the API sometimes returns undefined, which would explain the TypeError."
- "The cache is returning stale data, because TTL is set to 0, which would explain the inconsistent reads."

Write it down. It keeps you honest.

## Step 4 — Verify

Test your hypothesis without fixing first:

```bash
# Add a targeted log to confirm your theory
console.log('[debug] value at line 42:', value);

# Or add a breakpoint and inspect in debugger
# Or write a test that proves the hypothesis
```

If evidence contradicts your hypothesis, **abandon it** and form a new one. Never patch code to force your hypothesis to be true.

## Step 5 — Fix

Make the **minimal correct change**:

- Fix the root cause, not the symptom
- Do not change unrelated code in the same commit
- If the fix requires a refactor, do it separately

Ask before fixing:
- Is this the root cause, or am I treating a symptom?
- Could this fix break anything else?
- Is there a simpler fix?

## Step 6 — Confirm

After applying the fix:

1. Reproduce the original bug — it should now be gone
2. Run the full test suite
3. Test adjacent functionality that could be affected
4. Add a regression test that would catch this bug if reintroduced

## Common Bug Patterns

### Null / Undefined Errors

```
Cause: accessing a property on null/undefined
Fix: add a null check or ensure the value is initialized
Tip: trace backwards — where was this value set? Can it ever be null?
```

### Off-By-One Errors

```
Cause: wrong loop bounds, index starting at 0 vs 1
Fix: verify boundary conditions with unit tests
Tip: test with empty list, single item, two items
```

### Async / Race Conditions

```
Cause: two async operations completing in unexpected order
Fix: use proper await/promise chaining, or add synchronization
Tip: add logging with timestamps to see actual execution order
```

### Stale State / Cache Issues

```
Cause: old data being served from cache or local state
Fix: invalidate cache on mutation, or reduce TTL during debugging
Tip: test with cache disabled first
```

### Environment Differences

```
Cause: code works locally but fails in CI/production
Fix: compare environment variables, dependencies, OS/platform
Tip: check .env files, dependency versions, file path casing
```

### Regex / Parsing Errors

```
Cause: regex doesn't match expected input, or parser handles edge case wrong
Fix: test regex at regex101.com, add unit tests for edge cases
Tip: log the actual input vs expected — often it's a whitespace/encoding issue
```

## Debugging Tools by Language

### JavaScript / TypeScript

```bash
# Run with Node debugger
node --inspect src/index.js

# In tests (Jest)
node --inspect-brk node_modules/.bin/jest --testPathPattern=my-test

# Quick print debugging
console.log(JSON.stringify(value, null, 2));
```

### Python

```python
# Built-in debugger
import pdb; pdb.set_trace()

# Or with breakpoint() (Python 3.7+)
breakpoint()

# Pretty print
import json; print(json.dumps(value, indent=2, default=str))
```

### General

```bash
# Search for where a variable/function is used
grep -rn "functionName" src/

# Check recent changes that could have introduced the bug
git log --oneline --since="2 days ago"
git diff HEAD~5 -- path/to/file.ts

# Check if the issue exists on main
git stash
git checkout main
# reproduce the issue
git checkout -
git stash pop
```

## When You're Stuck

1. **Rubber duck**: explain the problem out loud (or in writing) step by step
2. **Take a break**: fresh eyes catch things tired eyes miss
3. **Ask for help early**: share your hypothesis and what you've tried
4. **Revert**: if a recent change introduced the bug, revert it and re-apply cleanly
5. **Read the error message carefully**: the answer is often right there

## After Fixing

- Write a regression test
- Add a code comment if the fix is non-obvious
- Update documentation if the behavior changed
- Note the root cause in the PR description so reviewers understand the fix
