---
title: The Five-Layer Agentic OS Architecture
topic: agentic-os-business
date: 2026-03-22
tags: [architecture, layers, CLAUDE.md, memory, skills, orchestration]
source: mindstudio.ai/blog/agentic-business-os-claude-code-architecture-guide
---

# The Five-Layer Agentic OS Architecture

## Summary
An Agentic Business OS has exactly five layers. Skip any one and the system becomes unreliable. This is the production-ready blueprint.

---

## Layer 1: Brand Context Layer (The Brain)

**What it is:** The persistent identity and knowledge base every agent draws from.

**Primary mechanism: CLAUDE.md**

The CLAUDE.md file is the institutional memory of the entire operation. It's read automatically at the start of every session by every agent. For a business OS, it contains:

- **Company identity** — mission, products, target customers, positioning
- **Brand voice and tone** — how the company communicates, what to avoid
- **Standard operating procedures** — step-by-step flows for recurring tasks
- **Decision frameworks** — when to escalate, what approval thresholds exist
- **Tool configurations** — which integrations are available, endpoints to reference
- **Output standards** — format requirements, naming conventions, file structure

**Rule:** Keep CLAUDE.md under ~200 lines. Use @imports to reference larger documents.

**Supporting knowledge store:**

Not everything belongs in CLAUDE.md. Large reference documents (product catalogs, support articles, historical data) live in:
- A `/context` folder (static files Claude reads on demand)
- A `/knowledge` directory (organized reference material)
- A vector database (Pinecone, Supabase pgvector) for semantic search at scale

**Hierarchical context:**
```
/root CLAUDE.md          ← global company context
/marketing/CLAUDE.md     ← marketing-specific overrides
/sales/CLAUDE.md         ← sales-specific overrides
/engineering/CLAUDE.md   ← engineering-specific overrides
```
Child directory CLAUDE.md files override parent instructions for that scope.

---

## Layer 2: Memory Architecture

**What it is:** How agents remember, retrieve, and update information across sessions.

### Four Memory Types

| Type | Where It Lives | What It Contains | Speed |
|------|---------------|-----------------|-------|
| **Working** | Context window | Current task, active documents | Instant (temporary) |
| **Declarative** | CLAUDE.md, knowledge store | Facts, SOPs, brand guidelines | Fast (retrieved) |
| **Episodic** | `/logs/YYYY-MM-DD.md` | Past runs, decisions, outputs, errors | Query on demand |
| **Procedural** | Skills + workflow scripts | How-to routines, encoded processes | Embedded in agents |

### Practical Memory Stack (Production)

```
memory/
├── MEMORY.md              ← Long-term curated facts
├── logs/
│   └── YYYY-MM-DD.md      ← Raw daily session logs
└── auto/                  ← Claude's auto-captured memory
```

**Auto memory:** Claude Code natively writes notes from corrections and preferences. These are loaded at session start (first 200 lines). Don't disable this — it's free institutional learning.

**Episodic memory pattern:**
Every agent appends a log entry after completing significant tasks:
```
2026-03-22 14:32 | marketing-agent | Drafted Q1 newsletter | Status: approved | Notes: client prefers shorter subject lines
```

Over time, this log becomes queryable history. Agents check it before re-deriving decisions.

---

## Layer 3: Skill Layer

**What it is:** The tools and capabilities agents can call to take action.

### What a Skill Is

A skill is a modular, reusable instruction set that enables Claude to perform a specific workflow consistently. Think: an employee's SOP document, except Claude reads it and executes it.

**Skill types:**
- **Tool-based:** Calls a specific function (search web, send email, update CRM)
- **Prompt-based:** Specialized sub-agent handles a narrow task with its own system prompt
- **Workflow-based:** Multi-step sequence producing a specific output

**Skill file structure:**
```
skills/
└── research-lead/
    ├── SKILL.md           ← Instructions + description (triggers auto-activation)
    ├── scripts/           ← Executable Python/bash scripts
    ├── references/        ← Reference documents agent reads during execution
    └── assets/            ← Templates, examples, output formats
```

### Official Anthropic Department Plugins

Anthropic open-sourced 11 production-ready department plugins at `github.com/anthropics/knowledge-work-plugins`:

