---
name: detail-steps
description: >
  Break down a roadmap feature into detailed implementation steps. Use when
  the user wants to plan how to implement a feature, create tasks, or
  break down a milestone into work items. Generates requirement-focused
  steps with design always separate from implementation. Keywords: steps,
  break down, implementation plan, tasks, how to build, plan feature,
  work breakdown, step by step, detail feature, let's implement, start
  building, plan implementation, what are the steps.
---

# Detail Steps

Break down roadmap features into implementation steps — requirement-focused, session-sized, design before code.

## Config Preamble

Before any operation, locate the PlanKit folder and read its config:

1. Check if `PlanKit/` exists in the project root. If not, check `plan-kit/`.
2. If neither exists, tell the user: "No PlanKit folder found. Run `/plankit:init` to set up PlanKit first."
3. Read `.config.json` from the PlanKit folder to get the naming convention.

Determine file paths:
- **Roadmap**: `PlanKit/Roadmap.md` or split `PlanKit/Roadmap/` — UpperCamelCase
  - Or: `plan-kit/roadmap.md` or split `plan-kit/roadmap/` — kebab-case
- **Features dir**: `PlanKit/Features/` or `plan-kit/features/`
- **Progress**: `PlanKit/Progress.md` or `plan-kit/progress.md`

## Step Breakdown Workflow

1. **Read the Roadmap** — load all active (non-✅) versions and their features
2. **Select a feature**:
   - If the user specified a feature name → find it in the roadmap
   - If not → present the active features using AskUserQuestion, letting the user pick which to break down
   - If the feature has ✅ already, warn: "This feature is marked complete. Continue anyway?"
3. **Optionally scan project source** — if helpful for understanding current architecture:
   - Ask: "Should I scan your codebase to understand the current architecture before planning steps?"
   - If yes, use Glob/Grep/Read to understand relevant code structure
   - This helps create more accurate, contextual steps
4. **Determine feature number**:
   - List existing entries in `Features/` directory
   - Find the highest 3-digit number and increment by 1
   - If empty, start at `001`
5. **Plan the steps**:
   - Analyze the feature's scope, decisions, and open questions from the roadmap
   - Apply splitting guidelines:
     - **Design always separate from implementation**
     - **Content creation separate from code**
     - **One session ≈ one step** (completable in one focused working session)
     - **Small cross-repo changes can combine**
   - Determine: **single step** (→ just a file) or **multiple steps** (→ folder with step files)
6. **Present the plan** — show the user the proposed step breakdown:

   Display in chat:
   ```
   ═══════════════════════════════════════════════════
   📋 STEP BREAKDOWN — Feature Name
   ═══════════════════════════════════════════════════

   Feature: [name] from Roadmap [version]
   Structure: [N] steps in folder [XXX-FeatureName/]
   OR: Single step as [XXX-FeatureName.md]

   Steps:
   1. [Step name] — [brief description]
   2. [Step name] — [brief description]
   3. [Step name] — [brief description]

   ═══════════════════════════════════════════════════
   ```

   Use AskUserQuestion:
   ```json
   {
     "questions": [{
       "question": "Does this step breakdown look right?",
       "header": "Steps Plan",
       "options": [
         {"label": "Looks good", "description": "Create the step files as shown"},
         {"label": "Adjust", "description": "I want to change the breakdown"}
       ],
       "multiSelect": false
     }]
   }
   ```

   If "Adjust" → discuss changes, then re-present.

7. **Create the files**:
   - **Single step**: create `Features/XXX-FeatureName.md` directly
   - **Multiple steps**: create `Features/XXX-FeatureName/` folder with `001-StepName.md`, `002-StepName.md`, etc.
   - Apply naming convention from `.config.json`
   - Each file includes:
     - H1 = step/feature name
     - Roadmap reference line
     - Freshness warning with today's date
     - References section with all carried-over links
     - Content sections (Design before Implementation, always)
     - Checkboxes where tracking makes sense
   - Content is **requirements-level**: exact numbers, rules, conditions, behaviors — NOT code patterns or implementation details
8. **Update Progress.md** — if Progress.md exists:
   - Find the feature's `### heading` under the `## Current:` section
   - Add a `Steps:` list with `[ ]` markers and file paths for each created step
   - If the feature section doesn't exist under Current, create it with `Status: Planned`
   - Update `Status:` to `In Progress` if any steps were created
9. **Confirm** — show what was created and suggest starting with the first step

## Step Completion

When the user indicates a step is done (or you notice from conversation):

1. Ask: "Step [name] is complete. Should I delete the step file?"
2. If confirmed → delete the step file (tip: suggest committing to git first so content is recoverable via `git show`)
3. **Update Progress.md** — change the step's marker from `[ ]` or `[>]` to `[x]`
4. Check if ALL steps in the feature folder are now done:
   - If yes → update Progress.md: set feature `Status: Done`
   - Ask: "All steps for [Feature] are complete. Delete the feature folder and mark it ✅ on the roadmap?"
   - If confirmed → delete folder, add ✅ to roadmap feature heading
5. If aspects were incomplete or newly discovered:
   - Offer to add them to another step file
   - Or note in the roadmap feature as needing follow-up
   - Or capture as a new idea via the capture-ideas skill

## Browse/Review Mode

When the user wants to see their current steps:
1. List all feature folders/files in `Features/`
2. Show step counts per feature
3. Read specific step files on request
4. Handle modifications conversationally

## Important Rules

- **Design before implementation** — every multi-step feature should have design steps before build steps
- **Requirements, not code** — describe WHAT the code should do, not HOW to write it. Include exact numbers, rules, conditions, edge cases, UI descriptions. Do NOT include class names, struct definitions, function signatures, or architecture patterns.
- **Preserve all links** — every URL from the roadmap feature carries into the relevant step files
- **Freshness warning** — always include creation date so future sessions know to check current state
- **Session-sized** — each step should feel like "I can finish this in one sitting"
- **No renumbering** — if step 002 is deleted, steps 001 and 003 remain. Gaps are fine.

## Format Reference

For detailed file format specification, see [FEATURES_FORMAT.md](references/FEATURES_FORMAT.md).
