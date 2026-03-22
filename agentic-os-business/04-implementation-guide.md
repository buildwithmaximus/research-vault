---
title: Implementation Guide — Installing an Agentic OS for a Client
topic: agentic-os-business
date: 2026-03-22
tags: [implementation, client, setup, consulting, installation]
source: Original research — Natan × Maximus
---

# Implementation Guide — Installing an Agentic OS for a Client

## Summary
Step-by-step process for installing a Business Agentic OS for a client company. This is the consultant's playbook.

---

## Phase 1: Discovery (Week 1)

### Goal: Understand the business before touching any files

**1. Business mapping session (90 min)**

Questions to answer:
- What departments exist and how many people in each?
- What are the top 3 time-consuming repetitive tasks per department?
- What tools do they use? (CRM, project management, communication, docs)
- What does a typical workday look like for each department?
- Where are the biggest bottlenecks?
- What does "success" look like in 90 days?

**2. Tool audit**

Build a tool inventory:
```
Department | Tool | Purpose | API Available? | MCP Server Exists?
Marketing  | HubSpot | CRM + Email | Yes | Yes (official)
Marketing  | Canva | Design | Yes | Yes (official)
Sales      | Salesforce | CRM | Yes | Community MCP
Ops        | Monday.com | Project mgmt | Yes | Yes (official)
```

**3. Workflow documentation**

For each department, document:
- 3-5 most important recurring workflows
- Input → Process → Output for each
- Who approves, who reviews, what format output takes

---

## Phase 2: Architecture Design (Week 1-2)

### Goal: Define exactly what we're building

**1. Department scope decision**

Not every company needs all 5 departments in v1. Start with:
- The department with the highest ROI potential
- The most tech-comfortable team
- Usually: Marketing or Operations

**2. Skill list per department**

For each department, define:
- Which Anthropic official plugin to use as base
- Which company-specific skills to add
- Which MCP connectors to configure

**3. CLAUDE.md outline**

Draft the structure before writing:
```
Global CLAUDE.md (200 lines max):
- Company: [name, mission, products, ICP]
- Brand voice: [tone, style, what to avoid]
- Tools: [primary tools list]
- Global rules: [escalation, approval, security]

Department CLAUDE.md (100 lines max each):
- Department focus and current goals
- Key contacts and approvers
- Department-specific tools
- Output formats and standards
```

**4. Architecture doc**

Deliver a 1-page architecture diagram showing:
- All departments in scope
- Skills per department
- MCP connections
- Agent hierarchy
- Scheduled tasks

Client must approve this before any implementation.

---

## Phase 3: Setup (Week 2-3)

### Goal: Build the OS

**Step 1: Initialize the repo**

```bash
mkdir company-agent-os
cd company-agent-os
git init
git remote add origin https://github.com/[client]/agent-os.git
```

**Step 2: Create global structure**

```bash
mkdir -p context memory/logs departments .claude/agents .claude/rules
touch CLAUDE.md .mcp.json .gitignore
```

**.gitignore:**
```
.claude/settings.local.json
.env
memory/logs/       # Optional: keep private
```

**Step 3: Write the global CLAUDE.md**

Template structure:
```markdown
# [Company Name] — Agent OS

@context/company-overview.md
@context/brand-voice.md

## Identity
[2-3 sentences: what the company does, who they serve]

## Brand Voice
[5-10 rules: tone, style, what to avoid]

## Startup Sequence
1. Check if today's log exists at memory/logs/YYYY-MM-DD.md
2. Read MEMORY.md for long-term context
3. Greet with reference to active projects

## Global Rules
- Always ask before external actions (emails, posts, CRM updates)
- Never expose credentials or PII in outputs
- Log significant decisions to memory/logs/YYYY-MM-DD.md
- Escalate to [Name] for approvals above [threshold]

## Available Departments
- /departments/marketing/ — content, campaigns, brand
- /departments/sales/ — outreach, pipeline, proposals
- /departments/operations/ — daily brief, reporting, vendor

## Tool Access
@context/tools-and-systems.md
```

**Step 4: Create department structures**

For each department in scope:
```bash
mkdir -p departments/[dept]/.claude/skills
mkdir -p departments/[dept]/.claude/agents
mkdir -p departments/[dept]/context
mkdir -p departments/[dept]/memory/logs
mkdir -p departments/[dept]/workflows
touch departments/[dept]/CLAUDE.md
```

**Step 5: Install official plugins**

```bash
claude plugin marketplace add anthropics/knowledge-work-plugins
claude plugin install marketing@knowledge-work-plugins
claude plugin install sales@knowledge-work-plugins
```

