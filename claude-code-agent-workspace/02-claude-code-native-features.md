---
title: Claude Code Native Features — Research
topic: claude-code-agent-workspace
date: 2026-03-21
tags: [claude-code, anthropic, native, features, skills, hooks, memory, subagents]
source: https://code.claude.com/docs/
---

# Claude Code Native Features

## Summary
Claude Code has a rich native feature set that our workspace should build ON TOP of, not duplicate. Understanding these prevents reinventing the wheel.

## Memory System (Native)

### CLAUDE.md
- Auto-loaded at session start
- Multiple scopes: managed (org-wide) → project → user → local
- Keep ≤200 lines for best adherence
- Use `.claude/rules/*.md` for scoped rules (loaded lazily per file type)
- `/init` command auto-generates CLAUDE.md from codebase analysis

### Auto Memory
- Claude writes notes itself based on corrections/preferences
- Loaded at start of every session (first 200 lines)
- Subagents can have their own auto memory
- Complement to CLAUDE.md — you write instructions, Claude writes learnings

### Imports
- `@path/to/file.md` in CLAUDE.md imports other files into context
- Keeps CLAUDE.md lean while referencing SOUL.md, USER.md etc.

## Skills System (Native)

- `SKILL.md` files with YAML frontmatter = slash commands
- Location: `.claude/skills/<name>/SKILL.md` OR `.claude/commands/<name>.md` (same thing)
- Auto-trigger: Claude loads skills when relevant based on description
- Manual invoke: `/skill-name`
- Follow AgentSkills open standard (agentskills.io)

### Bundled Skills (ship with Claude Code)
| Skill | Purpose |
|-------|---------|
| `/batch` | Parallel large-scale codebase changes |
| `/simplify` | Parallel code review + fixes |
| `/debug` | Session troubleshooting |
| `/loop [interval] <prompt>` | Run prompts on a schedule — **replaces cron** |
| `/claude-api` | Load Claude API reference |

### Built-in Commands (not skills)
`/clear`, `/compact`, `/config`, `/context`, `/cost`, `/diff`, `/doctor`, `/help`, `/init`, `/memory`, `/agents`, `/btw`, `/copy`, `/add-dir`, `/loop`, `/color`

## Subagents (Native)

- Defined in `.claude/agents/<name>.md` (project) or `~/.claude/agents/` (user)
- Own context window, system prompt, tool restrictions, model, permissions
- Built-in: **Explore** (read-only, Haiku), **Plan** (read-only), **General-purpose** (all tools)
- Custom: any specialized agent with own memory, hooks, skills
- `/agents` command to manage
- Cannot spawn sub-subagents (no infinite nesting)

## Hooks (Native)

Triggered at lifecycle points. Can run shell scripts, HTTP endpoints, or LLM prompts.

| Hook | When |
|------|------|
| SessionStart | Session begins or resumes |
| UserPromptSubmit | Before Claude processes prompt |
| PreToolUse | Before tool call (can block) |
| PostToolUse | After tool call succeeds |
| Stop | Claude finishes responding |
| SubagentStart/Stop | Subagent spawned/finished |
| InstructionsLoaded | CLAUDE.md file loaded |

Config: `.claude/settings.json` → `hooks` section

## Settings Scopes

| Scope | Location | Shared? |
|-------|----------|---------|
| Managed | System-level | Yes (IT deployed) |
| Project | `.claude/settings.json` | Yes (git) |
| Local | `.claude/settings.local.json` | No (gitignored) |
| User | `~/.claude/settings.json` | No |

## MCP Servers

- Project-level: `.mcp.json` in repo root
- User-level: `~/.claude.json`
- Used for integrations: Notion, GitHub, Slack, custom APIs
- NOT baked into starter — each person adds their own

## Channels (Advanced)

- Push events into a running session from an MCP server
- Forward CI results, webhooks, chat messages
- Enables proactive agent behavior without polling
