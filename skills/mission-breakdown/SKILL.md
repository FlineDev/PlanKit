---
name: mission-breakdown
description: >
  Break down a roadmap feature into detailed implementation missions. Use
  when the user wants to plan how to implement a feature, create tasks, or
  break down a milestone into work items. Generates requirement-focused
  missions with design always separate from implementation. Keywords:
  missions, mission, mission breakdown, break down, implementation plan,
  tasks, how to build, plan feature, work breakdown, mission by mission,
  detail feature, let's implement, start building, plan implementation,
  what are the missions.
---

# Mission Breakdown

Break down roadmap features into implementation missions — requirement-focused, session-sized, design before code.

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

## Mission Breakdown Workflow

1. **Read the Roadmap** — load all active (non-✅) versions and their features
2. **Select a feature**:
   - If the user specified a feature name → find it in the roadmap
   - If not:
     a. Tell the user: "Let me review your roadmap and codebase first, then I'll help you pick the right feature to break down."
     b. **Scan the codebase** — read relevant source files, project docs (AGENTS.md, README.md, etc.), and existing Features/ directory to understand the current state
     c. **Present a roadmap summary with recommendations** — show ALL active features with brief descriptions, note which ones have dependencies on others, which seem most impactful or foundational, and which already have missions defined. Recommend which feature to tackle next based on your investigation.
     d. **Ask via AskUserQuestion** — include every non-✅ feature that doesn't have missions yet. If there are more than 4 features, present the top recommendations as options and note others are available via "Other"
   - If the feature has ✅ already, warn: "This feature is marked complete. Continue anyway?"
3. **Deep investigation** — after feature selection:
   - Tell the user: "Let me investigate your codebase to understand the current architecture for [feature name]."
   - Read relevant source files, data models, existing implementations, and any related documentation
   - This is always done automatically for code features — no need to ask permission
4. **Determine feature number**:
   - Read `nextFeatureNumber` from `.config.json`
   - Use that number (zero-padded to 3 digits) for this feature
   - Increment `nextFeatureNumber` in `.config.json` and write it back
   - This ensures globally unique feature numbers that are never reused
5. **Discuss details and open questions** — before proposing any mission breakdown:
   - Present your investigation findings: what you found in the codebase, what's already implemented, what the current architecture looks like
   - Review the roadmap feature's **Key decisions** and **Open questions** sections
   - Identify the biggest uncertainties that affect mission scope or content
   - Discuss these with the user conversationally — ask questions grounded in specific findings (e.g., "I found X in your codebase, which means Y — does that match your intent?" NOT generic questions like "What framework do you prefer?")
   - When referencing project files, always include the file path and a one-sentence description of what the file contains
   - Cross-reference existing spec documents and project docs before asking questions that may already be answered
   - Record the user's answers — they will be baked into the mission files as requirements
6. **Plan the missions**:
   - Analyze the feature's scope, decisions (including from the discussion), and remaining open questions
   - Apply splitting guidelines:
     - **Design always separate from implementation**
     - **Content creation separate from code**
     - **One session ≈ one mission** (completable in one focused working session)
     - **Small cross-repo changes can combine**
   - Determine: **single mission** (→ just a file) or **multiple missions** (→ folder with mission files)
7. **Present the plan** — show the proposed mission breakdown using standard markdown (NOT decorative boxes — they constrain text width):

   ```
   ## Mission Breakdown — Feature Name

   **Feature:** [name] from Roadmap [version]
   **Structure:** [N] missions in folder `XXX-FeatureName/`

   1. **Mission name** — brief description
   2. **Mission name** — brief description
   3. **Mission name** — brief description
   ```

   Tell the user: "After you approve this breakdown, I'll create requirement-focused mission files with the details we discussed."

   Use AskUserQuestion:
   ```json
   {
     "questions": [{
       "question": "Does this mission breakdown look right?",
       "header": "Missions Plan",
       "options": [
         {"label": "Looks good", "description": "Create the mission files as shown"},
         {"label": "Adjust", "description": "I want to change the breakdown"}
       ],
       "multiSelect": false
     }]
   }
   ```

   If "Adjust" → discuss changes, then re-present.

