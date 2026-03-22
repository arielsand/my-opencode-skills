---
name: dev-time-tracker
description: Track development time and update Excel timesheets. Use this skill when the user wants to record development work sessions, log hours worked, or update their development time tracking spreadsheet. Triggers on phrases like "log my time", "record development hours", "track my work time", "update time tracking", "log session", or when the user mentions they finished working and wants to record it. This skill calculates session duration and appends entries to an Excel timesheet.
license: MIT
---

# Development Time Tracker

Track development sessions and update Excel timesheets with session details.

## When to Use

Invoke this skill when:
- User asks to log/record development time
- User mentions finishing a work session
- User wants to track hours worked
- User asks to update their timesheet/tracking spreadsheet

## Process

### 1. Identify Session Context

Determine from the conversation:
- **What was worked on**: The main focus/tasks during this session
- **Key deliverables**: What was built, fixed, or improved
- **Notes**: Any relevant context, blockers, decisions

### 2. Calculate Session Duration

Calculate time elapsed from session start to now:
- If session start time is known from context, use it
- Otherwise, ask the user for start time or duration

### 3. Get Today's Date

Use the current date (system provides this).

### 4. Locate or Create Timesheet

**Default location pattern**: `{project}/docs/development_time.xlsx`
- Check for existence at this path
- If not found, search common locations
- If creating new, use the template below

### 5. Add Entry to Timesheet

Use the xlsx skill patterns with openpyxl:

```python
from openpyxl import load_workbook
from datetime import datetime

wb = load_workbook(excel_path)
sheet = wb.active  # or wb['Time Tracking']

# Find next empty row (after header + existing data)
next_row = sheet.max_row + 1

# Get day name from date
day_name = date_obj.strftime('%A')

# Insert new row
sheet.insert_rows(next_row)

# Populate columns
sheet[f'A{next_row}'] = date_obj          # Date
sheet[f'B{next_row}'] = day_name           # Day
sheet[f'C{next_row}'] = hours              # Hours
sheet[f'D{next_row}'] = focus              # Focus
sheet[f'E{next_row}'] = deliverables       # Key Deliverables
sheet[f'F{next_row}'] = notes              # Notes

# Move TOTAL row down (it should stay at bottom)
# The TOTAL formula should auto-adjust or needs update

wb.save(excel_path)
```

## Timesheet Structure

The Excel file should have these columns:

| Column | Header | Content |
|--------|--------|---------|
| A | Date | Date (YYYY-MM-DD format) |
| B | Day | Day name (Monday, Tuesday, etc.) |
| C | Hours | Decimal hours worked |
| D | Focus | Brief description of main focus area |
| E | Key Deliverables | Bullet list of what was delivered |
| F | Notes | Additional context, blockers, decisions |

**Header row**: Row 1 (or row 3 if there's a title)

**TOTAL row**: Bottom row with `=SUM(C4:C{last_data_row})` formula

## Entry Format Guidelines

### Hours
- Use decimal format (e.g., 2.5 for 2 hours 30 minutes)
- Round to nearest 0.25 hours (15 minutes)
- Typical: 0.5, 1, 1.5, 2, 2.5, etc.

### Focus
- One line, 50-80 characters
- Format: `{Component/Feature} - {Brief Description}`
- Example: "Mobile Authentication API - Implementation & Testing"

### Key Deliverables
- Semicolon-separated list of accomplishments
- Start each item with a verb (Implemented, Fixed, Added, Created, Updated)
- Example: "Implemented login flow; Fixed registration bug; Added tests"

### Notes
- Optional but valuable for future reference
- Include: breaking changes, environment issues, decisions made
- Example: "Breaking change: requires migration; Docker env had issues"

## Session Summary Template

After adding the entry, show the user:

```
📊 Time Logged
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📅 Date: {date}
⏱️  Duration: {hours} hours
🎯 Focus: {focus}
✅ Deliverables: {deliverables}
📝 Notes: {notes}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📁 Updated: {excel_path}
```

## Creating a New Timesheet

If no timesheet exists, create one:

```python
from openpyxl import Workbook
from openpyxl.styles import Font, Alignment, PatternFill
from datetime import datetime

wb = Workbook()
sheet = wb.active
sheet.title = "Time Tracking"

# Title
sheet['A1'] = "Time Tracking - {Project Name}"
sheet['A1'].font = Font(bold=True, size=14)
sheet.merge_cells('A1:F1')

# Headers (row 3)
headers = ['Date', 'Day', 'Hours', 'Focus', 'Key Deliverables', 'Notes']
for col, header in enumerate(headers, 1):
    cell = sheet.cell(row=3, column=col, value=header)
    cell.font = Font(bold=True)
    cell.fill = PatternFill('solid', fgColor='D9E1F2')

# Column widths
sheet.column_dimensions['A'].width = 12
sheet.column_dimensions['B'].width = 10
sheet.column_dimensions['C'].width = 8
sheet.column_dimensions['D'].width = 50
sheet.column_dimensions['E'].width = 60
sheet.column_dimensions['F'].width = 40

# TOTAL row will be added when first entry comes
# Formula: =SUM(C4:C{row})

wb.save(excel_path)
```

## Dependencies

This skill depends on the xlsx skill for Excel file operations. Key requirements:
- openpyxl must be available
- LibreOffice for formula recalculation (optional, via xlsx skill's recalc.py)

## Error Handling

- If timesheet not found, offer to create one
- If openpyxl not available, suggest installing it
- Always backup before modifying existing files