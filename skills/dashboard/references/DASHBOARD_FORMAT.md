# Progress File Format Reference

## Structure

```markdown
# Progress

## Current: vX.Y — Version Subtitle

### Feature Name
Status: In Progress

Steps:
- [x] Design Streak Calendar — `Features/001-StreakSystem/001-DesignStreakCalendar.md`
- [>] Implement Streak Logic — `Features/001-StreakSystem/002-ImplementStreakLogic.md`
- [ ] Validate Edge Cases — `Features/001-StreakSystem/003-ValidateEdgeCases.md`

### Another Feature
Status: Planned

(No steps yet)

### Simple Feature
Status: Done

Steps:
- [x] Fix Login Button — `Features/002-SmartReminders.md`

---

## Released

### v1.5 — Quality of Life (2026-01-20)
- Offline Mode
- Custom Icons

### v1.0 — Initial Release (2025-11-01)
- Core Habits
- Basic Tracking
```

## Rules

1. **H1** = always `# Progress` (exactly one)
2. **H2 Current** = `## Current: vX.Y — Optional Subtitle` — exactly one at a time
3. **H2 Released** = `## Released` — completed versions, separated by `---` from Current
4. **H3** = feature names within the current version
5. **Status line** = `Status: Planned | In Progress | Done` — one per feature, on the line after H3
6. **Steps list** = checkbox-style lines with file paths in backticks
7. **No steps yet** = `(No steps yet)` when feature has no step breakdown

## Step Markers

| Marker | Meaning | Display |
|--------|---------|---------|
| `[ ]` | Not started | ○ |
| `[>]` | In progress (current) | ▶ |
| `[x]` | Done | ✅ |

## Step Line Format

```
- [marker] Step Name — `Features/NNN-FeatureName/NNN-StepName.md`
```

- Step name matches the H1 of the step file
- File path is relative to the PlanKit folder, in backticks
- Single-step features use the file path directly: `Features/NNN-FeatureName.md`

## Feature Status Rules

Status is derived from step markers:

| Condition | Status |
|-----------|--------|
| No steps yet | `Planned` |
| All steps `[ ]` | `Planned` |
| Any `[>]` or mix of `[x]` and `[ ]` | `In Progress` |
| All steps `[x]` | `Done` |

## Naming Convention

Follows `.config.json` naming setting:

| Convention | Progress file |
|-----------|--------------|
| UpperCamelCase | `PlanKit/Progress.md` |
| kebab-case | `plan-kit/progress.md` |

File paths inside Progress.md also follow the convention.

## Released Section Format

When a version ships, its features are slimmed to a brief entry:

```markdown
### vX.Y — Version Subtitle (YYYY-MM-DD)
- Feature Name A
- Feature Name B
- Feature Name C
```

- Date is the release date in ISO format
- Features are just bullet names, no steps or status
- Newest released version at top

## Carry-Over Rules

When a version ships but some features are incomplete:
- Move incomplete features to a new `## Current:` section for the next version
- Preserve their status, steps, and file paths
- Add a note if relevant: "Carried from vX.Y"

## Creation

Progress.md is created **lazily** by the plan-roadmap skill when the first version is planned. Initial content:

```markdown
# Progress

## Current: vX.Y — Version Subtitle

### Feature Name
Status: Planned

(No steps yet)

### Another Feature
Status: Planned

(No steps yet)

---

## Released
```
