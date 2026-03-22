---
title: Department Folder Structure — Production Architecture
topic: agentic-os-business
date: 2026-03-22
tags: [folder-structure, departments, claude-code, cowork, production]
source: Original research + Anthropic docs + mindstudio.ai
---

# Department Folder Structure — Production Architecture

## Summary
The canonical production folder structure for a business Agentic OS built on Claude Code. Each department is an isolated workspace with its own CLAUDE.md, skills, agents, memory, and MCP connections.

---

## Top-Level Structure

```
company-agent-os/
│
├── CLAUDE.md                      ← Global company context (≤200 lines)
│                                    Company identity, brand voice, global SOPs
│                                    @imports department-specific files as needed
│
├── context/                       ← Company knowledge base
│   ├── brand-voice.md             ← Tone, style, what to avoid
│   ├── company-overview.md        ← Mission, products, customers, positioning
│   ├── team-structure.md          ← Who does what, approval chains
│   └── tools-and-systems.md       ← All tools in use, credentials guide
│
├── memory/                        ← Persistent memory system
│   ├── MEMORY.md                  ← Long-term curated institutional memory
│   └── logs/
│       └── YYYY-MM-DD.md          ← Daily session logs (auto-written)
│
├── departments/                   ← One folder per department
│   ├── marketing/
│   ├── sales/
│   ├── operations/
│   ├── product/
│   └── support/
│
├── .claude/
│   ├── settings.json              ← Global permissions, hooks, team config
│   ├── settings.local.json        ← Personal overrides (gitignored)
│   ├── agents/                    ← Shared subagents available to all depts
│   │   ├── researcher.md          ← Read-only research agent (Haiku)
│   │   └── reviewer.md            ← Quality review agent
│   └── rules/
│       └── global-safety.md       ← Rules applied everywhere
│
└── .mcp.json                      ← MCP server connections (per company)
```

---

## Department Folder Structure

Each department is a self-contained agent workspace:

```
departments/marketing/
│
├── CLAUDE.md                      ← Marketing-specific context
│                                    Overrides global CLAUDE.md for this scope
│                                    Campaign goals, KPIs, active projects
│                                    Target audience details, competitor notes
│
├── context/                       ← Marketing knowledge
│   ├── brand-guidelines.md        ← Full brand guide (referenced, not in CLAUDE.md)
│   ├── content-calendar.md        ← Current quarter calendar
│   ├── personas.md                ← Customer personas
│   └── competitors.md             ← Competitive landscape
│
├── memory/
│   └── logs/
│       └── YYYY-MM-DD.md          ← Marketing-specific session log
│
├── .claude/
│   ├── skills/                    ← Marketing skills (slash commands)
│   │   ├── content-brief/
│   │   │   ├── SKILL.md
│   │   │   └── references/brief-examples.md
│   │   ├── campaign-launch/
│   │   │   ├── SKILL.md
│   │   │   └── scripts/launch-checklist.py
│   │   ├── competitor-analysis/
│   │   │   └── SKILL.md
│   │   └── newsletter/
│   │       ├── SKILL.md
│   │       └── assets/newsletter-template.html
│   │
│   └── agents/
│       └── content-writer.md      ← Specialized content agent with brand voice
│
└── workflows/
    ├── content-pipeline.md        ← Research → Brief → Draft → Review → Publish
    └── campaign-launch.md         ← Plan → Create → Review → Schedule → Report
```

---

## All Five Department Structures

### Marketing Department
```
departments/marketing/
├── CLAUDE.md               ← Brand voice, campaigns, audience, KPIs
├── context/                ← Brand guide, personas, competitors, calendar
├── .claude/skills/
│   ├── content-brief/
│   ├── newsletter/
│   ├── campaign-launch/
│   ├── competitor-analysis/
│   └── social-post/
├── .claude/agents/
│   └── content-writer.md   ← Sonnet, brand-voice specialized
└── workflows/
    └── content-pipeline.md
```
**Plugin:** `marketing@knowledge-work-plugins`
**MCP:** HubSpot, Canva, Figma, Ahrefs, Klaviyo

