---
name: plan-roadmap
description: >
  Create and manage the project roadmap by triaging ideas into versioned
  releases. Use when the user wants to plan a version, prioritize features,
  create a roadmap, or triage ideas. Interactively clarifies requirements
  with questions about edge cases and technical direction. Keywords: roadmap,
  plan version, next release, prioritize, triage, what should we build,
  release plan, version plan, plan features, next milestone, what's next.
---

# Plan Roadmap

Triage ideas into versioned releases, clarifying requirements and making key decisions along the way.

## Config Preamble

Before any operation, locate the PlanKit folder and read its config:

1. Check if `PlanKit/` exists in the project root. If not, check `plan-kit/`.
2. If neither exists, tell the user: "No PlanKit folder found. Run `/plan-kit:init` to set up PlanKit first."
3. Read `.config.json` from the PlanKit folder to get the naming convention and split threshold.

Determine file paths:
- **Ideas**: `PlanKit/Ideas.md` or `PlanKit/Ideas/` (split mode) — UpperCamelCase
  - Or: `plan-kit/ideas.md` or `plan-kit/ideas/` — kebab-case
- **Roadmap**: `PlanKit/Roadmap.md` or `PlanKit/Roadmap/` (split mode) — UpperCamelCase
  - Or: `plan-kit/roadmap.md` or `plan-kit/roadmap/` — kebab-case
- **Progress**: `PlanKit/Progress.md` or `plan-kit/progress.md`

## Roadmap Triage Workflow

This is the primary workflow — turning ideas into a versioned roadmap:

1. **Read Ideas file** — load all current ideas (handle split mode: read the index file then relevant topic files)
2. **Read existing Roadmap** — understand what versions and features already exist
3. **Detect current project version**:
   - Check for Xcode project (Marketing Version in pbxproj)
   - Check Package.swift for version
   - Check git tags (`git tag --sort=-v:refname | head -5`)
   - Check package.json version field
   - If none found, ask the user what version they're planning
4. **Investigate the codebase** — read AGENTS.md, CHECKPOINT.md, README.md, and other project docs to understand what's already implemented, what's changed, and what context is relevant to roadmap planning.

### If the roadmap already has content (review + triage):

5. **Present the current state** — show a summary: roadmap versions with feature counts, ideas grouped by theme with idea counts. Include observations from your codebase investigation (e.g., features that may already be implemented, items that are content tasks rather than code, ideas that relate to existing roadmap features).
6. **Discuss and refine conversationally** — don't force rigid per-idea triage. Instead:
   - Point out roadmap items that appear done, misplaced, or outdated
   - Suggest structural improvements (version scope, ordering, merging small items)
   - Ask about version scope and goals — what should each version represent?
   - Use AskUserQuestion for concrete choices (e.g., "Should X move to v2.0?") but keep the overall flow conversational
   - Ask targeted clarifying questions about the biggest unknowns (1-3 per feature)
   - When designing AskUserQuestion options, prefer mixed/progressive options over exclusive ones (e.g., "All in priority order" not just one exclusive choice). Include scope-fluid options like "Try all, defer complex ones."
   - **Always present the current version plan summary before asking about future versions or next priorities.** Never ask "what comes after vX?" without first showing what vX contains.
7. **After review, offer to triage remaining ideas** — present untriaged ideas that weren't discussed yet. Ask which to pull into existing or new versions.

### If the roadmap is empty (fresh triage):

5. **Determine target version** — if the user specified one, use it. Otherwise, suggest the next logical version and confirm.
6. **Present ideas for triage** — show ideas grouped by theme. For each idea (or group of related ideas):
   - Display the idea summary
   - Use AskUserQuestion: **Include in [version]** / **Skip (keep in ideas)** / **Discuss further**
   - If "Discuss further": ask clarifying questions about edge cases, technical direction, scope. These questions should focus on the BIGGEST unknowns — don't ask about trivia. Then re-ask include/skip.
