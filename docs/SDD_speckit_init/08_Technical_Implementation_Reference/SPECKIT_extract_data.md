# Spec-Kit: extract_data.py — Implementation Reference

**File:** `extract_data.py`
**Role:** Reads all DR Excel files → normalises data → writes `rllot_data.json`
**Date captured:** 2026-08-12

Use this document as the single reference when re-implementing the extractor in any language or framework.

---

## 1. Entry Point & File Discovery

```
BASE_DIR  = directory containing extract_data.py
INPUT_DIR = BASE_DIR / "input"        ← hardcoded subfolder name
OUTPUT    = BASE_DIR / "rllot_data.json"
```

### File discovery rules

- Scans `input/` for `*.xlsx` using `glob` (case-insensitive on Windows).
- Results are **sorted alphabetically** before processing — output order is deterministic.
- **Fatal exits** (non-zero exit code) if:
  - `input/` directory does not exist
  - `input/` is not a directory
  - No `.xlsx` files found inside `input/`
- **Warns and continues** (skips file) if a specific `.xlsx` file cannot be opened by openpyxl.

### DR name parsing from filename

Pattern: `GenAI SDLC Initiatives Roll Out Tracker - <DR Full Name>.xlsx`

```
regex: r"-\s*(.+?)\.xlsx$"   (case-insensitive)
```

Captures everything after the last `-` up to `.xlsx`.
Fallback: if regex finds no match, uses filename minus `.xlsx` extension.

| Filename | Parsed DR |
|---|---|
| `…Tracker - Kunal Shekhar.xlsx` | `Kunal Shekhar` |
| `…Tracker - Rupali Patil.xlsx` | `Rupali Patil` |
| `MyFile.xlsx` (no dash) | `MyFile` |

---

## 2. Target Sheets

The extractor processes exactly these 6 sheets in this order. Any sheet missing from a workbook generates a warning (not an error); processing continues with the remaining sheets.

```
SHEETS = [
    "BackEnd_Unit_Testing",
    "Gen_Test_Case",
    "Gen_Scripts_4_PostMan",
    "Integration_Testing",
    "Vuln_Discovery",
    "FrontEnd_Unit_Testing",
]
```

All 6 sheets share an **identical column layout** — one extractor function handles all of them.

---

## 3. Sheet Structure & Header Detection

### Header row detection

The extractor scans rows 0–10 (max 11 rows) looking for the first row where `cell[0].strip() == "#"`.
That row is the **header row**. Detection stops immediately when found.
If no header row is found in the first 11 rows → sheet returns empty `[]` (warning not printed, silently skipped).

### Category row

The row immediately above the header row (`header_idx - 1`) is the category row.
It provides metric category labels (`Productivity`, `Quality`, `Coverage`) above the metric columns.
If the header is on row 0, there is no category row (empty tuple used as fallback).

### Column layout (fixed indices, 0-based)

| Index | Field | Notes |
|---|---|---|
| 0 | Row number `#` | Must be castable to `int`; rows that fail are silently skipped |
| 1 | Org | Defaults to `"Unknown"` if blank |
| 2 | Project | Defaults to `"Unknown"` if blank |
| 3 | Unit | Empty string if blank |
| 4 | Team | Empty string if blank |
| 5 | PM | Empty string if blank |
| 6 | Members | Empty string if blank |
| 7 | (not extracted) | Column exists in sheet but is skipped |
| 8 | Customer Approval | `norm_ca()` applied; guarded by `len(row) > 8` |
| 9 | Prereq Date | `fmt()` → `"YYYY-MM-DD"` or `None`; guarded by `len(row) > 9` |
| 10 | Phase 1 Status | `norm()` applied; guarded by `len(row) > 10` |
| 11 | Phase 1 ETA | `fmt()` → `"YYYY-MM-DD"` or `None`; guarded by `len(row) > 11` |
| 12 | Phase 2 Status | `norm()` applied; guarded by `len(row) > 12` |
| 13 | Phase 2 ETA | `fmt()` → `"YYYY-MM-DD"` or `None`; guarded by `len(row) > 13` |
| 14 | Phase 3 Status | Special rule: if cell value is a `datetime` instance → `"Open"` (not run through `norm()`) |
| 15 | Phase 3 ETA | `fmt()` → `"YYYY-MM-DD"` or `None`; guarded by `len(row) > 15` |
| 16 | Remarks | `sv()` (strip); guarded by `len(row) > 16` |
| 17+ | Metric columns | Dynamic — discovered from header row (see §4) |

