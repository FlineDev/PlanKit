# Roadmap

## v2.0 — Streaks & Motivation

### Streak System

Visual streak tracking with a GitHub-style heatmap calendar. Users see their consistency at a glance — a filled grid is inherently satisfying and motivates continued engagement.

The calendar shows 12 months of history in a horizontally scrollable view. Each cell represents one day, with color intensity based on the percentage of habits completed (0% = empty, 100% = full color). Tapping a day shows which habits were completed.

**Key decisions:**
- Heatmap grid, not chain dots — richer data visualization, more satisfying to fill
- Intensity = percentage of daily habits completed (not binary yes/no)
- 12-month scrollable view, current month anchored to the right
- Streak count shown prominently above the calendar ("42 days")

**Open questions:**
- Should partial completion (e.g., 3 of 5 habits) count as maintaining the streak?
- Where does the calendar live — dedicated tab, or embedded in the main habits view?

### Streak Freeze

Allow users to protect their streak during planned absences. 1 free freeze per month, additional freezes available via premium. Reduces the "all or nothing" pressure that causes users to abandon habits entirely after a single missed day.

**Key decisions:**
- 1 free freeze/month for all users, unlimited for premium
- Must be activated before the day ends (no retroactive freezes)
- Frozen days show a distinct visual in the heatmap (snowflake icon or muted color)

**Open questions:**
- Should auto-freeze be an option (detect no activity by 10pm, offer to freeze)?

---

## v1.0 — Core Habits ✅

### Habit Creation ✅
### Daily Tracking ✅
### Weekly Overview ✅
