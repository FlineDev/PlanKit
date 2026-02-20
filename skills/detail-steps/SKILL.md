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
2. If neither exists, tell the user: "No PlanKit folder found. Run `/plan-kit:init` to set up PlanKit first."
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
   - If not:
     a. Tell the user: "Let me review your roadmap and codebase first, then I'll help you pick the right feature to break down."
     b. **Scan the codebase** — read relevant source files, project docs (AGENTS.md, README.md, etc.), and existing Features/ directory to understand the current state
     c. **Present a roadmap summary with recommendations** — show ALL active features with brief descriptions, note which ones have dependencies on others, which seem most impactful or foundational, and which already have steps defined. Recommend which feature to tackle next based on your investigation.
     d. **Ask via AskUserQuestion** — include every non-✅ feature that doesn't have steps yet. If there are more than 4 features, present the top recommendations as options and note others are available via "Other"
   - If the feature has ✅ already, warn: "This feature is marked complete. Continue anyway?"
3. **Deep investigation** — after feature selection:
   - Tell the user: "Let me investigate your codebase to understand the current architecture for [feature name]."
   - Read relevant source files, data models, existing implementations, and any related documentation
   - This is always done automatically for code features — no need to ask permission
4. **Determine feature number**:
   - List existing entries in `Features/` directory
   - Find the highest 3-digit number and increment by 1
   - If empty, start at `001`
5. **Discuss details and open questions** — before proposing any step breakdown:
   - Present your investigation findings: what you found in the codebase, what's already implemented, what the current architecture looks like
   - Review the roadmap feature's **Key decisions** and **Open questions** sections
   - Identify the biggest uncertainties that affect step scope or content
   - Discuss these with the user conversationally — ask questions grounded in specific findings (e.g., "I found X in your codebase, which means Y — does that match your intent?" NOT generic questions like "What framework do you prefer?")
   - When referencing project files, always include the file path and a one-sentence description of what the file contains
   - Cross-reference existing spec documents and project docs before asking questions that may already be answered
   - Record the user's answers — they will be baked into the step files as requirements
6. **Plan the steps**:
   - Analyze the feature's scope, decisions (including from the discussion), and remaining open questions
   - Apply splitting guidelines:
     - **Design always separate from implementation**
     - **Content creation separate from code**
     - **One session ≈ one step** (completable in one focused working session)
     - **Small cross-repo changes can combine**
   - Determine: **single step** (→ just a file) or **multiple steps** (→ folder with step files)
7. **Present the plan** — show the proposed step breakdown using standard markdown (NOT decorative boxes — they constrain text width):

   ```
   ## Step Breakdown — Feature Name

   **Feature:** [name] from Roadmap [version]
   **Structure:** [N] steps in folder `XXX-FeatureName/`

   1. **Step name** — brief description
   2. **Step name** — brief description
   3. **Step name** — brief description
   ```

   Tell the user: "After you approve this breakdown, I'll create requirement-focused step files with the details we discussed."

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

8. **Create the files**:
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
9. **Update Progress.md** — if Progress.md exists:
   - Find the feature's `### heading` under the `## Current:` section
   - Add a `Steps:` list using this exact format per line: `- [ ] Step Name — \`Features/NNN-FeatureName/NNN-StepName.md\``
   - File paths are relative to the PlanKit folder, wrapped in backticks (NOT markdown links)
   - If the feature section doesn't exist under Current, create it with `Status: Planned`
   - Update `Status:` to `In Progress` if any steps were created
10. **Confirm** — show what was created and suggest starting with the first step

## Step Completion

When the user indicates a step is done (or you notice from conversation), update Progress.md directly here. The dashboard skill also derives feature status from step markers, but explicit updates during interactive workflows ensure accuracy.

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

- **Narrate your process** — before investigating, tell the user what you're about to do and why: "Let me review your roadmap and scan the codebase to understand the current state." After investigating, summarize what you found before asking questions. Users should never wonder what's happening or why they're being asked something.
- **Ground questions in findings** — every question should reference something specific you found in the code, docs, or ideas. NOT "What framework should we use?" but "I see you're using SwiftData for persistence — should this feature also use SwiftData, or is there a reason to use a different approach?"
- **Design before implementation** — every multi-step feature should have design steps before build steps
- **Requirements, not code** — describe WHAT the code should do, not HOW to write it. Include exact numbers, rules, conditions, edge cases, UI descriptions. Do NOT include class names, struct definitions, function signatures, or architecture patterns.
- **Preserve all links** — every URL from the roadmap feature carries into the relevant step files
- **Freshness warning** — always include creation date so future sessions know to check current state
- **Session-sized** — each step should feel like "I can finish this in one sitting"
- **No renumbering** — if step 002 is deleted, steps 001 and 003 remain. Gaps are fine.

## Format Reference

For detailed file format specification, see [FEATURES_FORMAT.md](references/FEATURES_FORMAT.md).
