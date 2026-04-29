---
name: requirements-engineering
description: 'Basic validation skill for testing skill loading, slash invocation, and response behavior in another setup. Use when validating custom skill discovery, frontmatter parsing, and workflow execution.'
argument-hint: 'Optional target setup name (for example: local, ci, teammate-machine)'
user-invocable: true
disable-model-invocation: true
---

# Requirements Engineering

## Purpose
Provide a small, repeatable workflow to confirm a custom skill is discovered and runs correctly in a different environment.

## When to Use
- Validate that a copied skill is visible in chat slash commands.
- Verify frontmatter and discovery keywords are working.
- Check whether behavior is consistent across setups.

## Procedure
1. Confirm the skill exists in the expected folder and the folder name matches the frontmatter name.
2. Invoke the skill with slash command and a short prompt that requests a simple, deterministic output.
3. Verify expected behavior:
   - The skill is discoverable.
   - The assistant follows this procedure.
   - Output is a short free-text summary.
4. If behavior differs in the new setup, compare:
   - Skill file path and scope.
   - Frontmatter validity.
   - Any global instructions that may override behavior.
5. Return a short free-text summary with one observed success and one observed risk.

## Completion Criteria
- Skill appears in slash command suggestions.
- Skill runs without frontmatter or parsing issues.
- Output is a concise free-text summary.

## Example Prompts
- Run this skill to validate loading in setup local.
- Execute basic skill test in teammate-machine and return a short summary of what worked and what might fail.
