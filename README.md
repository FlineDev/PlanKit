# PlanKit

**Ideas → Roadmap → Steps** — a planning plugin for [Claude Code](https://docs.anthropic.com/en/docs/claude-code)

PlanKit is a 3-level planning system designed for indie and solo developers. It captures your project ideas, triages them into versioned roadmaps, and breaks features down into implementation-ready steps — all as markdown files that live in your project repository and can be committed alongside your code.

> Everything stays in one place: your plans, your roadmap, and your code — all in the same repo.

## How It Works

| Level | What | Detail Level | Example |
|-------|------|-------------|---------|
| **Ideas** | Raw ideas, improvements, wishes | Problem + motivation + examples | "Visual streak calendar like GitHub contributions" |
| **Roadmap** | Features grouped into versions | + Key decisions + open questions | "v2.0: Streak System — heatmap grid, 12-month scroll, freeze support" |
| **Steps** | Implementation-ready task files | Full requirements, design-first | `001-DesignStreakCalendar.md` with data model, layout spec, edge cases |

Content flows forward through the pipeline: **Ideas → Roadmap → Steps → Done**. Every link, example, and detail is preserved at each stage.

## Installation

Start Claude Code, then run:

```
/plugin marketplace add FlineDev/Marketplace
```

```
/plugin install plan-kit
```

```
/plan-kit:init
```

PlanKit is part of the [FlineDev Marketplace](https://github.com/FlineDev/Marketplace) — see the full list of available plugins there.

> [!TIP]
> **Automatic Updates:** By default, third-party plugins don't auto-update. To receive new features and fixes:
> 1. Type `/plugin` and press Enter
> 2. Switch to the **Marketplaces** tab
> 3. Navigate to **FlineDev** and press Enter
> 4. Press Enter on **Enable auto-update**

## Commands

| Command | Purpose |
|---------|---------|
| `/plan-kit:init` | First-time setup, convention detection, migration |
| `/plan-kit:capture-idea` | Capture a new idea (or brain-dump several) |
| `/plan-kit:plan-roadmap` | Triage ideas into a versioned release plan |
| `/plan-kit:define-steps` | Break a roadmap feature into implementation steps |

You don't need to use commands explicitly. PlanKit's skills activate automatically when you discuss relevant topics:

- **"I have an idea for..."** → captures to Ideas
- **"Let's plan the next version"** → starts roadmap triage
- **"How should we implement the streak feature?"** → creates step breakdown
- **"What's our status?"** → shows the project dashboard

## File Structure

PlanKit creates a planning folder in your project root:

```
PlanKit/
├── .config.json
├── Ideas.md
├── Roadmap.md
├── Progress.md          ← created when first version is planned
└── Features/
    ├── 001-StreakSystem/
    │   ├── 001-DesignStreakCalendar.md
    │   ├── 002-ImplementStreakLogic.md
    │   └── 003-ValidateEdgeCases.md
    └── 002-SmartReminders.md
```

The naming convention is auto-detected during `/plan-kit:init` — Swift/Apple projects get UpperCamelCase (shown above), while JS/web projects get kebab-case (`plan-kit/`, `ideas.md`, etc.). The convention is stored in `.config.json`.

## Detail Levels — A Concrete Example

### Level 1: Idea

```markdown
## Streaks & Motivation

### Visual Streak Calendar
The problem: users complete habits but don't feel progress.
A visual calendar (like GitHub's contribution graph) could provide
"don't break the chain" motivation.

See [Streaks app](https://streaksapp.com) for a minimal approach.

Possible approaches:
- Heatmap grid (intensity = habits completed)
- Simple chain dots (connected when streak continues)
```

### Level 2: Roadmap Feature

```markdown
## v2.0 — Streaks & Social

### Streak System

Visual streak tracking with a GitHub-style heatmap calendar...

**Key decisions:**
- Heatmap grid, not chain dots (richer data, more satisfying)
- Intensity = percentage of daily habits completed
- Show 12 months of history, scrollable

**Open questions:**
- Should partial completion count as a streak day?
```

### Level 3: Implementation Step

```markdown
# Design Streak Calendar

Feature: Streak System (from Roadmap v2.0 — Streaks & Social)

⚠️ Created 2026-02-19. Review current codebase before implementing.

## Heatmap Calendar Layout

12-month scrollable view, current month on the right. Grid: 7 rows
(days of week) × ~52 columns (weeks).

Color intensity based on completion percentage:
- 0% = empty (no color)
- 1-49% = light shade
- 50-99% = medium shade
- 100% = full intensity
```

Each level adds more specificity. Ideas capture the "what if", the roadmap adds "what exactly", and steps define "how specifically".

## Lifecycle

The dashboard (Progress.md) updates automatically throughout all stages — no separate command needed.

### Ideas → Roadmap (Extraction)
When an idea is selected for the roadmap during `/plan-kit:plan-roadmap`, it is **moved** (not copied) from the Ideas file to the Roadmap. All content is preserved and enriched with key decisions from the triage conversation. Progress.md is created with the new version and its features.

### Steps → Completion
When a step is done, PlanKit suggests deleting the step file and updates Progress.md. When all steps for a feature are complete, the feature folder is cleaned up and the roadmap feature is marked with a checkmark.

### Version Release
When a version ships, completed features are slimmed to just names in both the Roadmap and Progress.md. Any unfinished features carry over to the next version automatically.

## Auto-Split

When Ideas or Roadmap files grow beyond 5,000 words (configurable in `.config.json`), PlanKit automatically splits them into a directory of topic files with an `Index.md` (or `index.md` for kebab-case) overview. This keeps individual files manageable while preserving all content.

## Philosophy

- **Zero friction capture** — ideas should be as easy to record as saying them out loud
- **Preserve all detail** — never summarize or shorten user input; links, examples, and motivation matter
- **Progressive refinement** — each level adds specificity, nothing is lost along the way
- **Requirements, not code** — plans describe what to build and why, not implementation details
- **Design before implementation** — thinking about design is always a separate step from coding
- **Session-sized steps** — each step should be completable in one focused working session
- **Always current** — freshness warnings remind you to check the codebase before implementing

## License

MIT
