# Changelog

## 0.1.0 — 2026-02-19

Initial release.

### Added
- **capture-ideas** skill — capture, organize, and manage project ideas with theme grouping and auto-split
- **plan-roadmap** skill — interactive triage of ideas into versioned releases with key decisions and open questions
- **detail-steps** skill — break roadmap features into session-sized, design-first implementation steps
- **dashboard** skill — automatic progress tracking via Progress.md with step markers, version releases, and git recovery
- `/plan-kit:init` command — first-time setup with convention detection, migration, and onboarding
- `/plan-kit:1-idea` command — quick entry point for capturing ideas
- `/plan-kit:2-roadmap` command — quick entry point for roadmap planning
- `/plan-kit:3-steps` command — quick entry point for step breakdown
- Two naming conventions: UpperCamelCase (Swift/Apple) and kebab-case (JS/web)
- Auto-split for Ideas and Roadmap files when they exceed the configured word threshold
- Full lifecycle management: ideas → roadmap extraction → step completion → cleanup
