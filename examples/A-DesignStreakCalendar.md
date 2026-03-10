# Design Streak Calendar

Feature: Streak System (from Roadmap v2.0 — Streaks & Motivation)

⚠️ Created 2026-02-19. Review current codebase before implementing.

References:
- [GitHub contribution graph](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/managing-contribution-settings-on-your-profile/viewing-contributions-on-your-profile) — primary visual inspiration
- [Streaks app](https://streaksapp.com) — minimal habit streak UI reference
- [Duolingo streak psychology](https://blog.duolingo.com/how-does-duolingo-make-streaks-work/) — retention research

## Heatmap Calendar Layout

12-month scrollable view, current month anchored to the right. Grid layout: 7 rows (days of week, Monday at top) × ~52 columns (weeks).

Color intensity based on daily completion percentage:
- 0% = empty cell (background color)
- 1–49% = light shade
- 50–99% = medium shade
- 100% = full intensity (accent color)

Month labels above the grid, weekday labels (M, W, F) on the left side. Tapping a cell shows a popover with the date and which specific habits were completed.

## Streak Counter

Prominent number above the calendar: "42 days" with a flame icon. The counter shows the current active streak — consecutive days with at least one habit completed (pending decision on partial completion threshold).

Below the counter: "Longest streak: 67 days" in secondary text.

## Data Model

Daily completion record:
- Date
- List of completed habit IDs
- Completion percentage (computed: completed / total active habits)
- Streak-contributing flag (true if percentage meets threshold)

Streak metadata (derived, cached):
- Current streak length
- Longest streak length
- Current streak start date

## SwiftUI Considerations

- Use `LazyHGrid` for the calendar (only visible weeks rendered)
- Each cell is a small rounded rectangle (~12×12pt) with dynamic fill color
- Scroll position: `.scrollPosition(id:)` to anchor to current week on appear
- Popover: use `.popover(isPresented:)` on cell tap
- Accessibility: each cell should announce "March 5: 80% completed, 4 of 5 habits"

## Validation

- [ ] Calendar renders correctly for partial months (January starts mid-week)
- [ ] Scrolling performance with 365 cells is smooth
- [ ] Color contrast meets WCAG AA for all intensity levels
- [ ] VoiceOver reads meaningful descriptions for each cell
- [ ] Streak counter updates immediately when a habit is completed
