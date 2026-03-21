# Groundwork — Build Prompt for Claude Code

Paste this entire file into Claude Code to start building Groundwork.

---

## What We're Building

**Groundwork** — a portable, personal AI assistant workspace template for Claude Code.

The core idea: a git repo you clone once, open in Claude Code, and instantly have an AI assistant that knows you, remembers your history, and grows with you over time. Like having a persistent personal assistant inside your IDE — not a business tool, not a task manager. A relationship.

The experience we're aiming for: the user asks a question and the agent already knows the context. It knows their name, their work, their goals, their stress triggers, their family. It remembers what happened yesterday and last week. It feels personal, not generic.

---

## Architecture

Build this folder structure:

```
groundwork/
│
├── CLAUDE.md                    ← Master entry point (≤200 lines)
│                                  Auto-loaded every session
│                                  Imports SOUL.md + USER.md
│                                  Contains: startup sequence, behavior rules
│
├── SOUL.md                      ← Agent personality, tone, values
│                                  Opinionated but easy to customize
│
├── USER.md                      ← About the human
│                                  Name, role, timezone, preferences,
│                                  important dates, context
│
├── MEMORY.md                    ← Long-term curated memory
│                                  Significant events, decisions, lessons
│                                  Human-maintained, agent reads at startup
│
├── TOOLS.md                     ← Integration notes (empty by default)
│                                  User fills with API keys, service notes
│
├── BOOTSTRAP.md                 ← First-run setup guide
│                                  Walks user through filling USER.md + SOUL.md
│                                  Ends with instruction to delete itself
│
├── memory/                      ← Daily session logs
│   └── YYYY-MM-DD.md
│
├── .claude/
│   ├── settings.json            ← Tool permissions + hooks config
│   ├── settings.local.json      ← Personal overrides (gitignored)
│   │
│   ├── skills/                  ← 3 default skills only
│   │   ├── daily/
│   │   │   └── SKILL.md         ← /daily — structured session log
│   │   ├── compress/
│   │   │   └── SKILL.md         ← /compress — distill memory/ into MEMORY.md
│   │   └── install-skill/
│   │       └── SKILL.md         ← /install-skill — add a new skill
│   │
│   ├── agents/
│   │   └── researcher.md        ← Read-only research subagent (Haiku model)
│   │
│   └── rules/
│       └── memory.md            ← Rules for working with memory files
│
└── .mcp.json                    ← Empty placeholder with commented examples
```

---

## Key Design Principles

1. **CLAUDE.md ≤200 lines** — use @imports for SOUL.md and USER.md
2. **Native-first** — use Claude Code's built-in features, don't reinvent them
3. **Memory is the product** — daily logs + MEMORY.md + auto memory = what makes this feel personal
4. **3 custom skills only** — /daily, /compress, /install-skill. Everything else is native.
5. **No integrations in starter** — .mcp.json is empty. User adds Notion, GitHub etc. themselves.
6. **BOOTSTRAP.md = onboarding** — first-run conversation that personalizes the agent, then deletes itself

---

## What Anthropic Already Provides (don't rebuild)

- `/memory` — native memory management
- `/compact` — context compression
- `/batch`, `/simplify`, `/debug`, `/loop` — bundled skills
- Auto memory — Claude writes notes itself
- `/agents` — subagent management
- `/init` — CLAUDE.md generation

---

## Reference: ai-os (https://github.com/manselscheffel/ai-os)

Study this as a reference but DO NOT copy the architecture. It's a business OS — Groundwork is a personal assistant. Key things to note from ai-os:
- Their CLAUDE.md is ~80 lines (good)
- They use a Stop hook to auto-capture memories (worth implementing)
- They use a PreToolUse hook to block dangerous commands (worth implementing)
- Their `context/` folder concept is interesting for v2 (skip for v1)

---

## Start Here

1. Create the repo structure above
2. Write CLAUDE.md first — it drives everything
3. Write SOUL.md — make it opinionated and warm, not corporate
4. Write USER.md — template with clear sections the user fills in
5. Write BOOTSTRAP.md — a conversational first-run experience
6. Write the 3 skills
7. Write the researcher subagent
8. Set up .claude/settings.json with Stop hook for auto memory capture
9. Write .gitignore (include settings.local.json, memory/ is optional)
10. Test: clone fresh, open in Claude Code, run BOOTSTRAP, verify it feels personal

---

## The North Star

When the user opens Groundwork for the first time and chats with it, it should feel like meeting someone who is genuinely interested in getting to know them. After a week of use, it should feel like talking to someone who knows them well. That's the bar.
