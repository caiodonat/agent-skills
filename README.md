# agent-skills

A collection of reusable AI agent skills compatible with [skills.sh](https://skills.sh).

Works with Claude Code, Cursor, Windsurf, Codex, and any agent that supports the `SKILL.md` format.

## Installation

```bash
npx skills add caiodonat/agent-skills
```

Install a specific skill:

```bash
npx skills add caiodonat/agent-skills --skill git
npx skills add caiodonat/agent-skills --skill code-review
npx skills add caiodonat/agent-skills --skill debug
```

## Available Skills

### [git](./git/SKILL.md) — Git Workflow

Branch-first commits, clean history, meaningful messages, safe merge strategies, and conflict resolution.

### [code-review](./code-review/SKILL.md) — Code Review

Structured review checklist covering correctness, security, performance, readability, and test coverage.

### [debug](./debug/SKILL.md) — Debugging Workflow

Systematic reproduce → isolate → hypothesize → verify → fix → confirm loop for any language.

## License

MIT