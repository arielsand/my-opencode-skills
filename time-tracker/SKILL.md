---
name: time-tracker
description: Track development time and update Excel timesheets AND markdown work logs. Use this skill when the user wants to record development work sessions, log hours worked, update their development time tracking spreadsheet, or create/update a work log file. Triggers on phrases like "log my time", "record development hours", "track my work time", "update time tracking", "log session", "update worklog", "add to work log", or when the user mentions they finished working and wants to record it. This skill calculates session duration and appends entries to both an Excel timesheet AND a markdown WORKLOG.md file.
license: MIT
---

# Development Time Tracker

Track development sessions and update both Excel timesheets and markdown work logs with session details.

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

### 5. Add Entry to Timesheet (Excel)

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

### 6. Create or Update WORKLOG.md

**Location**: `{project}/docs/WORKLOG.md`

The WORKLOG.md uses a **weekly table format** with the following structure:
- One table per week
- Columns: Inicio | Fin | Horas | Actividad | Commit(s)
- Daily totals at the end of each day
- Weekly total at the end of each week

**To add a new session entry:**

```python
from datetime import datetime, timedelta
import os
import re

worklog_path = os.path.join(project_path, 'docs', 'WORKLOG.md')

# Get session details
date_str = date_obj.strftime('%Y-%m-%d')  # YYYY-MM-DD
time_str = datetime.now().strftime('%H:%M')  # Current time as end time
start_time_str = session_start_time.strftime('%H:%M') if session_start_time else (datetime.now() - timedelta(hours=hours)).strftime('%H:%M')

# Format hours for table (e.g., "1.5h", "2h")
hours_formatted = f"{hours}h" if hours == int(hours) else f"{hours}h"

# Get commits from deliverables (extract commit hashes if present)
commits = extract_commits(deliverables)  # e.g., "9bbbd55, 3e756e9" or "-"

# Activity description (use focus + shortened deliverables)
activity = f"{focus}: {deliverables.split(';')[0]}" if len(deliverables) > 50 else f"{focus}: {deliverables}"

# Build table row
new_row = f"| {start_time_str} | {time_str} | {hours_formatted} | {activity} | {commits} |"

# Determine week range
date_obj = datetime.strptime(date_str, '%Y-%m-%d')
week_start = date_obj - timedelta(days=date_obj.weekday())  # Monday
week_end = week_start + timedelta(days=6)  # Sunday
week_range = f"{week_start.strftime('%Y-%m-%d')} a {week_end.strftime('%Y-%m-%d')}"

day_name_es = {
    'Monday': 'Lunes', 'Tuesday': 'Martes', 'Wednesday': 'Miércoles',
    'Thursday': 'Jueves', 'Friday': 'Viernes', 'Saturday': 'Sábado', 'Sunday': 'Domingo'
}[date_obj.strftime('%A')]

# Read existing file or create new
if not os.path.exists(worklog_path):
    # Create new file with first week table
    content = generate_new_worklog(week_range, date_str, day_name_es, new_row, hours)
    with open(worklog_path, 'w') as f:
        f.write(content)
else:
    # Update existing file
    with open(worklog_path, 'r') as f:
        content = f.read()
    
    updated_content = update_worklog_content(content, week_range, date_str, day_name_es, new_row, hours)
    
    with open(worklog_path, 'w') as f:
        f.write(updated_content)
```

**Helper Functions:**

```python
def extract_commits(deliverables):
    """Extract commit hashes from deliverables text."""
    # Look for commit hashes (7-40 hex characters)
    commit_pattern = r'\b[0-9a-f]{7,40}\b'
    matches = re.findall(commit_pattern, deliverables.lower())
    if matches:
        return ', '.join(matches[:3])  # Limit to first 3 commits
    return '-'

def generate_new_worklog(week_range, date_str, day_name, row, hours):
    """Generate initial WORKLOG.md content."""
    return f"""# Work Log

Registro detallado de actividades de desarrollo.

## Semana: {week_range}

### {day_name} {date_str}

| Inicio | Fin | Horas | Actividad | Commit(s) |
|--------|-----|-------|-----------|-----------|
{row}

**Total día: {hours}h**

---

**Total semana: {hours}h**

---
"""

def update_worklog_content(content, week_range, date_str, day_name, new_row, hours):
    """Update existing WORKLOG.md with new entry."""
    lines = content.split('\n')
    
    # Check if week section exists
    week_header = f"## Semana: {week_range}"
    if week_header not in content:
        # Add new week section at the beginning (after title)
        new_week = f"""
## Semana: {week_range}

### {day_name} {date_str}

| Inicio | Fin | Horas | Actividad | Commit(s) |
|--------|-----|-------|-----------|-----------|
{new_row}

**Total día: {hours}h**

---

**Total semana: {hours}h**

---
"""
        # Insert after title (line 3)
        lines.insert(3, new_week)
        return '\n'.join(lines)
    
    # Week exists - find it and update
    updated_lines = []
    in_target_week = False
    in_target_day = False
    week_total_found = False
    day_total_found = False
    
    for i, line in enumerate(lines):
        # Check for week header
        if line.strip() == week_header:
            in_target_week = True
            updated_lines.append(line)
            continue
        
        # Check for day header
        if in_target_week and line.strip().startswith(f"### {day_name} {date_str}"):
            in_target_day = True
            updated_lines.append(line)
            continue
        
        # If we're in the target day and hit a separator or new day, add row before
        if in_target_day and (line.strip().startswith('---') or line.strip().startswith('### ') or line.strip().startswith('**Total día')):
            if not day_total_found:
                # Add the new row
                updated_lines.append(new_row)
                day_total_found = True
            
            # Update day total
            if line.strip().startswith('**Total día'):
                # Extract existing hours and add new
                existing = extract_hours(line)
                new_total = existing + hours
                updated_lines.append(f"**Total día: {format_hours(new_total)}**")
                continue
            
            in_target_day = False
        
        # Update week total
        if in_target_week and line.strip().startswith('**Total semana'):
            existing = extract_hours(line)
            new_total = existing + hours
            updated_lines.append(f"**Total semana: {format_hours(new_total)}**")
            week_total_found = True
            continue
        
        updated_lines.append(line)
    
    return '\n'.join(updated_lines)

def extract_hours(line):
    """Extract hours from a total line like '**Total día: 5.5h**'"""
    match = re.search(r'(\d+\.?\d*)h', line)
    return float(match.group(1)) if match else 0

def format_hours(hours):
    """Format hours (remove decimal if whole number)."""
    return int(hours) if hours == int(hours) else hours
```

