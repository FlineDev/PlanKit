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
  ═══════════════════════════════════════════════════
  📋 PlanKit already initialized
  ═══════════════════════════════════════════════════

  Folder: [PlanKit/ or plan-kit/]
  Convention: [UpperCamelCase or kebab-case]
  Ideas: [N words across M themes]
  Roadmap: [N features across M versions]
  Features: [N step files]

  ═══════════════════════════════════════════════════
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
- Migrate content into the appropriate PlanKit files:
  - Ideas/brainstorms/wishlists → Ideas.md (organized by themes)
  - Version plans/milestones → Roadmap.md (organized by version)
  - Detailed feature plans → Features/ directory (as step files)
- Preserve ALL detail: descriptions, links, examples, motivation, specifics
- Tell the user what was migrated and where

## Step 5: Ask About Immediate Ideas

```json
{
  "questions": [{
    "question": "Would you like to capture some ideas right now?",
    "header": "Ideas",
    "options": [
      {"label": "Yes, let me share", "description": "Brain-dump some ideas interactively"},
      {"label": "No, later", "description": "I'll add ideas later with /plankit:1-idea"}
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

Display:
```
═══════════════════════════════════════════════════
✅ PlanKit initialized!
═══════════════════════════════════════════════════

Your planning folder: [PlanKit/ or plan-kit/]
Convention: [UpperCamelCase or kebab-case]

📋 How to use PlanKit (in order):

1. /plankit:1-idea   — Capture ideas anytime
2. /plankit:2-roadmap — Plan your next version
3. /plankit:3-steps   — Break features into tasks

Or just talk naturally — PlanKit picks up on
planning-related conversations automatically.

PlanKit also maintains a dashboard (Progress.md)
that updates automatically as you work.

💡 Suggested first step: Capture some ideas!
═══════════════════════════════════════════════════
```
