# Roadmap File Format Reference

## Structure

```markdown
# Roadmap

## vX.Y — Version Subtitle

### Feature Name

Paragraphs describing the feature with full context, motivation,
user benefit, and technical direction.

See [Reference](https://example.com) for design inspiration.

**Key decisions:**
- Decision 1 with rationale
- Decision 2 with rationale

**Open questions:**
- Question that needs resolution before or during implementation

### Another Feature

Description with all context carried from ideas plus new
clarifications from roadmap triage.

---

## v1.0 ✅

### Completed Feature A ✅
### Completed Feature B ✅
```

## Rules

1. **H1** = always `# Roadmap` (exactly one)
2. **H2** = version sections — format: `## vX.Y — Optional Subtitle`
   - Active versions: full detail with features, decisions, questions
   - Completed versions: slimmed to feature names + ✅, separated by `---`
3. **H3** = features within a version
   - Can contain paragraphs, lists, subsections, links — whatever fits the content
   - Completed features in active versions: add ✅ to the H3 heading
4. **Key decisions** = bold heading `**Key decisions:**` followed by bulleted list
5. **Open questions** = bold heading `**Open questions:**` followed by bulleted list
6. **No metadata** — no IDs, scores, dates, or tracking markers (except ✅)
7. **Links preserved** — all URLs from ideas carry through to roadmap features
8. **Version ordering** — newest/active versions at top, completed versions at bottom

## Version Section Guidelines

- Use semantic versioning: `vMAJOR.MINOR`
- Subtitle after em-dash is optional but recommended: `## v2.0 — Streaks & Social`
- Group related features under the same version
- A version typically has 2-8 features
- Features are ordered by implementation priority within a version

## Key Decisions Format

Key decisions capture choices made during roadmap triage that narrow the implementation:

```markdown
**Key decisions:**
- Heatmap grid, not simple chain dots (richer data, more satisfying)
- Intensity = percentage of daily habits completed (not raw count)
- Show 12 months of history, scrollable
- Maximum 3 accountability partners (keeps it intimate)
```

Each decision should include the choice AND brief rationale in parentheses.

## Open Questions Format

Open questions are unresolved aspects that need answers before or during implementation:

```markdown
**Open questions:**
- Should partial completion (3 of 5 habits) count as a streak day?
- What color scheme? Green (like GitHub) or customizable?
- How to handle timezone changes mid-streak?
```

Questions should be specific and answerable, not vague.

## Version Completion (Slimming)

When a version is released, slim it down:

**Before (active):**
```markdown
## v1.5 — Quality of Life

### Offline Mode
Full offline habit tracking with local storage. Sync automatically
when connection returns. Server-wins conflict resolution.

**Key decisions:**
- SQLite for local storage
- Server-wins for conflicts (simpler, user data is server-canonical)

### Custom Icons
Users can pick from 200+ SF Symbols for each habit. Icons
shown in list view and detail view.
```

**After (completed):**
```markdown
---

## v1.5 ✅

### Offline Mode ✅
### Custom Icons ✅
```

## Carry-Over Rules

When a version ships but some features are incomplete:
- Move incomplete features to the next version section (create if needed)
- Keep all their content (decisions, questions, links) intact
- Add a note if relevant: "Carried from v1.5 — partially implemented, needs [aspect]"

## Auto-Split Format (when file exceeds threshold)

When Roadmap file grows beyond the configured word threshold:

```
PlanKit/Roadmap/        (or plan-kit/roadmap/)
├── Index.md            (or index.md)
├── V2_0.md             (or v2-0.md)
├── V1_5.md             (or v1-5.md)
└── V1_0.md             (or v1-0.md)
```

### Index.md format (or index.md for kebab-case):

```markdown
# Roadmap

| Version | Status | File | ~Words |
|---------|--------|------|--------|
| v2.0 — Streaks & Social | Active | [V2_0.md](V2_0.md) | ~1,800 |
| v1.5 ✅ | Done | [V1_5.md](V1_5.md) | ~120 |
| v1.0 ✅ | Done | [V1_0.md](V1_0.md) | ~95 |
```

### Individual version file format:

```markdown
# v2.0 — Streaks & Social

### Streak System
[Full feature content...]

### Social Accountability
[Full feature content...]
```

Each version file contains only features from that version, with H1 as the version header.
