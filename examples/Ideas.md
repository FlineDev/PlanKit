# Ideas

## Streaks & Motivation

### Visual Streak Calendar

The problem: users complete habits but don't *feel* progress. A visual calendar (like GitHub's contribution graph) could provide "don't break the chain" motivation.

See [Streaks app](https://streaksapp.com) for a minimal approach. Also worth looking at [Duolingo's streak system](https://blog.duolingo.com/how-does-duolingo-make-streaks-work/) — they found streaks are the #1 retention driver.

Possible approaches:
- Heatmap grid (intensity = percentage of habits completed that day)
- Simple chain dots (connected when streak continues)
- Calendar view with color-coded days

### Streak Freeze

Allow users to "freeze" their streak for a day (vacation, sick days). Maybe 1 free freeze per month, more via premium? Duolingo does this and it reduces frustration significantly.

- **Brief idea** — badge or trophy when reaching streak milestones (7, 30, 100 days)

## Social Features

### Accountability Partners

Pair two users who share progress on the same habit category. Not a full social network — just 1:1 accountability. Research shows accountability partners increase success rate by 65% ([ASTD study](https://www.afcpe.org/news-and-publications/the-standard/2018-3/the-power-of-accountability/)).

Key questions:
- How to match partners? Same habit type? Same timezone?
- What data is shared? Just completion status, or details?
- Privacy: opt-in only, easy to disconnect

### Weekly Summary Sharing

Generate a shareable image/card of weekly progress. Users can post to social media or send to friends. Keep it simple — not a social feed, just export.

## Notifications & Reminders

- **Smart reminders** — learn when the user typically completes habits and nudge if they haven't by that time
- **Morning planning** — optional daily notification: "You have 4 habits today. Start with [most important]?"
- **Streak at risk** — alert when a user hasn't completed any habits and the day is 75% over
