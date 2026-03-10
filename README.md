# 📋 PlanKit

**Ideas → Roadmap → Steps → Done** — a planning plugin for [Claude Code](https://docs.anthropic.com/en/docs/claude-code)

PlanKit is a 3-level planning system designed for indie and solo developers. It captures your project ideas, triages them into versioned roadmaps, and breaks features down into implementation-ready steps — all as markdown files that live in your project repository and can be committed alongside your code.

> Everything stays in one place: your plans, your roadmap, and your code — all in the same repo.

## How It Works

| Level | What | You provide | Claude does | Example |
|-------|------|-------------|-------------|---------|
| **Ideas** | Raw ideas, wishes, research | Describe what you want, share links | Organizes into themed groups, preserves every detail | [Ideas.md](examples/Ideas.md) |
| **Roadmap** | Features grouped into versions | Answer clarifying questions, decide scope | Investigates codebase, proposes features, records decisions | [Roadmap.md](examples/Roadmap.md) |
| **Steps** | Implementation-ready task files | Approve breakdown, discuss approach | Reads codebase, creates requirements-level step files | [A-DesignStreakCalendar.md](examples/A-DesignStreakCalendar.md) |
| **Done** | Archive of completed work | Confirm completion, provide reasons for skips | Records status, git SHAs for recovery, PR references | [Done.md](examples/Done.md) |

Content flows forward through the pipeline: **Ideas → Roadmap → Steps → Done**. Every link, example, and detail is preserved at each stage. A [Progress.md](examples/Progress.md) dashboard tracks the current state across all levels.

## You Stay in Control

PlanKit is collaborative, not autonomous. Claude guides the process but you make every important decision:

- **Ideas**: Claude captures exactly what you say — no filtering, no judgment. You describe the idea, Claude organizes it.
- **Roadmap**: Claude investigates your codebase first, then asks targeted questions grounded in what it found ("Your notification system already uses local notifications — should we build on that, or switch to push?"). You decide which ideas make it into which version, what the scope is, and how to resolve open questions.
- **Steps**: Claude proposes a breakdown and discusses it with you before creating any files. You approve or adjust the plan. Step files describe *what* to build (requirements), not *how* to build it (code).
- **Skipping & Dropping**: You decide when to skip a feature (never started) or drop one (started but abandoned). Claude records the reason in Done.md so the context isn't lost.

At no point does Claude silently generate plans or make decisions for you. Every triage, scope decision, and step breakdown goes through a conversation first.

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
├── Done.md              ← created when first work is completed
└── Features/
    ├── 005-StreakSystem/
    │   ├── A-DesignStreakCalendar.md
    │   ├── B-ImplementStreakLogic.md
    │   └── C-ValidateEdgeCases.md
    └── 006-SmartReminders.md
```

Features use globally unique 3-digit numbers (never reused, even after deletion). Steps use letters (A–Z). This creates clean cross-references like `005/A`.

The naming convention is auto-detected during `/plan-kit:init` — Swift/Apple projects get UpperCamelCase (shown above), while JS/web projects get kebab-case (`plan-kit/`, `ideas.md`, etc.). The convention is stored in `.config.json`.

## What the Files Look Like

All examples below are from a fictional **HabitTracker** app — the same files linked in the table above.

### Ideas — themed groups of raw ideas

```markdown
## Streaks & Motivation

### Visual Streak Calendar
The problem: users complete habits but don't feel progress.
A visual calendar (like GitHub's contribution graph) could provide
"don't break the chain" motivation.

See [Streaks app](https://streaksapp.com) for a minimal approach.
```

Ideas are organized by theme (H2) with detailed entries (H3) and brief bullets. Nothing is filtered or summarized — your exact words, links, and research are preserved. → [Full example](examples/Ideas.md)

### Roadmap — versioned features with decisions

```markdown
## v2.0 — Streaks & Motivation

### Streak System

Visual streak tracking with a GitHub-style heatmap calendar...

**Key decisions:**
- Heatmap grid, not chain dots (richer data, more satisfying)
- Intensity = percentage of daily habits completed
- 12-month scrollable view, current month anchored to the right

**Open questions:**
- Should partial completion count as maintaining the streak?
```

Each feature carries its full context from the idea, enriched with **Key decisions** (what you decided during the triage conversation) and **Open questions** (what's still unresolved). → [Full example](examples/Roadmap.md)

### Steps — requirements-level implementation files

```markdown
# Design Streak Calendar

Feature: Streak System (from Roadmap v2.0 — Streaks & Motivation)

⚠️ Created 2026-02-19. Review current codebase before implementing.

## Heatmap Calendar Layout

12-month scrollable view, current month anchored to the right.
Grid layout: 7 rows (days of week) × ~52 columns (weeks).

Color intensity based on daily completion percentage:
- 0% = empty cell (background color)
- 1–49% = light shade
- 50–99% = medium shade
- 100% = full intensity (accent color)
```

Steps describe *what* to build — data models, layouts, interactions, edge cases — not *how* to code it. Each step includes a freshness warning (creation date) reminding you to check the current codebase before implementing. → [Full example](examples/A-DesignStreakCalendar.md)

### Progress — automatic dashboard

```markdown
## Current: v2.0 — Streaks & Motivation

### Streak System
Status: In Progress

Steps:
- [x] Design Streak Calendar — `Features/005-StreakSystem/A-DesignStreakCalendar.md`
- [>] Implement Streak Logic — `Features/005-StreakSystem/B-ImplementStreakLogic.md`
- [ ] Validate Edge Cases — `Features/005-StreakSystem/C-ValidateEdgeCases.md`
```

Progress.md updates automatically as you work. Status markers: `[ ]` not started, `[>]` in progress, `[x]` done. → [Full example](examples/Progress.md)

### Done — archive of completed work

```markdown
## v1.0 — Core Habits (2025-09-01 → 2026-01-15)

### ✅ 001-HabitCreation

Create, edit, and delete custom habits with name, icon, and frequency.

- ✅ A-DesignHabitModel · plan: `e3f4a5b`
- ✅ B-BuildCreationFlow · plan: `c6d7e8f` · PR #3

### ⏭️ 004-HabitReminders

Descoped — notification framework not ready. Moved to v2.0 ideas.
```

When features or steps are completed (✅), skipped (⏭️), or dropped (🛑), they're archived in Done.md with git commit SHAs for full recovery. `plan:` SHAs point to the deletion commit — `git show <sha>` recovers the complete step file. → [Full example](examples/Done.md)

## Lifecycle

### Ideas → Roadmap (Triage)

When you run `/plan-kit:plan-roadmap`, Claude reads your ideas, investigates the codebase, and walks you through each idea: include in this version, skip, or discuss further? For each included feature, Claude asks 1–3 clarifying questions based on what it found in your code — then records the answers as **Key decisions**. Selected ideas are **moved** (not copied) from Ideas to the Roadmap.

### Roadmap → Steps (Breakdown)

When you run `/plan-kit:define-steps`, Claude reads the roadmap feature, investigates relevant code, and proposes a step breakdown. You discuss and approve before any files are created. Each step is designed to be completable in one focused session, with design always as a separate step before implementation.

### Steps → Done (Completion)

When you tell Claude a step is done, it commits and deletes the step file, updates Progress.md, and archives the entry to Done.md with the deletion commit SHA. When all steps for a feature are complete, the feature folder is cleaned up, the roadmap feature is marked ✅, and the feature entry in Done.md gets its final status.

Features can also be **skipped** (⏭️ — decided not to build) or **dropped** (🛑 — started but abandoned). Both are recorded in Done.md with the reason, so the context isn't lost.

### Version Release

When a version ships, completed features are slimmed to just names in both the Roadmap and Progress.md. Done.md gets the version's end date filled in. Any unfinished features carry over to the next version automatically.

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
- **Nothing is lost** — completed work is archived in Done.md with git SHAs for full recovery
