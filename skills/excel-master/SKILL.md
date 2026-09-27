---
name: excel-master
description: "Use when creating, editing, or analyzing Excel spreadsheets (.xlsx, .xlsm, .csv, .tsv). For building financial models with formulas, reading data for analysis, modifying existing files while preserving formatting, performing data visualization, and recalculating formulas with error detection."
---

# Excel Master

> Comprehensive spreadsheet creation, editing, and analysis with professional-grade formula handling, formatting standards, and data visualization.

## Overview

This skill enables you to work with Excel files (.xlsx) using Python, covering:
- **Creating** new spreadsheets with formulas, formatting, and data
- **Editing** existing files while preserving formulas and styles
- **Analyzing** data using pandas
- **Recalculating** formulas with LibreOffice and detecting errors

## Output Requirements

### Zero Formula Errors
Every Excel model MUST be delivered with ZERO formula errors (#REF!, #DIV/0!, #VALUE!, #N/A, #NAME?)

### Preserve Existing Templates
When modifying files, EXACTLY match existing format, style, and conventions. Existing template conventions ALWAYS override these guidelines.

---

## Financial Model Standards

### Color Coding (Industry Standard)

| Color | Usage |
|:------|:------|
| **Blue text** (0,0,255) | Hardcoded inputs, scenario values |
| **Black text** (0,0,0) | ALL formulas and calculations |
| **Green text** (0,128,0) | Links from other worksheets |
| **Red text** (255,0,0) | External links to other files |
| **Yellow background** (255,255,0) | Key assumptions needing attention |

### Number Formatting

| Type | Format | Example |
|:-----|:-------|:--------|
| Years | Text strings | "2024" not "2,024" |
| Currency | $#,##0 | Specify units in headers: "Revenue ($mm)" |
| Zeros | Show as "-" | Format: `$#,##0;($#,##0);-` |
| Percentages | 0.0% | One decimal default |
| Multiples | 0.0x | EV/EBITDA, P/E ratios |
| Negatives | Parentheses | (123) not -123 |

---

## Core Workflows

### Library Selection

- **pandas**: Best for data analysis, bulk operations, simple export
- **openpyxl**: Best for complex formatting, formulas, Excel-specific features

### Workflow Steps

1. **Choose tool**: pandas for data, openpyxl for formulas/formatting
2. **Create/Load**: Create new workbook or load existing file
3. **Modify**: Add/edit data, formulas, and formatting
4. **Save**: Write to file
5. **Recalculate** (MANDATORY IF USING FORMULAS):
   ```bash
   python scripts/recalc.py output.xlsx
   ```
6. **Verify and fix errors**: Check JSON output for error locations

---

## Critical Rule: Formulas Over Hardcoding

**Always use Excel formulas instead of calculating in Python.**

### ❌ WRONG
```python
total = df['Sales'].sum()
sheet['B10'] = total  # Hardcodes 5000
```

### ✅ CORRECT
```python
sheet['B10'] = '=SUM(B2:B9)'
```

---

## Code Examples

### Creating New Files

```python
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment

wb = Workbook()
sheet = wb.active

# Add data and formula
sheet['A1'] = 'Sales'
sheet['B1'] = 100
sheet['B2'] = '=SUM(B1:B10)'

# Formatting
sheet['A1'].font = Font(bold=True, color='FF0000')
sheet['A1'].fill = PatternFill('solid', start_color='FFFF00')
sheet.column_dimensions['A'].width = 20

wb.save('output.xlsx')
```

### Editing Existing Files

```python
from openpyxl import load_workbook

wb = load_workbook('existing.xlsx')
sheet = wb.active

# Modify
sheet['A1'] = 'New Value'
sheet.insert_rows(2)

# Add new sheet
new_sheet = wb.create_sheet('NewSheet')
new_sheet['A1'] = 'Data'

wb.save('modified.xlsx')
```

### Data Analysis with pandas

```python
import pandas as pd

# Read Excel
df = pd.read_excel('file.xlsx')
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)

# Analyze
df.describe()  # Statistics

# Write
df.to_excel('output.xlsx', index=False)
```

---

## Formula Recalculation

Use the `recalc.py` script to recalculate formulas:

```bash
python scripts/recalc.py <excel_file> [timeout_seconds]
```

### Output Format

```json
{
  "status": "success",
  "total_errors": 0,
  "total_formulas": 42,
  "error_summary": {}
}
```

### Error Types

| Error | Cause |
|:------|:------|
| `#REF!` | Invalid cell references |
| `#DIV/0!` | Division by zero |
| `#VALUE!` | Wrong data type in formula |
| `#NAME?` | Unrecognized formula name |

---

## Verification Checklist

- [ ] Test 2-3 sample references before building full model
- [ ] Verify column mapping (column 64 = BL, not BK)
- [ ] Check row offset (DataFrame row 5 = Excel row 6)
- [ ] Handle NaN with `pd.notna()`
- [ ] Check denominators for division (#DIV/0!)
- [ ] Verify cross-sheet references format (Sheet1!A1)

---

## Best Practices

### openpyxl Tips
- Cell indices are 1-based (row=1, column=1 = A1)
- Use `data_only=True` to read calculated values
- **Warning**: Opening with `data_only=True` and saving loses formulas
- For large files: Use `read_only=True` or `write_only=True`

### pandas Tips
- Specify data types: `pd.read_excel('file.xlsx', dtype={'id': str})`
- Read specific columns: `usecols=['A', 'C', 'E']`
- Handle dates: `parse_dates=['date_column']`

### Code Style
- Write minimal, concise Python code
- Avoid verbose variable names
- Add comments to cells with complex formulas
- Document data sources for hardcoded values