> **Critical:** Columns 0–16 are positional. Any column insertion in the Excel sheet before column 17 will silently misalign all field mappings. This is Pain Point P-03.

---

## 4. Metric Column Discovery (Dynamic, from col 17 onward)

Metric columns are discovered at runtime by scanning the header row from index 17 onward.

### Pairing logic

Each metric exists as two paired columns:

```
<Metric Name> (Pre GenAI)   → baseline value
<Metric Name> (Post GenAI)  → post-adoption value
```

The base metric name is produced by stripping the suffix:

```
regex (Pre):  r"\s*\(Pre GenAI\)\s*"   (case-insensitive)
regex (Post): r"\s*\(Post GenAI\)\s*"  (case-insensitive)
```

Both `Pre` and `Post` columns must have the same base name to be paired.
If a `Post` column has no matching `Pre` → it is ignored.
If a `Pre` column has no matching `Post` → `post` value is stored as `None`.

### Category assignment

The category row (row above the header) assigns a category to each metric column.

Rules:
- Walking left-to-right, the current category is sticky — it carries forward until a new non-empty category cell is found.
- Default category if none is found: `"Productivity"`.
- Only the `Pre` column's position sets the category; Post column inherits the same category.

### Explanation column

Any header at index ≥ 17 that contains `"metrics explanation"` or `"metrics explaination"` (case-insensitive, typo variant both handled) is the explanation column.
Its value is shared across all metrics in the same row (one explanation per row, not per metric).

---

## 5. Row Iteration & Termination

After the header row, the extractor iterates remaining rows with these rules:

| Condition | Action |
|---|---|
| Row is `None` or `row[0]` is falsy | Increment `empty_streak`; if `empty_streak >= 5` → stop iteration |
| Row[0] cannot be cast to `int` | Skip row silently (e.g. sub-header rows, label rows) |
| Valid numeric row[0] | Reset `empty_streak = 0`; extract row |

The `empty_streak` counter prevents reading past the real data when there are occasional blank rows mid-sheet, but stops after 5 consecutive blanks.

---

## 6. Status Normalisation

### `norm(v)` — Phase status columns (P1, P2, P3)

| Raw value (case-insensitive) | Output |
|---|---|
| `None` | `"Not Filled"` |
| `""` (blank after strip) | `"Not Filled"` |
| `"in progress"` or `"in-progress"` | `"In Progress"` |
| `"completed"` | `"Completed"` |
| `"open"` | `"Open"` |
| `"tbd"` | `"TBD"` |
| `"na"` or `"n/a"` | `"NA"` |
| `"hold"` or `"on hold"` | `"On Hold"` |
| `datetime` instance | `"Open"` |
| Any other non-blank string | Returned as-is (preserve unknown values) |

> **Note:** The `datetime` → `"Open"` branch in `norm()` is checked **after** the string comparisons. However for **Phase 3 specifically** (col 14), the datetime check is done **before** calling `norm()` at the call site — so Phase 3 never receives a datetime inside `norm()`.

### `norm_ca(v)` — Customer Approval column (col 8)

Extends `norm()` with extra mappings checked first:

| Raw value (case-insensitive) | Output |
|---|---|
| `None` | `"Not Filled"` |
| `"na"` or `"n/a"` | `"NA"` |
| `"not yet provided"` / `"yet to request"` / `""` | `"Not Filled"` |
| `"approved"` | `"Approved"` |
| `"hold"` / `"on hold"` | `"On Hold"` |
| `"rejected"` | `"Rejected"` |
| `"completed"` | `"Completed"` |
| `"open"` | `"Open"` |
| `"tbd"` | `"TBD"` |
| `"in progress"` | `"In Progress"` |
| Unrecognised non-blank string | Returned as-is |

---

## 7. Date Formatting

### `fmt(v)` — converts Excel date cells to string

```
datetime instance → "YYYY-MM-DD"   (strftime "%Y-%m-%d")
anything else     → None
```

openpyxl returns date-formatted Excel cells as Python `datetime` objects when `data_only=True`. Non-date cells (strings, None) pass through as `None`.

---

## 8. Metric Value Rules

| Excel cell type | Stored as |
|---|---|
| `int` or `float` | `float(value)` |
| `None`, string, datetime, error | `None` |

Values are **not clamped, validated, or scaled**. Whatever number is in the cell is stored as-is. The comment in the code explicitly states: all values are entered as actual percentages (e.g. `2.43` means `2.43%`, `100` means `100%`) — no conversion is applied.

`is_percentage_metric()` and `normalize_percentage_value()` exist in the file but are **not called anywhere** — they are dead code and can be omitted in a reimplementation.