7. **For each included idea**:
   - Ask 1-3 targeted clarifying questions to resolve the biggest uncertainties. Focus on:
     - Edge cases that affect the feature's scope
     - Technical direction choices (which framework, which approach)
     - Key constraints (performance targets, compatibility requirements)
   - Record answers as **Key decisions**
   - Note remaining unknowns as **Open questions**
   - Carry over ALL content from the idea: descriptions, links, examples, motivation

### After triage (both paths):

8. **Extract included ideas from the Ideas file**:
   - **Single-file mode**: DELETE them from Ideas.md (they now live in the Roadmap)
   - **Split mode**: DELETE them from the appropriate topic file(s) under Ideas/. Update word counts in the index file. If a topic file becomes empty after extraction, delete it and remove its entry from the index file.
   - Ideas now live in the Roadmap — they must not exist in both places.
   - Note: each topic file contains exactly one theme, so ideas cannot span file boundaries. Identify which topic file(s) contain the selected ideas and extract from the correct file(s).
9. **Write the version section(s)** in the Roadmap file with all enriched features
10. **MANDATORY — Create or update Progress.md** (do not skip this step, even when refining an existing roadmap):
    - See [DASHBOARD_FORMAT.md](references/DASHBOARD_FORMAT.md) for the exact format specification.
    - **If Progress.md doesn't exist**: create with `# Progress`, `## Current: vX.Y — Subtitle`, features as `Status: Planned` + `(No steps yet)`, and `## Released`
    - **If Progress.md exists with matching version**: merge — preserve existing features with their current status and steps, add only NEW features as `Status: Planned` + `(No steps yet)`
    - **If Progress.md exists with a DIFFERENT version**: warn the user that there's active progress on a different version. Use AskUserQuestion with options: "Release current version first" / "Replace current version" / "Cancel". If releasing, follow the dashboard skill's version release flow before proceeding. If replacing, move ALL existing Current features to Released (as-is, with today's date) before writing the new version — this prevents silent data loss.
11. **Check word count** — auto-split if exceeds threshold
12. **Summarize** — show what changed: features added/moved/removed, ideas remaining

## Version Management

### Mark Feature Complete

When user indicates a feature is done:
1. Add ✅ to the feature's `### heading` in the Roadmap
2. If all features in the version are ✅, offer to slim the version (see below)

### Version Release (Slim Completed Versions)

When all features in a version have ✅ or user says the version shipped:
1. Slim the version section: remove detailed decisions/questions, keep only feature names + ✅
2. Add `---` separator above the completed version
3. Move any features WITHOUT ✅ to the next version section (create if needed), preserving all content
4. **Update Progress.md**: move completed features to `## Released` as a brief entry (version + date + feature name bullets). Carry incomplete features to the next version's Current section.
5. Offer to clean up completed feature files in `Features/` directory (with user confirmation)

## Auto-Split Procedure

When the roadmap file exceeds the configured `splitThreshold` word count:

1. Read the full roadmap file
2. Create the Roadmap subdirectory: `PlanKit/Roadmap/` or `plan-kit/roadmap/`
3. For each `## version` section, create a separate file:
   - UpperCamelCase: `PlanKit/Roadmap/V2_0.md` (dots become underscores)
   - kebab-case: `plan-kit/roadmap/v2-0.md` (dots become hyphens)
4. Each version file: H1 = version header, then all features from that version
5. Create `Index.md` (UpperCamelCase) or `index.md` (kebab-case) with overview table (version, status, file link, word count)
6. Delete the original single Roadmap file
7. Tell the user about the split

When already in split mode, write to the relevant version file and update the index file.

## Important Rules

- **Extraction is deletion**: when an idea moves to the roadmap, it is REMOVED from the Ideas file. Ideas do not exist in both places.
- **Preserve all detail**: every link, example, motivation paragraph, and specific number from the idea carries into the roadmap feature entry.
- **Key decisions are new**: these come from the triage conversation, not from the original idea. They represent choices made during planning.
- **Open questions are honest**: if something isn't resolved during triage, mark it clearly rather than guessing.
- **Don't over-ask**: 1-3 clarifying questions per feature is usually enough. Focus on the biggest unknowns only.

## Format Reference

For detailed file format specification, see [ROADMAP_FORMAT.md](references/ROADMAP_FORMAT.md).
