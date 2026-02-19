---
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
disable-model-invocation: true
---

# PlanKit Setup

You are initializing PlanKit for this project. Follow these steps exactly.

## Step 1: Check Existing State

Check if a PlanKit folder already exists:
- Look for `PlanKit/` in the project root
- Look for `plan-kit/` in the project root

**If a PlanKit folder exists:**
- Read `.config.json` from it
- Count files and approximate word counts in Ideas, Roadmap, Features
- Display current state:
  ```
  ════════════════════════════════════════════════════════════════
  📋 PlanKit already initialized
  ════════════════════════════════════════════════════════════════

  Folder: [PlanKit/ or plan-kit/]
  Convention: [UpperCamelCase or kebab-case]
  Ideas: [N words across M themes]
  Roadmap: [N features across M versions]
  Features: [N step files]

  ════════════════════════════════════════════════════════════════
  ```
- Use AskUserQuestion:
  ```json
  {
    "questions": [{
      "question": "PlanKit is already set up. What would you like to do?",
      "header": "Action",
      "options": [
        {"label": "Re-run migration", "description": "Import additional planning documents into existing PlanKit"},
        {"label": "Nothing, exit", "description": "Keep current setup as-is"}
      ],
      "multiSelect": false
    }]
  }
  ```
- If "Nothing" → exit with a brief "All good!" message.
- If "Re-run migration" → skip to Step 4.

**If no PlanKit folder exists → continue to Step 2.**

## Step 2: Detect Naming Convention

Scan the project root directory to detect the naming convention:

**UpperCamelCase indicators** (Swift/Apple ecosystem):
- `Sources/`, `Tests/`, `Models/`, `Views/`, `ViewModels/`
- `.xcodeproj`, `.xcworkspace`, `Package.swift`
- `Resources/`, `Extensions/`, `Helpers/`, `Protocols/`

**kebab-case indicators** (JS/web ecosystem):
- `src/`, `lib/`, `dist/`, `node_modules/`
- `package.json`, `tsconfig.json`, `webpack.config.js`
- `public/`, `assets/`, `components/`

**If clear match** → use detected convention, tell user: "Detected [convention] based on project structure."

**If ambiguous or no indicators** → ask:
```json
{
  "questions": [{
    "question": "Which naming convention should PlanKit use for files and folders?",
    "header": "Convention",
    "options": [
      {"label": "UpperCamelCase", "description": "PlanKit/, Ideas.md, Features/001-HabitStreaks.md — common for Swift/Apple projects"},
      {"label": "kebab-case", "description": "plan-kit/, ideas.md, features/001-habit-streaks.md — common for JS/web projects"}
    ],
    "multiSelect": false
  }]
}
```

## Step 3: Create Folder Structure

Based on the detected/chosen convention, create:

**UpperCamelCase:**
```
PlanKit/
├── .config.json
├── Ideas.md
├── Roadmap.md
└── Features/
```

**kebab-case:**
```
plan-kit/
├── .config.json
├── ideas.md
├── roadmap.md
└── features/
```

### .config.json content:
```json
{
  "naming": "[UpperCamelCase or kebab-case]",
  "created": "[today's date YYYY-MM-DD]",
  "splitThreshold": 5000
}
```

### Ideas.md content:
```markdown
# Ideas
```

### Roadmap.md content:
```markdown
# Roadmap
```

Create the Features directory (empty).

## Step 4: Ask About Existing Documentation

```json
{
  "questions": [{
    "question": "Do you have existing planning documents to migrate into PlanKit?",
    "header": "Migration",
    "options": [
      {"label": "Yes", "description": "I have ideas, features, or plans documented somewhere"},
      {"label": "No, starting fresh", "description": "No existing documents to import"}
    ],
    "multiSelect": false
  }]
}
```

**If "Yes":**
- Ask: "Where are your existing planning documents? Share file paths, paste content, or describe where they are."
- Read the provided documents
- **Classify each source document:**
  - Raw ideas/brainstorms/wishlists → Ideas.md
  - Version plans/milestones/roadmaps → Roadmap.md
  - Detailed feature plans with implementation steps → Features/ directory