**Step 6: Configure MCP servers**

Install relevant MCP servers based on tool audit:
```bash
npm install -g @hubspot/mcp-server
npm install -g @notionhq/notion-mcp-server
```

Add to .mcp.json with client's API credentials.

**Step 7: Set up hooks**

Create `.claude/hooks/` directory:

```bash
# session-start.sh — runs at every session start
#!/bin/bash
TODAY=$(date +%Y-%m-%d)
LOG_FILE="memory/logs/$TODAY.md"
if [ ! -f "$LOG_FILE" ]; then
  echo "# Session Log: $TODAY" > "$LOG_FILE"
  echo "## Started: $(date +%H:%M)" >> "$LOG_FILE"
fi
```

```bash
# auto-memory.sh — runs when Claude stops responding
#!/bin/bash
# Trigger Claude to write a session summary
echo "Session ended at $(date +%H:%M)" >> memory/logs/$(date +%Y-%m-%d).md
```

```bash
# safety-check.sh — PreToolUse safety
#!/bin/bash
CMD="$1"
BLOCKED="rm -rf|git push --force|DROP TABLE|DELETE FROM|truncate"
if echo "$CMD" | grep -qE "$BLOCKED"; then
  echo "BLOCKED: Dangerous command detected"
  exit 1
fi
```

**Step 8: Build company-specific skills**

For each workflow identified in discovery, build a SKILL.md:

```markdown
---
name: weekly-marketing-report
description: Generate weekly marketing performance report. Use every Friday or when asked for marketing report.
---

# Weekly Marketing Report

## Process
1. Pull data from HubSpot: email opens, clicks, unsubscribes (last 7 days)
2. Pull content metrics: blog views, social engagement
3. Compare to previous week
4. Write executive summary (3 bullet points max)
5. Format as markdown report
6. Save to context/reports/YYYY-MM-DD-weekly.md

## Output Format
- Title: Weekly Marketing Report — [Date Range]
- Section: Email Performance
- Section: Content Performance  
- Section: Key Wins
- Section: Action Items for Next Week

## Standards
- Data must come from HubSpot MCP, not manual input
- All numbers must cite their source
- Tone: professional, concise, no fluff
```

---

## Phase 4: Testing (Week 3)

### Goal: Verify everything works before handoff

**Testing checklist:**

```
[ ] Global CLAUDE.md loads and is followed
[ ] Each department CLAUDE.md loads in that scope
[ ] Each skill fires correctly when invoked
[ ] Each MCP server connects and returns data
[ ] Scheduled tasks run at correct times
[ ] Session-start hook fires
[ ] Auto-memory hook writes logs
[ ] Safety hook blocks dangerous commands
[ ] Agent team (if enabled) coordinates correctly
[ ] Memory persists across sessions
```

**Test each skill:**
- Run it with sample inputs
- Verify output format matches requirements
- Verify it uses the correct MCP tools
- Check that it writes to the correct log

---

## Phase 5: Training + Handoff (Week 4)

### Goal: Team can operate and maintain the OS independently

**Training sessions (2-4 hours total):**

1. **Overview session (30 min)** — What is this, why it works
2. **Department session per team (45 min each)** — How to use their specific skills
3. **Power user session (60 min)** — How to add new skills, update CLAUDE.md
4. **Admin session (45 min)** — Maintenance, logs, updating context

**Handoff deliverables:**
- Full repo committed to client's GitHub
- Architecture documentation
- Skill reference guide (one page per skill)
- Maintenance runbook (how to add skills, update context, handle errors)

---

## Maintenance (Monthly Retainer)

**What's included:**

- Review session logs monthly → update MEMORY.md
- Add 1-2 new skills per month as team identifies needs
- Update CLAUDE.md when company/product changes
- MCP server updates and troubleshooting
- Slack/Telegram access for questions

**Rate:** 800-2,000 NIS/month depending on scope

---

## Pricing Model

| Item | Price |
|------|-------|
| Discovery + Architecture design | 3,000-5,000 NIS |
| Implementation (1 department) | 8,000-15,000 NIS |
| Each additional department | 4,000-8,000 NIS |
| Training + handoff | Included |
| Monthly maintenance | 1,500-3,000 NIS/month |

**Example: 3 departments (Marketing, Sales, Ops)**
- Setup: 5,000 + 15,000 + 8,000 + 6,000 = 34,000 NIS
- Monthly: 2,500 NIS/month
- Year 1 total: ~64,000 NIS