**WORKLOG.md Structure:**
- Header with project title
- Sections organized by week (`## Semana: YYYY-MM-DD a YYYY-MM-DD`)
- Subsections by day (`### Lunes 2024-01-15`)
- Table with columns: Inicio, Fin, Horas, Actividad, Commit(s)
- Daily totals at end of each day
- Weekly totals at end of each week
- Newest entries added to current day/week

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
- **Include commit hashes** when available (format: `Description text (commit: abc1234)`)
- Example: "Implemented login flow (commit: 9bbbd55); Fixed registration bug (commit: 3e756e9); Added tests"

### Commits
- Commit hashes are automatically extracted from deliverables
- Format recognized: `abc1234`, `commit: abc1234`, `(abc1234)`
- If no commits mentioned, shown as `-` in the table

### Notes
- Optional but valuable for future reference
- Include: breaking changes, environment issues, decisions made
- Example: "Breaking change: requires migration; Docker env had issues"

## Session Summary Template

After adding the entries, show the user:

```
📊 Time Logged
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📅 Date: {date}
⏱️  Duration: {hours} hours
🎯 Focus: {focus}
✅ Deliverables: {deliverables}
📝 Notes: {notes}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📁 Files Updated:
   • {excel_path}
   • {worklog_path}
```

## Creating New Tracking Files

If tracking files don't exist, create both:

### Excel Timesheet

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

### Markdown Work Log (WORKLOG.md)

```python
import os
from datetime import datetime, timedelta

worklog_path = os.path.join(project_path, 'docs', 'WORKLOG.md')

# Get current week range
today = datetime.now()
week_start = today - timedelta(days=today.weekday())
week_end = week_start + timedelta(days=6)
week_range = f"{week_start.strftime('%Y-%m-%d')} a {week_end.strftime('%Y-%m-%d')}"

day_name_es = {
    'Monday': 'Lunes', 'Tuesday': 'Martes', 'Wednesday': 'Miércoles',
    'Thursday': 'Jueves', 'Friday': 'Viernes', 'Saturday': 'Sábado', 'Sunday': 'Domingo'
}[today.strftime('%A')]

date_str = today.strftime('%Y-%m-%d')

# Create the file with header and empty first week table
content = f"""# Work Log

Registro detallado de actividades de desarrollo.

## Semana: {week_range}

### {day_name_es} {date_str}

| Inicio | Fin | Horas | Actividad | Commit(s) |
|--------|-----|-------|-----------|-----------|

**Total día: 0h**

---

**Total semana: 0h**

---
"""

with open(worklog_path, 'w') as f:
    f.write(content)
```

**WORKLOG.md Format Example**:

```markdown
# Work Log

Registro detallado de actividades de desarrollo.

## Semana: 2024-01-15 a 2024-01-21

### Lunes 2024-01-15

| Inicio | Fin | Horas | Actividad | Commit(s) |
|--------|-----|-------|-----------|-----------|
| 12:00 | 13:30 | 1.5h | Diseño spec seguridad (biometría + 2FA) | 9bbbd55, 3e756e9 |
| 13:30 | 15:00 | 1.5h | Plan implementación seguridad detallado | 0199989 |
| 15:00 | 16:00 | 1h | Backend: migración, User model, BiometricService | - |

**Total día: 4h**

---

### Martes 2024-01-16

| Inicio | Fin | Horas | Actividad | Commit(s) |
|--------|-----|-------|-----------|-----------|
| 09:00 | 12:00 | 3h | Frontend: Componentes UI (5 componentes) | - |

**Total día: 3h**

---

**Total semana: 7h**

---
```

## Dependencies

This skill depends on the xlsx skill for Excel file operations. Key requirements:
- openpyxl must be available
- LibreOffice for formula recalculation (optional, via xlsx skill's recalc.py)

## Error Handling

- If timesheet not found, offer to create one
- If WORKLOG.md not found, create it automatically
- If openpyxl not available, suggest installing it
- Always backup before modifying existing files
- If one file fails, still try to update the other and report partial success