---
title: Agentic OS for Business — Key Findings
topic: agentic-os-business
date: 2026-03-22
tags: [findings, decisions, architecture, implementation]
source: Original research — Natan × Maximus
---

# Key Findings

## TL;DR
Build a 5-layer Business Agentic OS on Claude Code. Each department gets its own isolated workspace with CLAUDE.md, skills, MCP connections, and agents. Anthropic already built the official department plugins — our job is installing, configuring, and customizing them for each client.

## Top Findings

### 1. Anthropic already built the department plugins
11 official open-source plugins at `github.com/anthropics/knowledge-work-plugins`: Marketing, Sales, Support, Product, Operations, Finance, Data, Legal. This dramatically reduces build time. We configure and customize — we don't build from scratch.

### 2. The 5-layer architecture is non-negotiable
Brand context → Memory → Skills → Orchestration → Self-maintenance. Skip any layer and the system degrades. All five must be present for a production-ready OS.

### 3. CLAUDE.md is the institutional brain — keep it ≤200 lines
Everything flows through CLAUDE.md. Global one at root, department-specific ones in each folder. Hierarchical — child overrides parent. Use @imports to keep it lean.

### 4. Memory is what makes it feel intelligent
Without memory, it's just a fancy prompt. With it (daily logs + MEMORY.md + auto memory), the system compounds knowledge over time. Every session makes the next one smarter.

### 5. Scheduled tasks are the autonomous layer
`/loop` command + `SessionStart` hooks = agents that work while the human is away. Morning brief, lead research, reports — all autonomous.

### 6. Safety hooks are mandatory for client deployments
PreToolUse hook blocking `rm -rf`, force pushes, SQL drops. Non-negotiable before giving any client an agent with write access.

### 7. Agent teams are powerful but experimental
Enable with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Best for cross-functional work. Higher token cost. Use subagents for most cases, agent teams for complex multi-department workflows.

### 8. MCP is the integration layer
Everything connects via MCP. HubSpot, Notion, Slack, GitHub — all have official MCP servers. Custom tools can be wrapped in MCP. This is the extensibility mechanism.

## Architecture Summary

```
company-agent-os/
├── CLAUDE.md                    ← Global brain (≤200 lines)
├── context/                     ← Company knowledge
├── memory/                      ← Logs + MEMORY.md
├── departments/
│   ├── marketing/               ← Own CLAUDE.md + skills + agents
│   ├── sales/
│   ├── operations/
│   ├── product/
│   └── support/
├── .claude/
│   ├── settings.json            ← Hooks + permissions
│   ├── agents/                  ← Shared subagents
│   └── rules/                   ← Global safety rules
└── .mcp.json                    ← All tool connections
```

## Decisions Made
| Decision | Rationale |
|----------|-----------|
| Claude Code over Cowork | Full power, no MCP limitations |
| Official plugins as base | Don't rebuild what Anthropic already built |
| One repo per company | Single source of truth, version controlled |
| Department folders with own CLAUDE.md | Isolation + context hierarchy |
| Monthly retainer model | Recurring revenue + ongoing value |

## Action Items
- [ ] Build a template "starter OS" repo on buildwithmaximus/company-agent-os
- [ ] Test Anthropic official plugins locally
- [ ] Build the marketing department as a demo/proof of concept
- [ ] Write a sales deck for the service
- [ ] Identify first 3 Israeli companies to approach

## Open Questions
- How do we handle multi-user access? (multiple team members using same OS)
- What's the right model mix per department? (Haiku vs Sonnet vs Opus per role)
- How do we handle Israeli tools not covered by official MCP servers?
- Data residency concerns for enterprise clients?
