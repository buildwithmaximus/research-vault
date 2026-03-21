---
title: Claude Code Agent Workspace — Key Findings
topic: claude-code-agent-workspace
date: 2026-03-21
tags: [findings, decisions, next-steps]
source: Original research — Natan × Maximus
---

# Key Findings

## TL;DR
Build a lean, native-first Claude Code agent workspace template. 3 files define identity, 3 files manage memory, `.claude/` folder handles skills + subagents + config. 3 custom skills only — everything else is native. Ship as a GitHub template repo.

## Top Findings

### 1. Build native-first, not OpenClaw-port
Claude Code has a powerful native feature set (skills, hooks, subagents, auto memory, /loop). The workspace should use these primitives directly rather than recreating OpenClaw's architecture in a different environment. Leaner, more maintainable, aligned with Anthropic's roadmap.

### 2. CLAUDE.md is the single entry point
Everything loads through CLAUDE.md. Keep it ≤200 lines. Import SOUL.md and USER.md via @-mentions. Use `.claude/rules/` for scoped rules. This is the non-negotiable core of the architecture.

### 3. Skills = Commands (unified in Claude Code)
`.claude/skills/` and `.claude/commands/` are the same thing. Skills can auto-trigger based on description — Claude loads them when relevant without being explicitly called. This is more powerful than OpenClaw's skill system.

### 4. /loop replaces cron
`/loop 30m <prompt>` runs prompts on a schedule while the session is open. No cron setup, no heartbeat infrastructure. Native.

### 5. Only 3 custom skills needed
Everything else is already built by Anthropic. Custom: `/daily`, `/compress`, `/install-skill`. That's it.

### 6. Memory is already solved
Auto memory (Claude writes) + CLAUDE.md (human writes) + memory/ files (daily logs) + MEMORY.md (curated). The workspace structures these, doesn't reinvent them.

### 7. No integrations in the starter
Notion, GitHub, Slack = .mcp.json add-ons. Each person adds what they need. Starter is pure agent identity + behavior + memory.

## Decisions Made

| Decision | Rationale |
|----------|-----------|
| Personal instances, not shared | Each person owns memory + identity |
| Local memory only | Privacy, no sync infrastructure |
| Native skills over custom | Less maintenance, follows Anthropic updates |
| No integrations in starter | Clean separation of concerns |
| .mcp.json for integrations | Claude Code native, extensible |
| BOOTSTRAP.md self-deletes | Clean first-run experience |

## Action Items
- [ ] Name the project
- [ ] Build the starter codebase in Claude Code / Antigravity
- [ ] Write CLAUDE.md template
- [ ] Write SOUL.md template (opinionated but customizable)
- [ ] Write USER.md template
- [ ] Write BOOTSTRAP.md (first-run wizard)
- [ ] Write 3 custom skills: /daily, /compress, /install-skill
- [ ] Write researcher subagent
- [ ] Publish as GitHub template repo

## What We'd Research Next
- Claude Code hooks in depth (SessionStart payload, settings.json schema)
- MCP server setup for Notion + GitHub (for skills add-on pack)
- Agent teams feature (multiple agents coordinating across sessions)
- Channels feature (push external events into running session)