- **Load the relevant PlanKit skills** to ensure correct formatting:
  - If migrating ideas → read the `capture-ideas` skill and its format reference (IDEAS_FORMAT.md) before writing Ideas.md
  - If migrating a roadmap → read the `plan-roadmap` skill and its format reference (ROADMAP_FORMAT.md) before writing Roadmap.md
  - If migrating feature plans → read the `detail-steps` skill and its format reference (FEATURES_FORMAT.md) before writing to Features/
- **Migrate Roadmap first** (if any source contains versioned/prioritized features):
  - Convert to PlanKit roadmap format: each feature group becomes a `### Feature Name` section with description paragraph
  - Checkbox items become descriptive content — do NOT preserve checkbox format
  - Include **Key decisions** and **Open questions** sections if apparent from source
  - Distribute open decisions/blockers as **Open questions** under relevant features
  - Preserve ALL detail: descriptions, links, examples, motivation, specifics
- **Then migrate Ideas** (remaining untriaged content):
  - Organize by themes (H2) with brief or detailed entries
  - **Deduplicate**: if an item already exists in the migrated Roadmap, do NOT add it to Ideas.md. Ideas.md only contains items not yet triaged into any version.
  - If source is in a different language, ask the user whether to translate or keep original
  - Preserve ALL detail: descriptions, links, examples, motivation, specifics
- **Provenance**: at the top of each migrated file, add:
  `> Migrated from [source] on [date]. PlanKit is now the authoritative source for all planning.`
- **Ask about source cleanup** after migration is complete:
  Use AskUserQuestion:
  ```json
  {
    "questions": [{
      "question": "Migration complete. Should I delete the original source files? (PlanKit now has all the content)",
      "header": "Cleanup",
      "options": [
        {"label": "Yes, delete originals", "description": "Remove the source files — PlanKit is now the single source of truth"},
        {"label": "No, keep them", "description": "Leave the original files in place alongside PlanKit"}
      ],
      "multiSelect": false
    }]
  }
  ```
  Only suggest deletion if confident ALL content was migrated. If unsure, note which items might not have been fully captured.
- Do not display intermediate progress messages — just do the work and report at the end

## Step 5: Ask About Immediate Ideas

Before proceeding, read the `capture-ideas` skill instructions and its format reference (IDEAS_FORMAT.md) so you know the correct format for writing to Ideas.md.

```json
{
  "questions": [{
    "question": "Would you like to capture some ideas right now?",
    "header": "Ideas",
    "options": [
      {"label": "Yes, let me share", "description": "Brain-dump some ideas interactively"},
      {"label": "No, later", "description": "I'll add ideas later with /plan-kit:capture-idea"}
    ],
    "multiSelect": false
  }]
}
```

**If "Yes":**
- Ask: "Go ahead — share your ideas! I'll organize them into the Ideas file. Share as many as you want, and say 'done' when finished."
- Capture ideas conversationally, writing them to the Ideas file following the ideas format (themes, subsections for detailed, bullets for brief)
- Continue until the user says they're done

## Step 6: Show Onboarding Summary

Display the following summary EXACTLY as written — use the exact command names shown, do not substitute skill names. Do not display intermediate progress messages during init — the user sees tool calls. Only show AskUserQuestion prompts and this final summary.

```
════════════════════════════════════════════════════════════════
✅ PlanKit initialized!
════════════════════════════════════════════════════════════════

Your planning folder: [PlanKit/ or plan-kit/]
Convention: [UpperCamelCase or kebab-case]

[If migration was done, add:]
Migrated:
  - Ideas.md ← [source] ([N] themes, ~[N] ideas)
  - Roadmap.md ← [source] ([N] versions, [N] features)

📋 Commands (in workflow order):

1. /plan-kit:capture-idea  — Capture ideas anytime
2. /plan-kit:plan-roadmap  — Plan your next version
3. /plan-kit:define-steps  — Break features into tasks

Or just talk naturally — PlanKit picks up on
planning-related conversations automatically.

PlanKit also maintains a dashboard (Progress.md)
that updates automatically as you work.

💡 Suggested first step: Capture some ideas!
════════════════════════════════════════════════════════════════
```
