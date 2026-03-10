# Done File Format Reference

## Structure

```markdown
# Done

## v2.0 — Streaks & Motivation (2026-04-10 → 2026-06-15)

### ✅ 005-StreakSystem

GitHub-style heatmap calendar showing daily habit completion with streak counter.

- ✅ A-DesignStreakCalendar · plan: `a1b2c3d` · PR #12
- ✅ B-ImplementStreakLogic · plan: `d4e5f6a` · PR #15 #17
- 🛑 C-IntegrateWithWatch · plan: `b7c8d9e` — watchOS app not ready
- ✅ D-ValidateEdgeCases · plan: `f0a1b2c` · PR #18

### ⏭️ 006-StreakFreeze

Descoped — added complexity without clear user demand. May revisit in v3.0.

### ✅ 007-StreakMilestones

Badge system for 7/30/100-day streaks with celebration animations.

- ✅ A-DesignMilestones · plan: `c3d4e5f` · PR #20
- ✅ B-ImplementMilestones · plan: `e6f7a8b` · PR #20

---

## v1.0 — Core Habits (2025-09-01 → 2026-01-15)

### ✅ 001-HabitCreation

Create, edit, and delete custom habits with name, icon, and frequency.

- ✅ A-DesignHabitModel · plan: `e3f4a5b`
- ✅ B-BuildCreationFlow · plan: `c6d7e8f` · PR #3
```

## Status Markers

| Marker | Meaning | Used on |
|--------|---------|---------|
| ✅ | Completed | Features, steps |
| ⏭️ | Skipped (never started) | Features, steps |
| 🛑 | Dropped (started but abandoned) | Features, steps |

## Rules

1. **H1** = always `# Done`
2. **H2** = version sections with date range: `## vX.Y — Subtitle (YYYY-MM-DD → YYYY-MM-DD)`
   - Start date = when work on this version began (first entry date)
   - End date = when the version was released (blank as `→)` until then)
3. **H3** = feature entries: `### STATUS NNN-FeatureName`
   - Features that never got a number (skipped before step creation): `### ⏭️ FeatureName`
4. **Description line** = one sentence summarizing the feature (from Roadmap)
5. **Step lines** = `- STATUS X-StepName · plan: \`SHA\` · PR #N #M`
6. **Reason lines** (⏭️/🛑 only) = plain text after the H3, explaining why
7. **Version ordering** = newest first, separated by `---`
8. **Naming convention** follows `.config.json` (UpperCamelCase or kebab-case)

## Step Line Format

```
- STATUS X-StepName · plan: `<sha>` · PR #N
```

- **STATUS** = one of ✅ ⏭️ 🛑
- **X** = step letter (A–Z)
- **`plan: \`SHA\``** = short SHA of the commit that deleted the step file
- **`· PR #N`** = optional, one or more PR references separated by spaces

For dropped steps, add reason after `—`:
```
- 🛑 C-StepName · plan: `b7c8d9e` — reason for dropping
```

## Git SHA Recovery

The `plan:` SHA points to the deletion commit. To recover a step file:

```bash
git show <sha>~1:PlanKit/Features/NNN-FeatureName/X-StepName.md
```

Use `~1` to get the parent commit (which still has the file). The deletion commit itself shows the full file content as removed lines in the diff.

## Naming Convention

| Convention | Done file |
|-----------|----------|
| UpperCamelCase | `PlanKit/Done.md` |
| kebab-case | `plan-kit/done.md` |
