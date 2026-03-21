---
title: Claude Code Agent Workspace — Overview
topic: claude-code-agent-workspace
date: 2026-03-21
tags: [claude-code, agent, architecture, local, team]
source: Original research — Natan × Maximus
---

# Claude Code Agent Workspace — Overview

## Summary
A portable, open-source agent workspace template — a folder you clone once, open in any AI coding assistant (Claude Code, Cursor, Antigravity, Windsurf), and instantly have a fully configured, capable agent running locally. No VPS, no gateway, no infrastructure. The folder *is* the agent.

## The Problem It Solves
OpenClaw solved persistent AI assistants for cloud/messaging (Telegram, Discord). This solves it for **local IDE-first individuals and teams**.

- No VPS setup, no gateway, no Tailscale
- Each person clones a repo → opens in Claude Code → working agent in 5 minutes
- Memory is local and private per instance
- Skills are portable — shared via git
- Integrations (Notion, GitHub, Slack) added by each person as needed

## Target User
Technical individuals and small teams — MarTech, product, engineering — who want a capable, persistent AI assistant without infrastructure overhead. Each team member gets their own instance with their own identity and memory.

## Core Concept
The "distribution unit" is a **git repository**. Teams share agents like they share codebases. One clone = one agent. Personal memory stays local. Skills and behavior rules are shared via git.

## Key Design Decisions
- **Personal, not shared** — each person owns their instance
- **Local memory** — memory files live in the repo, private per clone
- **No baked-in integrations** — Notion, GitHub etc. are add-on skills, not defaults
- **Native-first** — built on Claude Code primitives, not workarounds
- **Lean CLAUDE.md** — ≤200 lines, imports other files rather than growing large

## Open Questions
- Name for the project (no decision yet)
- Whether to publish as a GitHub template repo
- Skill marketplace approach (none planned yet — each codebase ships its own)
