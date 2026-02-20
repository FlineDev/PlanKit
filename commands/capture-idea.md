---
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
---

# Phase 1 — Capture ideas and improvements

If the user provided an idea alongside this command, pass it along when invoking the skill.

If no idea was provided, ask: "What idea would you like to capture?"

Then invoke the `capture-ideas` skill to record it in the PlanKit ideas file.

$ARGUMENTS
