# Claude Code For Brands — 101 Guide

> The complete setup guide for founders and creatives who want to generate photorealistic AI content directly from their terminal.

Source: AI Creative Studio (private guide)

---

## What It Is

Most people use AI tools like Gemini, Midjourney, or Kling one at a time — open a tab, write a prompt, download a file, next tab, repeat. For every piece of content: 6 apps, 40 decisions, 90 minutes.

Claude Code removes all of that. It's an AI agent running inside your terminal that chains every tool in your workflow together automatically. You describe what you want once. It runs the whole pipeline.

It can be trained on your brand and remember context across every session. Think of it as a creative director who lives inside your computer: give it your brand guidelines once, teach it your workflows once, and it runs them on demand at consistent quality.

---

## Who This Is For

- Founders, creative directors, heads of social, and creators who want to:
  - Generate photorealistic AI model content for their brand
  - Produce UGC-style video content at scale
  - Create product shots, packshots, and lifestyle imagery without a photographer
  - Build repeatable content workflows that don't depend on any single person

---

## What You'll Need

### 1. Claude Account
Claude Pro subscription or above at claude.ai.

### 2. FAL.ai Account + API Key
All image and video generation runs through FAL.ai. Create an account at fal.ai and generate an API key from your dashboard.

### 3. An IDE (Code Editor)
- **Antigravity** (recommended): Built specifically for Claude Code workflows. Download at antigravity.ai
- **VS Code**: Free, install the Claude Code extension
- **Cursor**: Built on VS Code with AI features

---

## Step 1: Install Claude Code

Open your IDE → Extensions panel → search "Claude Code" → install → sign in with your Claude account.

No terminal commands, no configuration. You'll see a Claude Code panel where you can type instructions and watch it work.

---

## Step 2: Create Your Project Folder

Create a dedicated project folder (e.g. `brand-studio` or `ai-creative`). Open it in your IDE.

```
brand-studio/
  .env                    ← Your FAL.ai key (private, never shared)
  CLAUDE.md               ← Your session instructions for Claude
  brands/
    [your-brand-name]/
      .claude/
        skills/           ← Your skill files live here
```

Claude Code will build this out as you work. You don't need to create it manually.

---

## Step 3: Add Your FAL.ai Key

Create a `.env` file in your project folder:

```
FAL_KEY=your_fal_api_key_here
```

Where to find it: fal.ai → profile icon → Dashboard → API Keys

**Keep `.env` private.** Never share it, commit it to GitHub, or paste it in messages.

### FAL.ai Pricing

| Model | Best Use | Cost |
|---|---|---|
| Nano Banana 2 (edit) | Product shots, model compositing, packshots | ~$0.12/image at 2K |
| Veo 3.1 (image-to-video) | Product UGC video with voice | ~$0.15/s at 1080p |
| Seedance 1.5 Pro | UGC talking-head video | ~$0.05/s at 1080p |
| Kling 3.0 Pro | Motion and animation | ~$0.168/s with audio |

---

## Step 4: CLAUDE.md — The Brain

CLAUDE.md is a plain text file Claude Code reads at the start of every session. It carries permanent context: who you are, what you're building, how you work.

Without it, Claude starts every session fresh. With a good one, it picks up exactly where you left off.

### What Goes In CLAUDE.md

- **Project overview** — what you're building, who it's for
- **Folder structure** — where things live so Claude can navigate without asking
- **Tools and APIs** — which services to use for image, video, voiceover
- **Workflow rules** — conventions (versioning logic, naming, what not to modify)
- **Brand context** — visual guidelines, tone of voice, product range

### Starter Template

```markdown
# AI Brand Studio: Session Context

## Project
AI content generation pipeline for [brand name].
All outputs saved under brands/[brand-name]/.

## Tools
- Image generation: FAL.ai (Nano Banana 2)
- Video generation: FAL.ai (Veo 3.1, Seedance 1.5 Pro, Kling 3.0 Pro)
- API keys loaded from .env

## Folder conventions
- Always create output folders before sharing file paths
- Image naming: [output-name].png for first generation, rename to _v1 on regeneration
- Video naming: [image-stem]-run[n].mp4

## Workflow rules
- Never modify script files unless explicitly asked
- Run scripts from the project root, not from subfolders
- Check for existing spec files before creating new ones

## Brand
[Paste a short summary of your brand here]
```

Update CLAUDE.md every time you establish a new convention or catch a mistake. It gets better the more you use it.

---

## Step 5: Skill Files — The Cheat Codes

A Skill is a saved, reusable workflow stored as a Markdown file:

```
.claude/skills/[skill-name]/SKILL.md
```

Type `/ugc` or `/packshot` → Claude reads the skill file → follows it step by step → asks the right questions → calls the right API → saves the output exactly where it should go.

### Why Skills Are the Whole Game

Without skills, Claude is improvising every session. You re-explain the workflow every time: folder structure, which API, parameters, where to save, what to do when output is wrong.

With skills, every decision you've already made is baked in permanently. Same folder structure, same versioning logic, same prompt architecture, same API call. Consistent output because consistent instructions.

One well-built skill file can replace hours of prompting across an entire campaign.

### What Makes a Good Brand Skill

Getting consistent, high-quality photorealistic outputs requires understanding:
- Which FAL model to use for which task, and why
- How to structure image references (identity vs. product accuracy vs. composition)
- What prompt architecture produces reliable results vs. hallucinations
- How to handle versioning so iterations don't overwrite each other
- How video models interpret prompts differently from image models

---

## Step 6: Set Up Your Brand

Before running creative workflows, give Claude Code your brand context:
- A visual guidelines file (aesthetic, colour palette, photography style, tone)
- A product catalogue (JSON) with image URLs
- A folder of product images as references

---

## Full Skill Pipeline (AI Creative Studio)

Pre-built skill files for:
- **Creating models**: Photorealistic AI models with consistent identity
- **Styling**: Dressing models in your product without a photographer
- **Clothing shoots**: Models in locations, environments, lifestyle scenes
- **Packshots**: Clean commercial studio product photography
- **Product shots**: Lifestyle and context imagery
- **Model product shoots**: Editorial and campaign shots
- **UGC video**: Talking-head creator content at scale
- **Product UGC**: Creator-to-camera with model holding your product
- **Add motion**: Animating still images into short clips

---

## Key Takeaways

1. Claude Code + FAL.ai = full brand content pipeline from terminal
2. CLAUDE.md = persistent brand memory across every session
3. Skill files = reusable workflows that run consistently without re-explaining
4. `.env` = keep your FAL key here, never commit to git
5. Start simple: install, set up CLAUDE.md, run one workflow, add skills over time
