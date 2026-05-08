---
name: time-tracker
description: Track work sessions and maintain a WORKLOG.md file. Use this skill
  at the end of any significant work session, when the user asks to log work,
  update the worklog, or track time spent. Also use when the user says things
  like "log this", "update worklog", "track time", "record what we did", or "add
  to worklog". This skill should be used proactively at natural stopping points
  like finishing a feature, fixing a bug, or ending a coding session.
license: MIT
---

You maintain the project's WORKLOG.md file, recording what was accomplished in each work session with accurate time tracking.

## Core Behavior

When triggered, you:

1. **Analyze the current session** — Review the conversation history to extract what was done
2. **Calculate duration automatically** — Estimate time spent based on the session's activity (see Duration Calculation below)
3. **Write the entry** — Append a new entry following the exact format below
4. **Update summary tables** — Recalculate daily, weekly, and monthly totals

## WORKLOG.md Location

The file is at `docs/WORKLOG.md` relative to the project root. If it doesn't exist, create it with the initial structure shown below.

## Entry Format

Each entry follows this exact structure:

```markdown
---

## [YYYY-MM-DD HH:MM] Session Title - Brief Description

**Duration**: X hours (or X.Y hours)

**Key Deliverables**:
- Specific thing that was done
- Another specific thing
- Files created or modified
- Tests written or results

**Notes**: Context about decisions made, issues encountered, or follow-up items.
```

The `---` separator goes BEFORE each entry. Entries are ordered newest-first (prepended after the header section, before older entries).

## Writing Good Entries

**Title**: Be specific. "User Manager Bug Fixes" not "Fixed stuff". Include the module or feature area.

**Duration**: Calculate automatically using these signals from the conversation, in order of reliability:

1. **Message count heuristic**: Count the user-assistant message pairs in the conversation. Use this scale:
   - 1-3 exchanges (quick fix, small change): 0.5 hours
   - 4-8 exchanges (feature work, debugging session): 1.5 hours
   - 9-15 exchanges (multi-step feature, complex debugging): 2.5 hours
   - 16-25 exchanges (full feature implementation): 3.5 hours
   - 25+ exchanges (major feature or long debugging session): 4-6 hours

2. **Task complexity adjustment**: Within each range, adjust based on:
   - Files created or modified (more files = more time)
   - Whether tests were written
   - Whether migrations were run
   - Whether research/exploration was needed

3. **User correction**: Always present the calculated estimate to the user for confirmation. If they disagree, use their number.

Round to the nearest 0.5 hours. The format is decimal: `2.5 hours`, never `2:30` or `2 hours 30 min`.

**Key Deliverables**: List concrete outcomes, not activities. Write "Created 5 API endpoints for user management" not "Worked on API". Include:
- Features implemented or bugs fixed
- Files created (with paths if important)
- Tests written or results (e.g., "12 tests, all passing")
- Database changes (migrations, schema updates)
- Documentation updates

**Notes**: Capture context that would be useful later:
- Technical decisions and why they were made
- Issues encountered and workarounds
- Breaking changes or migration requirements
- Known issues or follow-up items

## Summary Tables

The file ends with a `## Time Summary` section containing three tables. This is the most important part — it provides the project's time tracking overview. You MUST rebuild these tables from scratch every time you add an entry. Do NOT try to incrementally edit them — scan ALL entries and recompute.

### How to Rebuild

After inserting the new entry, follow these steps in order:

1. **Scan all entries** — Read every `## [YYYY-MM-DD HH:MM]` header and its `**Duration**: X hours` line
2. **Build Daily Totals** — One row per unique date, summing hours if multiple entries share the same date
3. **Build Weekly Totals** — Group entries by ISO week, count distinct days, sum hours
4. **Build Monthly Totals** — Group entries by month, count distinct days, sum hours
5. **Add TOTAL row** — Sum all months at the bottom

### Daily Totals

One row per unique date across all entries. If a date has multiple entries, sum their hours.

```markdown
### Daily Totals

| Date | Day | Hours | Focus |
|------|-----|-------|-------|
| 2026-04-01 | Tuesday | 3.0 | User Manager Debugging & UI Polish |
| 2026-03-15 | Sunday | 7.0 | Mobile Authentication API |
```

- **Date**: The entry date (YYYY-MM-DD)
- **Day**: Day of the week in English (Monday, Tuesday, etc.)
- **Hours**: Sum of all entry durations on that date (decimal, one decimal place)
- **Focus**: Short description from the entry title

### Weekly Totals

Group daily entries by ISO week number. Use PHP's `Carbon::parse($date)->isoWeek()` logic or equivalent:

```markdown
### Weekly Totals

| Week | Days Worked | Total Hours |
|------|-------------|-------------|
| 2026-W11 (Mar 9-15) | 1 | 7.0 hours |
| 2026-W14 (Mar 30-Apr 5) | 1 | 3.0 hours |
```

