---
name: dashboard
user-invocable: false
description: >
  Project dashboard that tracks progress on the current version. Only
  activates in projects with a PlanKit folder. Triggers automatically
  when the user asks about status or progress, mentions completing a
  step, or discusses shipping a version. Keywords: progress, status,
  what's done, where are we, show progress, how far, what's left,
  current status, mark done, mark complete, ship version, release
  version, what did we finish, step is done, just finished, we released,
  version is out.
---

# Dashboard

Automatic project dashboard — tracks feature progress, step completion, and version releases via Progress.md.

This skill has **no command**. It triggers automatically when the user discusses status, completion, or releases. Other PlanKit skills (plan-roadmap, detail-steps) also update Progress.md directly as part of their workflows.

## Config Preamble

Before any operation, locate the PlanKit folder and read its config:

1. Check if `PlanKit/` exists in the project root. If not, check `plan-kit/`.
2. If neither exists, tell the user: "No PlanKit folder found. Run `/plankit:init` to set up PlanKit first."
3. Read `.config.json` from the PlanKit folder to get the naming convention.

Determine file paths:
- **Progress**: `PlanKit/Progress.md` or `plan-kit/progress.md`
- **Features dir**: `PlanKit/Features/` or `plan-kit/features/`
- **Roadmap**: `PlanKit/Roadmap.md` or split `PlanKit/Roadmap/`
  - Or: `plan-kit/roadmap.md` or split `plan-kit/roadmap/`

## View Dashboard

When the user asks about status/progress:

1. **Read Progress.md** — if it doesn't exist, tell the user: "No Progress.md yet. It's created when you plan your first version with `/plankit:2-roadmap`."
2. **Display a visual summary** in chat:

   ```
   ═══════════════════════════════════════════════════
   📊 DASHBOARD — vX.Y — Version Subtitle
   ═══════════════════════════════════════════════════

   Feature Name                          [▓▓▓▓░░░░] 2/4
     ✅ Step One
     ▶  Step Two (current)
     ○  Step Three
     ○  Step Four

   Another Feature                       Planned
     (No steps yet)

   Third Feature                         [▓▓▓▓▓▓▓▓] Done
     ✅ Only Step

   ───────────────────────────────────────────────────
   Overall: 3/5 steps done · 1 in progress · 1 planned
   ═══════════════════════════════════════════════════
   ```

3. **Offer actions** if there's something actionable:
   - If a step is marked `[>]`, mention it's the current focus
   - If all steps for a feature are `[x]`, suggest marking the feature as Done
   - If all features are Done, suggest releasing the version

## Update Step Markers

When the user says a step is done, in progress, or wants to change status:

1. **Read Progress.md**
2. **Find the step** by name match
3. **Update the marker**:
   - `[ ]` → `[>]` when starting a step (mark as current)
   - `[>]` → `[x]` when completing a step
   - `[ ]` → `[x]` when completing a step directly
4. **Check feature completion**: if all steps for a feature are `[x]`:
   - Update the feature's `Status: In Progress` → `Status: Done`
5. **Write updated Progress.md**
6. **Confirm** the change briefly

## Version Release

When all features are Done or user says the version shipped:

1. **Read Progress.md**
2. **Slim the Current section** — move completed features to `## Released`:
   - Add a new entry under Released: `### vX.Y — Subtitle (YYYY-MM-DD)` with bullet list of feature names
   - Remove the detailed feature sections from Current
3. **Handle incomplete features**:
   - Any features with `Status: Planned` or `Status: In Progress` → carry over to a new `## Current:` section for the next version
   - Use AskUserQuestion to confirm the next version number
4. **Write updated Progress.md**
5. **Confirm** what was released and what carries over

## Recover Deleted Step Content

When a step file has been deleted but the user needs its content:

1. **Find the file path** from the Progress.md step line (in backticks)
2. **Use git to recover**: `git log --all --full-history -- "path/to/file"` then `git show <commit>:path/to/file`
3. **Display the content** to the user
4. **Offer to recreate** the file if needed

## Important Rules

- **No command** — this skill triggers automatically from conversation context
- **One Current version at a time** — indie devs work on one version; Progress.md has exactly one `## Current:` section
- **Step markers are authoritative** — `[ ]` not started, `[>]` in progress, `[x]` done
- **File paths on every step line** — preserved in backticks for git recovery after deletion
- **Feature status derives from steps** — `Planned` (no steps or all `[ ]`), `In Progress` (any `[>]` or mix of `[x]`/`[ ]`), `Done` (all `[x]`)
- **Released section is slim** — just version + date + feature name bullets, no step details
- **Progress.md is created lazily** — by the plan-roadmap skill when a version is first planned

## Format Reference

For detailed file format specification, see [DASHBOARD_FORMAT.md](references/DASHBOARD_FORMAT.md).
