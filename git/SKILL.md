---
name: git
description: Git workflow best practices. Branch-first commits, clean history, meaningful messages, and safe merge strategies. Use when working with git repositories, creating commits, managing branches, or resolving conflicts.
license: MIT
---

# Git Workflow

Best-practice git workflow for clean history, meaningful commits, and safe collaboration.

## When to Use

Auto-activates on: "commit", "branch", "merge", "rebase", "push", "pull request", "git", "stash", "cherry-pick", "resolve conflict".

## Core Principles

- **Branch-first**: Never commit directly to `main` or `master`. Always work on a feature/fix branch.
- **Atomic commits**: Each commit should represent one logical change. If you cannot summarize it in one sentence, split it.
- **Meaningful messages**: Commit messages must explain *why*, not just *what*.
- **Clean before push**: Rebase, squash noise commits, and verify tests pass before pushing.
- **Never force-push shared branches**: Only force-push your own unreviewed feature branches.

## Commit Message Format

```
<type>(<scope>): <short summary>

<optional body — explain why, not what>

<optional footer — refs, breaking changes>
```

**Types:**
- `feat` — new feature
- `fix` — bug fix
- `refactor` — code change that is neither a fix nor a feature
- `docs` — documentation only
- `test` — adding or fixing tests
- `chore` — tooling, deps, CI, build scripts
- `perf` — performance improvement
- `style` — formatting, missing semicolons (no logic change)

**Examples:**
```
feat(auth): add OAuth2 login with GitHub provider

fix(api): handle null user in session middleware

Fixes #42

refactor(db): extract query builder into separate module

docs(readme): add installation steps for Windows
```

## Branch Naming

```
feat/<short-description>
fix/<short-description>
chore/<short-description>
release/<version>
hotfix/<short-description>
```

Examples:
- `feat/user-profile-page`
- `fix/login-redirect-loop`
- `chore/update-dependencies`

## Workflow

### Starting Work

```bash
# Always start from an up-to-date main
git checkout main
git pull origin main

# Create your branch
git checkout -b feat/my-feature
```

### During Work

```bash
# Stage specific files (avoid git add .)
git add src/feature.ts tests/feature.test.ts

# Commit with a meaningful message
git commit -m "feat(feature): implement X to solve Y"

# Keep your branch up to date
git fetch origin
git rebase origin/main
```

### Before Pushing

1. Run tests — do not push broken code
2. Review your diff: `git diff origin/main`
3. Squash noise commits: `git rebase -i origin/main`
4. Push: `git push origin feat/my-feature`

### Merging / Pull Requests

- Prefer **squash merge** for feature branches with noisy history
- Prefer **merge commit** for long-lived branches where full history matters
- Prefer **rebase merge** for clean linear histories
- Delete the branch after merge

## Conflict Resolution

```bash
# Update your branch with latest main
git fetch origin
git rebase origin/main

# If conflicts arise:
# 1. Open conflicting files and resolve markers (<<<<<<<, =======, >>>>>>>)
# 2. Stage resolved files
git add <resolved-file>
# 3. Continue rebase
git rebase --continue
```

**Never resolve a conflict by blindly accepting one side.** Read both changes and produce the correct merged result.

## Stashing

```bash
# Save work in progress
git stash push -m "WIP: description of what is stashed"

# List stashes
git stash list

# Apply latest stash (keep it in list)
git stash apply

# Apply and remove from list
git stash pop

# Apply specific stash
git stash apply stash@{2}
```

## Undoing Mistakes

| Situation | Command |
|-----------|---------|
| Undo last commit (keep changes staged) | `git reset --soft HEAD~1` |
| Undo last commit (keep changes unstaged) | `git reset HEAD~1` |
| Discard all local changes | `git checkout -- .` |
| Remove untracked files | `git clean -fd` |
| Revert a pushed commit safely | `git revert <sha>` |
| Find a lost commit | `git reflog` |

**Never use `git reset --hard` on shared branches.**

## Tags & Releases

```bash
# Create annotated tag
git tag -a v1.2.0 -m "Release v1.2.0: add feature X, fix bug Y"

# Push tags
git push origin --tags

# Delete a tag (local + remote)
git tag -d v1.2.0
git push origin :refs/tags/v1.2.0
```

## Useful Aliases

Add to `~/.gitconfig`:

```ini
[alias]
  st = status -sb
  lg = log --oneline --graph --decorate --all
  co = checkout
  br = branch
  unstage = reset HEAD --
  last = log -1 HEAD
  aliases = config --get-regexp alias
```