---

## 9. Output JSON Structure

### Top-level keys

```json
{
  "BackEnd_Unit_Testing":     [ ...rows ],
  "Gen_Test_Case":            [ ...rows ],
  "Gen_Scripts_4_PostMan":    [ ...rows ],
  "Integration_Testing":      [ ...rows ],
  "Vuln_Discovery":           [ ...rows ],
  "FrontEnd_Unit_Testing":    [ ...rows ],
  "_meta": {
    "generated": "YYYY-MM-DD",
    "files": ["filename1.xlsx", "filename2.xlsx", ...],
    "sheets": ["BackEnd_Unit_Testing", ...]
  }
}
```

Sheet keys are **merged across all DR files** — rows from all 5 DR files under `BackEnd_Unit_Testing` are concatenated into one array.

### Row object schema

```json
{
  "num": 1,
  "org": "FNFTP",
  "project": "TitlePointApps",
  "unit": "TitlePoint",
  "team": "TitlePoint",
  "pm": "Suravarapu Surya Phaninder",
  "members": "Vikas Kumar Benda, ...",
  "dr": "Ajit Bhardwaj",
  "customer_approval": "Completed",
  "prereq_date": "2026-07-14",
  "p1": "Completed",
  "p1_eta": "2026-02-27",
  "p2": "Completed",
  "p2_eta": "2026-04-27",
  "p3": "Completed",
  "p3_eta": "2026-05-13",
  "remarks": "free text...",
  "metrics": [
    {
      "name": "Unit test creation effort Hrs",
      "category": "Productivity",
      "pre": 42.0,
      "post": 18.5,
      "explanation": "free text shared by all metrics in this row"
    }
  ]
}
```

### Field defaults when data is missing

| Field | Default |
|---|---|
| `org` | `"Unknown"` |
| `project` | `"Unknown"` |
| `unit`, `team`, `pm`, `members`, `remarks` | `""` (empty string) |
| `customer_approval` | `"Not Filled"` |
| `prereq_date`, all `*_eta` fields | `null` |
| `p1`, `p2` | `"Not Filled"` |
| `p3` | `"Not Filled"` (via `norm()`) or `"Open"` (if cell was a datetime) |
| `metrics[].pre`, `metrics[].post` | `null` |
| `metrics[].explanation` | `""` |

---

## 10. Workbook Loading

```
openpyxl.load_workbook(path, data_only=True, read_only=True)
```

- `data_only=True` — returns calculated cell values (not formulas). Required; without it, formula cells return `None`.
- `read_only=True` — memory-efficient streaming mode. Do not call `.cell()` on worksheets in this mode; use `.iter_rows()` only.
- The workbook is explicitly closed (`wb.close()`) after each file to release file handles.

---

## 11. Edge Cases to Handle in Re-implementation

| Case | Current behaviour | Must preserve? |
|---|---|---|
| Column count shorter than expected index | `cell(idx)` returns `None` via bounds check; normalisation produces default | Yes |
| Phase 3 cell is a datetime | Detected before `norm()` call → `"Open"` | Yes |
| `num` cell is a float like `1.0` | `int(1.0)` succeeds → row is kept with `num=1` | Yes |
| `num` cell is a string like `"Total"` | `int("Total")` raises → row silently skipped | Yes |
| Metric has Pre but no Post column | `post` stored as `None` | Yes |
| Metric has Post but no Pre column | Metric is not stored at all (Pre drives the loop) | Yes |
| Same sheet in multiple DR files | Rows are concatenated (not deduped) | Yes |
| Explanation column missing | `explanation` stored as `""` for all metrics in all rows | Yes |
| `""` explanation vs `None` | Always stored as `""` (never `null`) | Yes |
| `is_percentage_metric` / `normalize_percentage_value` | Dead code — not called | Omit |

---

## 12. What a Re-implementation Must Replicate

Minimum contract for any replacement extractor:

1. Produce identical JSON schema (§9).
2. Apply identical `norm()` and `norm_ca()` rules (§6).
3. Parse DR name from filename using the same regex (§1).
4. Use `data_only=True` when reading Excel (§10).
5. Detect header row by scanning for `cell[0] == "#"` in the first 11 rows (§3).
6. Treat Phase 3 datetime as `"Open"` at the call site, before normalisation (§3 column layout).
7. Discover metric column pairs dynamically — do not hardcode metric column positions (§4).
8. Assign metric category from the category row with left-to-right sticky inheritance (§4).
9. Share one explanation string across all metrics in the same row (§4).
10. Stop row iteration after 5 consecutive blank rows (§5).
