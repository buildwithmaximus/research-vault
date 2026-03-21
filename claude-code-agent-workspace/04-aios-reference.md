---
title: ai-os Reference — Comparison & Learnings
topic: claude-code-agent-workspace
date: 2026-03-21
tags: [reference, ai-os, comparison, architecture]
source: https://github.com/Natan-Yago/AIOS (fork of manselscheffel/ai-os)
---

# ai-os Reference

## What It Is
An open-source Claude Code workspace template focused on turning Claude Code into a **business operating system**. 16 skills, 3 subagents, SQLite task tracking, optional vector memory.

## Their Architecture
```
ai-os/
├── CLAUDE.md          # Kernel (~80 lines)
├── setup.sh           # One-command setup wizard
├── .claude/
│   ├── skills/        # 16 skills
│   ├── agents/        # 3 subagents
│   └── rules/         # Modular rules
├── context/           # Business knowledge (filled by wizard)
├── args/              # Runtime preferences
├── memory/            # MEMORY.md + daily logs
├── data/              # SQLite databases
├── hooks/             # Automation hooks (Stop, PreToolUse, PostToolUse)
└── docs/              # Guides
```

## What They Do Well
- CLAUDE.md ~80 lines — matches our ≤200 line target
- `context/` folder — cleaner than a single USER.md for structured business knowledge
- `hooks/` at root — visible, editable, not buried in .claude/
- Stop hook auto-captures memories after every response
- PreToolUse hook blocks dangerous commands (rm -rf, force-push)
- Setup wizard via setup.sh + business-setup skill
- "Separation of concerns: AI reasons about WHAT. Scripts handle HOW."

## Key Difference From Groundwork
ai-os = **business operating system** — workflows, tasks, pipelines, automation
Groundwork = **personal AI assistant** — relationship, continuity, knowing the user deeply

## What We Borrow (for consideration in v2)
- `context/` folder concept — `context/me.md`, `context/work.md`, `context/voice.md`
- `hooks/` at root level — more visible than `.claude/hooks/`
- Stop hook for auto memory capture

## What We Don't Borrow (intentionally)
- SQLite task management — too heavy for personal assistant focus
- 16 skills out of the box — we stay lean, 3 default skills only
- Business-setup wizard — we use BOOTSTRAP.md conversation instead
- Vector memory — overkill for v1

## Decision
**Don't change Groundwork architecture based on ai-os.** The experience is different:
- ai-os: power user, business workflows, automation
- Groundwork: personal assistant that knows you deeply, remembers your life, feels like the relationship Natan has with Maximus on OpenClaw

The memory system (not task automation) is what makes Groundwork special.
