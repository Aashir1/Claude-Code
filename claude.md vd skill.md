# CLAUDE.md vs SKILL.md

## CLAUDE.md - Project Memory
**Purpose**: General instructions and context that's always loaded automatically

- **When it loads**: Automatically when Claude Code starts
- **What it's for**: Project preferences, coding standards, architecture notes, team conventions
- **Where it lives**: `./.claude/CLAUDE.md` or `./CLAUDE.md`
- **Use case**: "Always remember our API uses JWT auth and follows REST conventions"

## SKILL.md - Specialized Capabilities
**Purpose**: Specific, focused expertise that Claude chooses to use when relevant

- **When it loads**: Claude autonomously activates it when your request matches its description
- **What it's for**: Single-purpose workflows (e.g., "generate commit messages", "fill PDF forms")
- **Where it lives**: `.claude/skills/skill-name/SKILL.md`
- **Structure**: Requires YAML frontmatter with `name` and `description`
- **Use case**: "I need help with X" → Claude sees relevant Skill and activates it

## Quick Comparison

| Aspect | CLAUDE.md | SKILL.md |
|--------|-----------|----------|
| **Invocation** | Always loaded | Loaded when relevant |
| **Purpose** | Broad context | Focused capability |
| **Structure** | Plain markdown | YAML + markdown |
| **Usage** | Project memory | Modular expertise |
| **Activation** | Session start | During conversation (autonomously) |
| **Scope** | Project-wide instructions | Single-purpose expertise |
| **Sharing** | Via git directly | Via git or plugins |
| **Tool control** | Standard permissions | Can restrict with `allowed-tools` |

## Storage Locations

### CLAUDE.md Hierarchy
- **Project memory**: `./CLAUDE.md` or `./.claude/CLAUDE.md` (shared with team)
- **User memory**: `~/.claude/CLAUDE.md` (personal, all projects)
- **Project local**: `./CLAUDE.local.md` (personal, auto-gitignored)
- **Modular rules**: `./.claude/rules/*.md` (topic-specific, team-shared)

### SKILL.md Locations
- **Personal Skills**: `~/.claude/skills/skill-name/`
- **Project Skills**: `.claude/skills/skill-name/`
- **Plugin Skills**: Bundled with installed plugins

## Example

**CLAUDE.md**: Your current `CLAUDE.md` has the "speak in roman urdu" directive and recipe constraints - this applies to all interactions.

**SKILL.md**: A SKILL.md for "recipe-generator" would only activate when you're creating recipes.

## When to Use Which?

- Use **CLAUDE.md** for: Project-wide rules, coding standards, always-applicable context
- Use **SKILL.md** for: Specific workflows, specialized tasks, focused capabilities that aren't always needed
