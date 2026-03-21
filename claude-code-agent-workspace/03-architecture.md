---
title: Agent Workspace — File Architecture
topic: claude-code-agent-workspace
date: 2026-03-21
tags: [architecture, file-structure, claude-code, design]
source: Original research — Natan × Maximus
---

# Agent Workspace File Architecture

## Full Structure

```
agent-workspace/
│
├── CLAUDE.md                    ← Master entry point (≤200 lines)
│                                  Auto-loaded by Claude Code every session
│                                  Contains: identity, startup sequence, behavior rules
│                                  Imports SOUL.md + USER.md via @-mention
│
├── SOUL.md                      ← Personality, tone, values
│                                  Who the agent is, how it communicates
│
├── USER.md                      ← About the human using this agent
│                                  Name, role, preferences, timezone, context
│
├── MEMORY.md                    ← Long-term curated memory (human-maintained)
│                                  Significant events, decisions, lessons learned
│
├── TOOLS.md                     ← Integration notes specific to this setup
│                                  API keys locations, service nicknames, etc.
│
├── BOOTSTRAP.md                 ← First-run setup guide
│                                  Walks human through filling USER.md, SOUL.md
│                                  Self-deletes after first run
│
├── memory/                      ← Daily session logs
│   └── YYYY-MM-DD.md            ← Raw notes per day
│
├── .claude/
│   ├── settings.json            ← Shared team config (git-committed)
│   │                              Tool permissions, hook configs
│   │
│   ├── settings.local.json      ← Personal overrides (gitignored)
│   │                              Machine-specific, private settings
│   │
│   ├── skills/                  ← Custom skills = slash commands
│   │   ├── daily/
│   │   │   └── SKILL.md         ← /daily — structured session log
│   │   ├── compress/
│   │   │   └── SKILL.md         ← /compress — distill memory into MEMORY.md
│   │   └── install-skill/
│   │       └── SKILL.md         ← /install-skill — add a new skill
│   │
│   ├── agents/                  ← Custom subagents
│   │   └── researcher.md        ← Deep research agent (read-only, Haiku model)
│   │
│   └── rules/                   ← Scoped rules (lazily loaded)
│       └── memory.md            ← Rules for working with memory files
│
└── .mcp.json                    ← MCP servers (empty placeholder)
                                   Add Notion, GitHub, Slack etc. as needed
```

## What Each File Does

### CLAUDE.md (the brain)
The single file Claude Code reads automatically. Keep it ≤200 lines. Structure:
1. Identity section (who am I — references SOUL.md)
2. About the human (references USER.md)
3. Startup sequence (what to do at session start)
4. Memory instructions (how to read/write memory)
5. Behavior rules (core do's and don'ts)
6. Available skills (brief list)

### SOUL.md
Personality document. Who the agent is, how it speaks, what it values. Opinionated by default — easy for users to customize.

### USER.md
Template with sections: Name, Role, Timezone, Preferences, Important Dates, Context. User fills this in during BOOTSTRAP.

### MEMORY.md
Long-term curated memory. Human writes significant things here. Agent reads it at startup. Periodically distilled from daily memory/ files using `/compress`.

### memory/YYYY-MM-DD.md
Raw daily logs. What happened, what was decided, what was learned. Auto-written by agent during session. Referenced by `/compress` to distill into MEMORY.md.

### .claude/settings.json
```json
{
  "permissions": {
    "allow": ["Bash", "Read", "Write", "Edit"],
    "deny": []
  },
  "hooks": {
    "SessionStart": [
      { "type": "command", "command": "echo 'Session started'" }
    ]
  }
}
```

### .mcp.json (empty starter)
```json
{
  "_comment": "Add MCP servers here. See https://code.claude.com/docs/en/mcp",
  "mcpServers": {}
}
```

## OpenClaw → Claude Code Mapping

| OpenClaw | Claude Code Native |
|---|---|
| AGENTS.md | `CLAUDE.md` |
| Skills system | `.claude/skills/` |
| Plugin integrations | `.mcp.json` MCP servers |
| Cron jobs | `/loop` skill + `SessionStart` hook |
| Heartbeat | `Stop` hook |
| Memory files | `memory/` + MEMORY.md |
| Sub-agents | `.claude/agents/` |
| Identity/Soul | SOUL.md imported by CLAUDE.md |
| Per-user config | `settings.local.json` |

## Default Skills (ships with starter)

| Skill | Command | Purpose |
|-------|---------|---------|
| daily | `/daily` | Start/end of day structured log |
| compress | `/compress` | Distill memory/ into MEMORY.md |
| install-skill | `/install-skill` | Add a new skill from path or URL |

**Not needed (Anthropic provides natively):**
- `/remember` → native `/memory` + auto memory
- `/status` → native `/context`
- `/recap` → just read MEMORY.md
- `/batch`, `/simplify`, `/debug`, `/loop` → bundled skills
