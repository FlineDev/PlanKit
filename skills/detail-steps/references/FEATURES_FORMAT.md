# Features / Steps File Format Reference

## Directory Structure

```
PlanKit/Features/               (or plan-kit/features/)
├── 005-StreakSystem/            Feature with multiple steps
│   ├── A-DesignStreakCalendar.md
│   ├── B-ImplementStreakLogic.md
│   └── C-ValidateEdgeCases.md
├── 006-SmartReminders.md       Single-step feature (just a file)
└── 007-StreakMilestones/       Another multi-step feature
    ├── A-DesignMilestones.md
    └── B-BuildMilestones.md
```

## Numbering

### Features: Global 3-digit counter

- Read `nextFeatureNumber` from `.config.json`, use it, increment, write back
- Numbers are **globally unique** — never reused, even after a feature is deleted
- Format: 3-digit zero-padded (`001`, `002`, ..., `999`)
- This ensures references like `005/A` remain unambiguous forever

### Steps: Alphabet letters (A–Z)

- Steps within a feature use uppercase letters: `A-`, `B-`, `C-`, ...
- Letters are assigned in order of creation, NOT priority
- Maximum 26 steps per feature — if more are needed, split the feature
- Gaps are fine (if B is deleted, A and C remain — no re-lettering)

### Cross-referencing

Use `NNN/X` format to reference a specific step:
- `005/A` = feature 005, step A
- `005/C` = feature 005, step C
- `005` alone refers to the feature as a whole

## Naming Convention

Follows `.config.json` naming setting:

| Convention | Feature Folder | Step File |
|-----------|---------------|-----------|
| UpperCamelCase | `005-StreakSystem/` | `A-DesignStreakCalendar.md` |
| kebab-case | `005-streak-system/` | `a-design-streak-calendar.md` |

## Single-Step Feature Format

```markdown
# Feature Name

Feature from Roadmap vX.Y — Version Subtitle

⚠️ Created YYYY-MM-DD. Review current codebase before implementing.

References:
- [Link text](https://example.com) — brief description of relevance

## Design

Design decisions, UI mockup descriptions, data models, user flows.
All requirements-level detail: exact numbers, rules, conditions.

## Implementation

- [ ] Task description with specific acceptance criteria
- [ ] Another task with measurable outcome
- [ ] Integration or wiring task

## Validation

- [ ] Edge case to verify
- [ ] Performance consideration to check
- [ ] User experience scenario to test
```

## Multi-Step Feature — Step File Format

```markdown
# Step Name

Feature: Feature Name (from Roadmap vX.Y — Version Subtitle)

⚠️ Created YYYY-MM-DD. Review current codebase before implementing.

References:
- [Link text](https://example.com) — brief description

## Section Title

Requirements-level content. Paragraphs, lists, subsections as needed.
Exact numbers, rules, conditions — not code structures.

## Another Section

More detailed requirements for this step.

- [ ] Checkboxes where tracking individual items makes sense
```

## Content Rules

1. **H1** = step name (multi-step) or feature name (single-step)
2. **Subtitle line** = roadmap reference: `Feature: Name (from Roadmap vX.Y — Subtitle)` or `Feature from Roadmap vX.Y — Subtitle`
3. **Freshness warning** = `⚠️ Created YYYY-MM-DD. Review current codebase before implementing.`
4. **References** = all links carried from roadmap, listed with brief descriptions
5. **Design before Implementation** — design sections ALWAYS come before implementation sections
6. **Requirements, not code** — describe WHAT and WHY, not HOW in code terms:
   - YES: "Daily completion record with: date, habits completed count, total habits, completion percentage"
   - NO: "Create a `DailyRecord` struct with `date: Date`, `completedCount: Int`..."
7. **Checkboxes** (`- [ ]`) — use where tracking makes sense, not required everywhere
8. **Sections are flexible** — use whatever section structure fits the content. Common patterns:
   - Design / Implementation / Validation
   - Data Model / UI Layout / Interactions
   - Requirements / Constraints / Edge Cases

## Step Splitting Guidelines

When breaking a feature into steps, follow these principles:

1. **Design always separate from implementation** — design thinking is its own step
2. **Content creation separate from code** — writing copy, preparing assets = separate step
3. **One session ≈ one step** — each step should be completable in roughly one working session
4. **Small cross-repo changes can combine** — if a step touches 2-3 files in different places, that's fine
5. **Dependencies flow forward** — step B can depend on A, never the reverse
6. **Maximum 26 steps** — if a feature needs more, split it into multiple features

## Lifecycle

### Step Completion
When a step is fully done:
- Suggest deleting the step file (tip: commit to git first)
- If user confirms → delete the file
- Load the `done` skill and follow **Scenario 1: Step Completed** to archive it
- If ALL steps in a feature folder are done → offer to delete the entire folder
- Mark the corresponding roadmap feature with ✅
- Load the `done` skill and follow **Scenario 2: Feature Completed**

### Incomplete Aspects
If aspects are discovered during implementation that weren't planned:
- Add them to another existing step file if they fit
- Or note in the roadmap feature: "Partially implemented — [aspect] needs follow-up"
- Or capture as a new idea in the Ideas file for future planning