---

### Sales Department
```
departments/sales/
├── CLAUDE.md               ← ICP, sales process, objection handling, pricing
├── context/                ← Product details, case studies, battlecards
├── .claude/skills/
│   ├── research-prospect/
│   ├── call-prep/
│   ├── outreach-draft/
│   ├── pipeline-review/
│   └── proposal-builder/
├── .claude/agents/
│   └── prospect-researcher.md  ← Haiku, read-only, LinkedIn + web
└── workflows/
    └── lead-to-close.md
```
**Plugin:** `sales@knowledge-work-plugins`
**MCP:** HubSpot, Clay, ZoomInfo, Fireflies, Slack

---

### Operations Department
```
departments/operations/
├── CLAUDE.md               ← Processes, SLAs, escalation paths
├── context/                ← SOPs, vendor contacts, compliance docs
├── .claude/skills/
│   ├── daily-brief/        ← Morning summary of all activity
│   ├── weekly-report/
│   ├── incident-response/
│   └── vendor-comms/
├── .claude/agents/
│   └── ops-monitor.md      ← Scheduled, checks systems
└── workflows/
    └── daily-ops.md        ← Morning brief → task triage → EOD summary
```
**Plugin:** `productivity@knowledge-work-plugins`
**MCP:** Slack, Notion, Asana, Monday, Microsoft 365

---

### Product Management Department
```
departments/product/
├── CLAUDE.md               ← Product vision, roadmap, current sprint
├── context/                ← User research, feature specs, metrics
├── .claude/skills/
│   ├── write-spec/
│   ├── user-research-synthesis/
│   ├── roadmap-update/
│   └── stakeholder-update/
├── .claude/agents/
│   └── researcher.md       ← User interviews, market analysis
└── workflows/
    └── discovery-to-spec.md
```
**Plugin:** `product-management@knowledge-work-plugins`
**MCP:** Linear, Figma, Amplitude, Pendo, Jira

---

### Customer Support Department
```
departments/support/
├── CLAUDE.md               ← Support policies, escalation criteria, tone
├── context/                ← FAQ, known issues, product docs
├── .claude/skills/
│   ├── triage-ticket/
│   ├── draft-response/
│   ├── escalation-package/
│   └── kb-article/         ← Turn resolved issue into KB article
├── .claude/agents/
│   └── support-agent.md    ← Empathetic tone, product-aware
└── workflows/
    └── ticket-resolution.md
```
**Plugin:** `customer-support@knowledge-work-plugins`
**MCP:** Intercom, HubSpot, Guru, Jira, Slack

---

## The .claude/settings.json (Global)

```json
{
  "permissions": {
    "allow": ["Bash", "Read", "Write", "Edit", "WebSearch"],
    "deny": ["Bash(rm -rf*)", "Bash(*--force*)"]
  },
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "hooks": {
    "SessionStart": [{
      "type": "command",
      "command": "bash .claude/hooks/session-start.sh"
    }],
    "Stop": [{
      "type": "command", 
      "command": "bash .claude/hooks/auto-memory.sh"
    }],
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "bash .claude/hooks/safety-check.sh"
      }]
    }]
  }
}
```

---

## The .mcp.json Template

```json
{
  "_instructions": "Add your company's MCP servers here. Each department uses the tools relevant to it.",
  "mcpServers": {
    "hubspot": {
      "command": "npx",
      "args": ["-y", "@hubspot/mcp-server"],
      "env": { "HUBSPOT_ACCESS_TOKEN": "${HUBSPOT_TOKEN}" }
    },
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": { "NOTION_TOKEN": "${NOTION_TOKEN}" }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@slack/mcp-server"],
      "env": { "SLACK_TOKEN": "${SLACK_TOKEN}" }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "${GITHUB_TOKEN}" }
    }
  }
}
```
