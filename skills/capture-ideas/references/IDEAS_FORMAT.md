# Ideas File Format Reference

## Structure

```markdown
# Ideas

## Theme Group Name

### Detailed Idea Title
Paragraphs describing the idea with full context, motivation, problem
description, examples, and any links.

See [Reference](https://example.com) for inspiration.

Possible approaches:
- Approach A with explanation
- Approach B with explanation

### Another Detailed Idea
Description with all context preserved.

## Another Theme Group

- **Brief idea** — One-line description when the idea is simple
- **Another brief idea** — Short description with key point
```

## Rules

1. **H1** = always `# Ideas` (exactly one)
2. **H2** = theme groups — broad categories like "UI & Design", "Performance", "Monetization"
3. **Content under H2** = mix of:
   - `### Subsections` for detailed ideas (paragraphs, lists, links)
   - `- **Bold title** — description` for brief ideas
4. **No metadata** — no dates, IDs, scores, priorities, or status markers
5. **No limits on content** — paragraphs, lists, subsections, links, examples all welcome
6. **Preserve everything** — motivation, problem context, user quotes, links, examples, specifics
7. **Links are sacred** — any URL provided must be preserved exactly as given
8. **Deduplication** — if a new idea overlaps with existing, merge new aspects into existing entry

## Theme Group Guidelines

- Group by broad topic, not by urgency or size
- 3-8 theme groups is typical; create new ones as needed
- Theme names should be short and descriptive (2-4 words)
- Common themes: "UI & Design", "Performance", "Social Features", "Monetization", "Developer Experience", "Accessibility", "Content", "Integrations"

## Auto-Split Format (when file exceeds threshold)

When Ideas file grows beyond the configured word threshold, it splits into a directory:

```
PlanKit/Ideas/          (or plan-kit/ideas/)
├── index.md
├── UIDesign.md         (or ui-design.md)
├── Performance.md      (or performance.md)
└── Social.md           (or social.md)
```

### index.md format:

```markdown
# Ideas

| Topic | File | ~Words |
|-------|------|--------|
| UI & Design | [UIDesign.md](UIDesign.md) | ~1,240 |
| Performance | [Performance.md](Performance.md) | ~650 |
| Social | [Social.md](Social.md) | ~890 |
```

### Individual topic file format:

```markdown
# UI & Design

### Visual Streak Calendar
[Full content of this idea...]

### Dark Mode Improvements
[Full content...]

- **Icon refresh** — Update all icons to SF Symbols 6
```

Each topic file contains only the content from its theme group, with H1 as the theme name.