8. **Create the files**:
   - **Single mission**: create `Features/XXX-FeatureName.md` directly
   - **Multiple missions**: create `Features/XXX-FeatureName/` folder with `A-MissionName.md`, `B-MissionName.md`, etc.
   - Apply naming convention from `.config.json`
   - Each file includes:
     - H1 = mission/feature name
     - Roadmap reference line
     - Freshness warning with today's date
     - References section with all carried-over links
     - Content sections (Design before Implementation, always)
     - Checkboxes where tracking makes sense
   - Content is **requirements-level**: exact numbers, rules, conditions, behaviors — NOT code patterns or implementation details
9. **Update Progress.md** — if Progress.md exists:
   - Find the feature's `### heading` under the `## Current:` section
   - Add a `Missions:` list using this exact format per line: `- [ ] Mission Name — \`Features/NNN-FeatureName/X-MissionName.md\`` (where X is the mission letter)
   - File paths are relative to the PlanKit folder, wrapped in backticks (NOT markdown links)
   - If the feature section doesn't exist under Current, create it with `Status: Planned`
   - Update `Status:` to `In Progress` if any missions were created
10. **Confirm** — show what was created and suggest starting with the first mission

### Executing missions with AI agents

Once a mission file exists, the user has a few options for execution:

- **Work through it interactively with Claude Code** — the classic flow: read the mission, implement step by step, ask questions as they come up.
- **Hand it off to [TandemKit](https://github.com/FlineDev/TandemKit)** for a more autonomous loop — TandemKit treats each PlanKit mission as its own mission and coordinates Claude + Codex across planning, generation, and evaluation so you can step away while it runs. A good fit when the mission is well-scoped and you'd rather review the result than babysit the process.

You don't need to decide upfront — PlanKit just defines the mission; how it gets executed is up to you.

## Mission Completion

When the user indicates a mission is done (or you notice from conversation), update Progress.md directly here. The dashboard skill also derives feature status from mission markers, but explicit updates during interactive workflows ensure accuracy.

1. Ask: "Mission [name] is complete. Should I delete the mission file?"
2. If confirmed:
   a. **Commit the mission file deletion** to git (suggest committing first so content is in history)
   b. Delete the mission file
   c. **Archive to Done.md** — load the `done` skill and follow its **Scenario 1: Mission Completed** instructions. Pass the mission name, feature name/number, and file path.
3. **Update Progress.md** — change the mission's marker from `[ ]` or `[>]` to `[x]`
4. Check if ALL missions in the feature folder are now done:
   - If yes → update Progress.md: set feature `Status: Done`
   - Ask: "All missions for [Feature] are complete. Delete the feature folder and mark it ✅ on the roadmap?"
   - If confirmed → delete folder, add ✅ to roadmap feature heading
   - **Archive to Done.md** — load the `done` skill and follow its **Scenario 2: Feature Completed** instructions
5. If a mission is **dropped** (started but abandoned):
   - Delete the mission file (commit first)
   - **Archive to Done.md** — load the `done` skill and follow its **Scenario 1: Mission Completed** instructions, using the 🛑 marker and including the reason
6. If aspects were incomplete or newly discovered:
   - Offer to add them to another mission file
   - Or note in the roadmap feature as needing follow-up
   - Or capture as a new idea via the idea-dump skill

## Browse/Review Mode

When the user wants to see their current missions:
1. List all feature folders/files in `Features/`
2. Show mission counts per feature
3. Read specific mission files on request
4. Handle modifications conversationally

## Important Rules

- **Narrate your process** — before investigating, tell the user what you're about to do and why: "Let me review your roadmap and scan the codebase to understand the current state." After investigating, summarize what you found before asking questions. Users should never wonder what's happening or why they're being asked something.
- **Ground questions in findings** — every question should reference something specific you found in the code, docs, or ideas. NOT "What framework should we use?" but "I see you're using SwiftData for persistence — should this feature also use SwiftData, or is there a reason to use a different approach?"
- **Design before implementation** — every multi-mission feature should have design missions before build missions
- **Requirements, not code** — describe WHAT the code should do, not HOW to write it. Include exact numbers, rules, conditions, edge cases, UI descriptions. Do NOT include class names, struct definitions, function signatures, or architecture patterns.
- **Preserve all links** — every URL from the roadmap feature carries into the relevant mission files
- **Freshness warning** — always include creation date so future sessions know to check current state
- **Session-sized** — each mission should feel like "I can finish this in one sitting"
- **No re-lettering** — if mission B is deleted, missions A and C remain. Gaps are fine.

## Format Reference

For detailed file format specification, see [FEATURES_FORMAT.md](references/FEATURES_FORMAT.md).
