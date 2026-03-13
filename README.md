# Claude Skills

Shareable skills for Claude Code. Built by Ramy Barsoum.

## Install

**All skills:**

```bash
curl -fsSL https://raw.githubusercontent.com/ramybarsoum/claude-skills/main/install.sh | bash
```

**One skill:**

```bash
curl -fsSL https://raw.githubusercontent.com/ramybarsoum/claude-skills/main/install.sh | bash -s -- feature-spec-interview
```

Skills install to `~/.claude/skills/`. Restart Claude Code after installing.

## Available Skills

| Skill | What it does | Invoke |
|-------|-------------|--------|
| `feature-spec-interview` | Interactive interview that produces AI-agent-executable feature specs (NLSpec). Based on the Dark Factory Framework. Supports solo and PM/Eng split modes. | `/feature-spec-interview` |

## How Skills Work

Each skill is a folder with a `SKILL.md` file and optional `references/`, `scripts/`, and `assets/` directories. Claude Code reads the `SKILL.md` when the skill is invoked and loads reference files as needed.

## Adding a New Skill

1. Create a folder under `skills/` with your skill name
2. Add a `SKILL.md` with YAML frontmatter (`name` and `description` fields)
3. Add reference files, scripts, or assets as needed
4. Submit a PR

## License

MIT