- **Week**: ISO year-week number with the date range of that week in parentheses. Format: `YYYY-WNN (Mon DD-Mon DD)` or `YYYY-WNN (Mon DD-Mon DD)` crossing months like `(Mar 30-Apr 5)`. Use 3-letter month abbreviations.
- **Days Worked**: Count of distinct dates that have entries in that week
- **Total Hours**: Sum of hours for all entries in that week, with ` hours` suffix

To determine the week's date range: ISO weeks start on Monday. Find the Monday and Sunday of that week.

### Monthly Totals

Group weekly entries by month:

```markdown
### Monthly Totals

| Month | Days Worked | Total Hours |
|-------|-------------|-------------|
| March 2026 | 3 | 17.5 hours |
| April 2026 | 1 | 3.0 hours |
| **TOTAL** | **4** | **20.5 hours** |
```

- **Month**: Full month name and year (e.g., `March 2026`)
- **Days Worked**: Count of distinct dates that have entries in that month
- **Total Hours**: Sum of hours for all entries in that month, with ` hours` suffix
- **TOTAL row**: Always the last row. `**TOTAL**` in Month column, bold count of ALL distinct dates across all months in Days Worked column, bold sum of ALL hours across all months in Total Hours column.

### Complete Example

Given these entries:
- 2026-04-01, 3.0 hours, User Manager Fixes
- 2026-03-26, 2.5 hours, Change Password API
- 2026-03-22, 8.0 hours, Profile Endpoints
- 2026-03-15, 7.0 hours, Auth API

The Time Summary section should look exactly like:

```markdown
## Time Summary

### Daily Totals

| Date | Day | Hours | Focus |
|------|-----|-------|-------|
| 2026-04-01 | Tuesday | 3.0 | User Manager Debugging & UI Polish |
| 2026-03-26 | Thursday | 2.5 | Mobile Change Password API |
| 2026-03-22 | Sunday | 8.0 | Mobile Profile Endpoints + Architecture Map |
| 2026-03-15 | Sunday | 7.0 | Mobile Authentication API |

### Weekly Totals

| Week | Days Worked | Total Hours |
|------|-------------|-------------|
| 2026-W11 (Mar 9-15) | 1 | 7.0 hours |
| 2026-W12 (Mar 16-22) | 1 | 8.0 hours |
| 2026-W13 (Mar 23-29) | 1 | 2.5 hours |
| 2026-W14 (Mar 30-Apr 5) | 1 | 3.0 hours |

### Monthly Totals

| Month | Days Worked | Total Hours |
|-------|-------------|-------------|
| March 2026 | 3 | 17.5 hours |
| April 2026 | 1 | 3.0 hours |
| **TOTAL** | **4** | **20.5 hours** |

---
```

## Full File Template

When creating `docs/WORKLOG.md` from scratch (file doesn't exist or is empty/corrupt), use this exact template. The new entry and Time Summary are filled with the session's data. This template is also the authoritative reference for the complete file structure — every WORKLOG must match this layout.

```
# Work Log

Project development activity log.

---

## [YYYY-MM-DD HH:MM] Session Title

**Duration**: X.Y hours

**Key Deliverables**:
- First deliverable
- Second deliverable

**Notes**: Context and observations.

---

## Time Summary

### Daily Totals

| Date | Day | Hours | Focus |
|------|-----|-------|-------|
| YYYY-MM-DD | DayName | X.Y | Short focus description |

### Weekly Totals

| Week | Days Worked | Total Hours |
|------|-------------|-------------|
| YYYY-WNN (Mon DD-Mon DD) | N | X.Y hours |

### Monthly Totals

| Month | Days Worked | Total Hours |
|-------|-------------|-------------|
| Month YYYY | N | X.Y hours |
| **TOTAL** | **N** | **X.Y hours** |

---
```

Rules for the template:
- The header `# Work Log` + `Project development activity log.` + `---` is always the file opener
- Each entry starts with `---` on its own line, then `## [date time] Title`
- The `## Time Summary` section is always the last section in the file
- The file always ends with a final `---` after the Monthly Totals table
- Entries are newest-first — the most recent entry is right after the header, older entries follow below

## Process

1. Read the current `docs/WORKLOG.md`
2. Analyze the conversation to determine what was accomplished
3. Calculate duration automatically using the message count heuristic
4. Draft the new entry with the calculated duration
5. Show it to the user for review (they can adjust the duration)
6. Insert the entry (newest first) after the header section
7. **Rebuild the entire Time Summary section from scratch** — scan ALL entries (old + new) and recompute Daily, Weekly, Monthly totals with TOTAL row. Replace the old `## Time Summary` section entirely.
8. Write the updated file
9. Confirm the update to the user

## Important Details

- The date in the entry header uses the current date and time
- Day names in the Daily Totals are in English (Monday, Tuesday, etc.)
- Hours are decimal (1.5 not 1:30)
- If the same date already has an entry, add a new entry for that date (multiple sessions per day are allowed) and update the Daily Total to reflect the sum
- Always preserve existing entries — only add new ones and update summary tables
- When in doubt about duration, ask the user rather than guessing