| Plugin | Key Skills | MCP Connectors |
|--------|-----------|----------------|
| **productivity** | Task management, calendar, daily briefing | Slack, Notion, Asana, Linear, Jira, Monday |
| **sales** | Lead research, pipeline review, outreach drafts, call prep, battlecards | HubSpot, Clay, ZoomInfo, Fireflies, Slack |
| **customer-support** | Ticket triage, response drafting, escalation packages, KB articles | Intercom, HubSpot, Guru, Jira, Slack |
| **product-management** | Specs, roadmaps, user research synthesis, stakeholder updates | Linear, Figma, Amplitude, Pendo, Jira |
| **marketing** | Content drafting, campaign planning, brand voice, competitor briefs, reporting | HubSpot, Canva, Figma, Ahrefs, Klaviyo, Slack |
| **legal** | Contract review, NDA triage, compliance navigation, risk assessment | Box, Egnyte, Jira, Microsoft 365 |
| **finance** | Journal entries, reconciliation, financial statements, variance analysis | Snowflake, Databricks, BigQuery, Slack |
| **data** | SQL queries, visualization, statistical analysis, dashboard building | Snowflake, BigQuery, Hex, Amplitude |
| **enterprise-search** | Cross-tool search (email, chat, docs, wikis) | Slack, Notion, Guru, Jira, Microsoft 365 |
| **bio-research** | Literature search, genomics analysis, target prioritization | PubMed, bioRxiv, ClinicalTrials.gov |
| **cowork-plugin-management** | Create/customize new plugins | — |

**Install via Claude Code:**
```bash
claude plugin marketplace add anthropics/knowledge-work-plugins
claude plugin install marketing@knowledge-work-plugins
```

### Skill Chaining (Workflow-Based Skills)

Skills can chain together into full business workflows:

**Example: Content Marketing Pipeline**
```
research-topic → generate-brief → write-draft → check-brand-voice → review-SEO → schedule-publish
```
Each step is a skill. The orchestrator chains them. Output of step N becomes input to step N+1.

**Implementation pattern:**
```
workflows/
└── content-pipeline/
    ├── SKILL.md           ← Describes the full chain
    └── steps/
        ├── 01-research.md
        ├── 02-brief.md
        ├── 03-draft.md
        └── 04-review.md
```

---

## Layer 4: Orchestration Layer

**What it is:** How multiple agents coordinate, delegate, and hand off work.

### Single Agent vs Subagents vs Agent Teams

| Mode | Use When | Token Cost |
|------|----------|-----------|
| **Single agent** | Sequential tasks, simple workflows | Low |
| **Subagents** | Parallel tasks that only report results back | Medium |
| **Agent teams** | Complex work requiring agents to discuss and coordinate | High |

### Subagent Pattern (Most Common)

Main orchestrator agent spawns specialized subagents:

```
Orchestrator (Opus)
├── Researcher (Haiku) — read-only, web search
├── Writer (Sonnet) — content generation  
├── Reviewer (Sonnet) — quality check
└── Publisher (Haiku) — format and deliver
```

Each subagent has:
- Own context window (prevents context rot)
- Specific tool access (researcher can't write, writer can't publish)
- Own model choice (Haiku for fast/cheap, Opus for complex reasoning)

**Configuration (.claude/agents/researcher.md):**
```markdown
---
name: researcher
description: Read-only research agent. Use for web search, document analysis, competitive intelligence.
model: claude-haiku-4-5
tools: [web_search, read_file]
---
You are a focused research agent. Your only job is to find information and return structured summaries. Never write to files. Never take actions.
```

### Agent Teams (Experimental, High Value)

Agent teams let multiple Claude Code instances work in parallel with direct communication:

- Team lead coordinates + assigns tasks
- Teammates work independently, each in own context
- Teammates can message each other directly (not just report to lead)
- Shared task list with self-coordination

**Enable in settings.json:**
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

**Best for:**
- Research from multiple angles simultaneously
- Different teams owning separate modules
- Cross-functional work (marketing + sales + ops)

---

## Layer 5: Self-Maintenance Layer

**What it is:** How the system monitors itself and improves over time.

### Required Components

**1. Retry logic**
```bash
# hooks/post-tool-failure.sh
MAX_RETRIES=3
BACKOFF=5
# Exponential backoff on API failures
```

**2. Output validation**
Before passing output to next step, validate:
- Format matches expected schema
- Required fields are present
- Quality threshold met (word count, sentiment, etc.)

**3. Learning loop**
```
Task completed → Output evaluated → Feedback captured → System updated
```
Update targets:
- Skill SKILL.md files (improve instructions)
- Knowledge base (correct facts)
- Routing logic (better agent selection)

**4. Health checks**
Periodic verification that MCP servers and external services are reachable.

**5. Error logging**
```
logs/errors/YYYY-MM-DD.md ← Failed runs, error messages, resolution notes
```

**6. PreToolUse safety hook**
Block dangerous commands before execution:
```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "bash /hooks/safety-check.sh"
      }]
    }]
  }
}
```
Block: `rm -rf`, `git push --force`, `DROP TABLE`, credential exposure
