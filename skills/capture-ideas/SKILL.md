---
name: capture-ideas
description: >
  Capture and organize project ideas in the PlanKit ideas file. Use when the
  user mentions a new idea, feature concept, improvement, or wants to review
  their ideas. Preserves all details, examples, motivation, and links.
  Keywords: idea, feature idea, brain dump, improvement, wish list, backlog,
  concept, what if, we should add, capture idea, new idea, I was thinking,
  wouldn't it be cool, we could also, it would be nice.
---

# Capture Ideas

Capture, organize, and manage project ideas in the PlanKit ideas file.

## Config Preamble

Before any operation, locate the PlanKit folder and read its config:

1. Check if `PlanKit/` exists in the project root. If not, check `plan-kit/`.
2. If neither exists, tell the user: "No PlanKit folder found. Run `/plankit:init` to set up PlanKit first."
3. Read `.config.json` from the PlanKit folder to get the naming convention and split threshold.

Determine the ideas file path:
- **Single file mode**: `PlanKit/Ideas.md` (UpperCamelCase) or `plan-kit/ideas.md` (kebab-case)
- **Split mode**: `PlanKit/Ideas/` directory with `index.md` (UpperCamelCase) or `plan-kit/ideas/` (kebab-case)
- **Progress**: `PlanKit/Progress.md` or `plan-kit/progress.md` (if it exists — used for dashboard context)

Check which mode is active by testing if the Ideas directory exists.

## Capture Workflow

When the user shares an idea (explicitly or in conversation):

1. **Read the existing ideas file** (or index.md + relevant topic file if split)
2. **Understand the idea fully** — ask clarifying questions ONLY if the idea is genuinely ambiguous. Do NOT ask unnecessary questions just to seem thorough. If the user gave enough context, proceed directly.
3. **Find the right theme group**:
   - Scan existing `## Theme` headers for a good fit
   - If no existing theme fits, create a new `## Theme` section
   - If unsure which theme fits best, ask the user with AskUserQuestion (2-3 theme options + "New theme")
4. **Check for duplicates/overlap**:
   - If the new idea overlaps with an existing entry, merge only the NEW aspects into the existing entry
   - Mention to the user: "This overlaps with [existing idea] — I've added your new aspects to it."
5. **Choose the right format**:
   - **Brief idea** (1-2 sentences, no complex context) → bullet: `- **Title** — description`
   - **Detailed idea** (paragraphs, examples, links, multiple aspects) → subsection: `### Title` with full content
6. **Write the idea** — preserving ALL details the user provided:
   - Problem description and motivation
   - Examples and scenarios
   - Links and references
   - Specific numbers, rules, or conditions
   - Comparisons to other products or approaches
   - NEVER summarize or shorten user input
7. **Check word count** — run `wc -w` on the file. If it exceeds the `splitThreshold` from `.config.json`, perform auto-split (see below).
8. **Confirm** — tell the user what was captured and where, briefly.

## Browse/Review Mode

When the user wants to review, browse, or explore their ideas:

1. Read the ideas file(s)
2. If Progress.md exists, show a one-line current version status as context (e.g., "Currently working on v2.0 — 2/5 features in progress")
3. Present a summary organized by theme groups
4. If the user wants to discuss, modify, or remove ideas, handle it conversationally
5. For removal: delete the entry and confirm what was removed

## Auto-Split Procedure

When the ideas file exceeds the configured `splitThreshold` word count:

1. Read the full ideas file
2. Create the Ideas subdirectory: `PlanKit/Ideas/` or `plan-kit/ideas/`
3. For each `## Theme` section, create a separate file:
   - UpperCamelCase: `PlanKit/Ideas/ThemeName.md` (spaces removed, each word capitalized)
   - kebab-case: `plan-kit/ideas/theme-name.md` (lowercase, hyphens)
4. Each topic file: H1 = theme name, then all content from that theme section
5. Create `index.md` with overview table (topic, file link, approximate word count)
6. Delete the original single Ideas file
7. Tell the user: "Your ideas file grew past [threshold] words — I've split it into [N] topic files for easier navigation."

When already in split mode, write to the relevant topic file and update word counts in `index.md`.

## Format Reference

For detailed file format specification, see [IDEAS_FORMAT.md](references/IDEAS_FORMAT.md).
