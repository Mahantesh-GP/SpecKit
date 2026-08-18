# Spec-Kit: build_rllot_dashboard.py — Implementation Reference

**File:** `build_rllot_dashboard.py`
**Role:** Reads `rllot_data.json` → computes all stats → writes `RLLOT_Dashboard.html`
**Date captured:** 2026-08-12

Use this as the single reference when re-implementing the dashboard builder in any language or framework.

---

## 1. Entry Point & JSON Loading

```
BASE_DIR = directory containing build_rllot_dashboard.py
INPUT    = BASE_DIR / "rllot_data.json"   (or rllot_data_v2.json fallback)
OUTPUT   = BASE_DIR / "RLLOT_Dashboard.html"
TODAY    = date.today()   ← used for overdue detection and the header date stamp
```

### JSON file resolution order

For the filename `rllot_data.json`, candidates are tried in this order:

1. Absolute path (if given)
2. `BASE_DIR / "rllot_data.json"`
3. Relative path as-is
4. `BASE_DIR / "rllot_data_v2.json"` ← fallback version

First candidate that exists as a file is used. **Fatal exit** if none found.

### Validation

- Root of loaded JSON must be a `dict` — fatal exit otherwise.
- `json.JSONDecodeError` → fatal exit with message.

---

## 2. Constants

### SHEETS — initiative display names

```python
SHEETS = {
    "BackEnd_Unit_Testing":     "Backend Unit Testing",
    "Gen_Test_Case":            "Generate Test Cases",
    "Gen_Scripts_4_PostMan":    "Generate PostMan Scripts",
    "FrontEnd_Unit_Testing":    "Frontend Unit Testing",
    "Integration_Testing":      "Integration Testing",
    "Vuln_Discovery":           "Vulnerability Discovery",
}
```

Order matters — it controls the left-to-right tab order and the overview card order.

### STATUS_ORDER — canonical sort order for statuses

```python
["Completed", "Approved", "In Progress", "Open", "TBD", "On Hold", "Rejected", "NA", "Not Filled"]
```

Used in stacked bars and legends to guarantee consistent left-to-right / top-to-bottom ordering regardless of which statuses appear in the data.

### STATUS_COLOR — badge and bar colors

| Status | Background | Text |
|---|---|---|
| Completed | `#22c55e` | `#fff` |
| Approved | `#0ea5e9` | `#fff` |
| In Progress | `#f59e0b` | `#fff` |
| Open | `#3b82f6` | `#fff` |
| TBD | `#8b5cf6` | `#fff` |
| On Hold | `#f97316` | `#fff` |
| Rejected | `#ef4444` | `#fff` |
| NA | `#94a3b8` | `#fff` |
| Not Filled | `#fef9c3` | `#92400e` |

Any status not in this map falls back to `bg:#64748b, text:#fff`.

### ACTIVE_STATUSES

```python
{"Completed", "In Progress", "Open", "On Hold", "Not Filled"}
```

These are the only statuses that count toward the denominator in completion % calculations. `NA`, `TBD`, `Rejected` are excluded from both numerator and denominator.

### EXCLUDED_SUMMARY_METRICS

Raw-count metrics excluded from the initiative Metrics Summary cards (not meaningful as averages):

```python
{
    'Number of test cases generated',
    'Number of test scripts generated',
    'Number of unit tests generated',
    'Number of integration scenarios generated',
    'Number of APIs covered',
}
```

---

## 3. Pre-Processing: Summary Objects

At module load time (before any rendering), a `summary` dict is built for all 6 initiatives:

```python
summary[key] = {
    "label":       str,        # display name from SHEETS
    "total":       int,        # total row count
    "overall_pct": float,      # 0-100, 1 decimal
    "phases": {
        "p1": {"Completed": n, "In Progress": n, ...},
        "p2": {same},
        "p3": {same},
    },
    "rows": [normalized row dicts],
}
```

`normalize_rows()` is applied first — it backfills `num` with 1-based index for any row missing it, and skips non-dict entries.

---

## 4. Completion % Formulas

### `phase_completion(rows, phase) → float`

```
active = count of rows where phase status ∈ ACTIVE_STATUSES
comp   = count of rows where phase status == "Completed"
result = comp / active * 100   (0 if active == 0), rounded to 1 decimal
```

### `overall_progress(rows) → float`

```
For each phase in [p1, p2, p3]:
    compute phase_completion(rows, phase)

overall = average of 3 phase completion values, rounded to 1 decimal
Returns 0.0 if rows is empty.
```

### `pct(a, tot) → float`

```
round(a / tot * 100, 1)   if tot > 0,  else 0
```

Utility used throughout for single-fraction calculations.

---

## 5. Overdue Detection

### `is_overdue(status, eta_str) → bool`

Returns `True` only if:

1. `status ∈ {"Open", "In Progress"}` AND
2. `eta_str` is not empty/None AND
3. `date.today() > date.fromisoformat(eta_str)`

Returns `False` on any date parse error (`ValueError`, `TypeError`).

A row is considered overdue if ANY of these are overdue:

- Phase 1 status + `p1_eta`
- Phase 2 status + `p2_eta`
- Phase 3 status + `p3_eta`
- Customer Approval status + `prereq_date`

`Completed`, `NA`, `TBD` rows are **never** overdue regardless of date.

---

## 6. Metrics Helpers

### `norm_category(cat) → str`

| Input | Output |
|---|---|
| `None` / `""` | `"Other"` |
| `"covergae"` (typo) or `"coverage"` | `"Coverage"` |
| `"productivity"` | `"Productivity"` |
| `"quality"` | `"Quality"` |
| anything else | trimmed as-is |

### `is_lower_better(name) → bool`

Returns `True` if the metric name (case-insensitive) contains any of:
`"time"`, `"effort"`, `"hrs"`, `"hour"`, `"escape"`, `"maintenance"`, `"flaky"`

This flips the improvement formula so that a decrease in value is shown as a positive improvement.

### `is_post_avg_metric(name, category) → bool`

Returns `True` (show post-GenAI average, not improvement delta) for:

- Any metric in category `"Coverage"`
- Quality metrics with names: `"acceptance rate %"`, `"critical/high found pre-check-in"`, `"critical/high found pre check-in"`
- Any metric name containing `"cov"` or `"coverage"` (regardless of category)

### `calc_improvement(pre, post, name) → float | None`

```python
if pre is None or post is None or pre == 0:
    return None

if is_lower_better(name):
    return (pre - post) / pre * 100    # positive = good (value reduced)
else:
    return (post - pre) / pre * 100    # positive = good (value increased)
```

**Known gap:** When `pre == 0`, returns `None` — shows "No Data". Business spec (`METRICS_CALCULATION_SCENARIOS.md`) defines a sprint-to-sprint fallback for this case that is not yet implemented.

### `is_plain_number_metric(name) → bool`

Returns `True` for `"critical/high found pre-check-in"` / `"critical/high found pre check-in"`.
These render without a `%` suffix in the summary card (plain number, not percentage).

### `aggregate_metrics(rows) → dict`

Aggregates metrics across all rows in an initiative:

1. For each row → each metric: accumulate `total_pre`, `total_post`, `count` (only pairs where both `pre` and `post` are non-null).
2. Post-processing:
   - `display_kind == "post_avg"` → `avg_post = total_post / count`, `improvement = None`
   - `display_kind == "improvement"` and `total_pre != 0` → `improvement = calc_improvement(total_pre, total_post, name)`, `avg_post = None`
   - Otherwise → both `None`

**Important:** `count` is the number of rows that had both pre+post values — it is shown as `{count} / {total} reported` on the metric card.

### Productivity metric per initiative (hardcoded mapping)

Only one Productivity metric is shown in the table column per initiative:

| Initiative | Metric name |
|---|---|
| BackEnd_Unit_Testing | `Unit test creation effort Hrs` |
| Gen_Test_Case | `Total Effort Hrs required to create test cases` |
| Gen_Scripts_4_PostMan | `Time required to Generate API Collections` |
| FrontEnd_Unit_Testing | `Total Effort Hrs required to create UI unit tests` |
| Integration_Testing | `Total time required to create integration tests` |
| Vuln_Discovery | `Time taken to run vulnerability` |

The table column for Productivity filters to **only** this metric (case-insensitive match) and ignores all other Productivity metrics when building the column value.

---

## 7. SVG Donut Generation

### `donut_svg(pct_val, size, stroke, color, arc_id, txt_id)`

Formula:

```
r    = (size - stroke) / 2
circ = π x 2 x r          (uses 3.14159, not math.pi)
dash = circ x pct_val / 100
```

SVG structure:

- Grey background circle (`stroke:#e2e8f0`)
- Colored arc circle (`stroke-dasharray="{dash} {circ}"`, rotated -90°)
- Center text (`{pct_val}%`)

**N/A state:** if `pct_val == "N/A"` or `None`, renders grey ring only with `"N/A"` text in `#64748b`.

`arc_id` and `txt_id` are assigned to the arc `<circle>` and `<text>` elements respectively — these are used by client-side JavaScript to animate filter updates without re-rendering HTML.

### Size configurations used in the codebase

| Usage | size | stroke | r | circ |
|---|---|---|---|---|
| Phase card donut | 64 | 8 | 28 | ~175.9 |
| Overall initiative donut | 72 | 9 | 31.5 | ~197.9 |
| Overview KPI donut | 80 | 10 | 35 | ~219.9 |
| Overview activity card | 72 | 9 | 31.5 | ~197.9 |

JavaScript must use the same radius values when updating dasharray on filter change.

---

## 8. HTML Rendering Pipeline

### 8.1 `render_tab(key) → str`

Produces one initiative tab panel `<div id="tab-{key}" class="tab-panel">`. The render order inside the tab:

1. `initiative-top-shell` div
   - `tab-header` (overall donut + title + team count + overall %)
   - `filter-bar` (phase status dropdowns + overdue + search + reset + count)
2. `initiative-insights` div (2-column grid)
   - `phase-panel` (3 phase cards: donut + stacked bar + legend)
   - `metrics-panel` (aggregated metric cards)
3. `<details class="sec-collapsible" open>`
   - team detail table
     - tbody rows (one `<tr>` per team)

#### Phase card tooltip

Each phase card `title` attribute contains the full calculation:

```
Calculation: {completed} completed / {active} active * 100 = {pct}% | Breakdown: Completed: n, In Progress: n, ...
```

The breakdown respects `STATUS_ORDER`. `html.escape()` applied.

#### Row ID scheme

```
rid = "{sheet_key}_{1-based-index}"
e.g. "BackEnd_Unit_Testing_3"
```

Used as the key in `all_row_data` dict and as the argument to `openDrawer()`.

#### `<tr>` data attributes (used for client-side JS filtering)

| Attribute | Value |
|---|---|
| `data-rid` | Row ID (e.g. `BackEnd_Unit_Testing_3`) |
| `data-org` | Org name (HTML-escaped) |
| `data-dr` | DR name (HTML-escaped) |
| `data-pm` | PM name (HTML-escaped) |
| `data-overdue` | `"yes"` or `"no"` |
| `data-ca` | Customer approval status |
| `data-p1` | Phase 1 status |
| `data-p2` | Phase 2 status |
| `data-p3` | Phase 3 status |
| `data-p1od` | `"yes"` or `"no"` (Phase 1 overdue) |
| `data-p2od` | `"yes"` or `"no"` (Phase 2 overdue) |
| `data-p3od` | `"yes"` or `"no"` (Phase 3 overdue) |
| `data-search` | `"{team} {project} {org} {pm} {dr}"` concatenated for free-text search |
| `data-metrics` | Full metrics array as JSON, with `"` replaced by `&quot;` |

A row with any overdue phase/approval gets CSS class `row-overdue` (`background:#fff7f7`).

#### Metric columns in the table (per row)

Three metric columns: Productivity, Quality, Coverage.

- For **Productivity**: only the initiative's designated metric (§6 mapping) is included; case-insensitive name match.
- For **Quality**: `is_post_avg_metric` check drives whether post value or improvement is used.
- For **Coverage**: always shows post average.

Display format:

- Coverage → `"{avg:.1f}%"` (no sign)
- Quality (post_avg) → `"{avg:.1f}%"` (no sign)
- Productivity / other improvement → `"+{avg:.1f}%"` or `"-{avg:.1f}%"` (signed)
- No data → `"–"` in `#CBD5E1`

Color: `#16A34A` if avg ≥ 0, `#DC2626` if avg < 0.

#### Detail drawer body structure (`all_row_data[rid]`)

```python
{
    "title": "#{num}  {team}",
    "sub":   "{project} · {org}",
    "body":  HTML string containing:
             1. Metrics Explanation section
             2. Metrics section (Before/After cards per metric)
             3. Remarks section (only if remarks non-empty)
    "metrics": [...raw metrics array...],
}
```

Metric card drawer display:

- Grouped by category order: `Productivity`, `Quality`, `Coverage`, `Other`
- Each card: category label, metric name, Before value, After value (colored), change % arrow
- Change % format: `↑{n}%` (green) or `↓{n}%` (red) or `"No change data"` (grey)
- After value color: `#16A34A` if improvement ≥ 0, `#DC2626` if < 0, `#1e293b` if no improvement data

Explanation deduplication: multiple metrics in a row share the same explanation text. The drawer renders only unique explanation strings (insertion order preserved, `set` used for seen tracking).

### 8.2 `render_overview() → str`

Produces the `<div id="tab-overview" class="tab-panel active">` panel. Contains:

1. **KPI row — 7 boxes:**
   - Overall completion donut (aggregated across all sheets)
   - Total Team-Initiatives
   - Fully Completed (all 3 phases)
   - Phase 1 Completed (count / active)
   - Phase 2 Completed (count / active)
   - Phase 3 Completed (count / active)
   - Teams with Due Phases (overdue count)
2. **Activity summary cards** — one per initiative, clicking calls `switchTab(key)`.
   Card shows: overall % donut, label, team count, P1/P2/P3 completed counts, P3 stacked bar, category metric boxes.
3. **Activity info section** — static descriptive cards explaining what each initiative means.

#### Overview KPI aggregation formula

```
agg_denom = count of all active rows across all phases across all sheets
            (each sheet contributes p1_active + p2_active + p3_active)
agg_pct   = (p1_completed + p2_completed + p3_completed) / agg_denom * 100
```

Note: this is **different** from `overall_progress()` per initiative. The overview KPI is a flat weighted fraction; per-initiative uses phase-averaged percentage.

#### Overview card metric boxes (3 categories)

For the Overview activity cards, Quality is limited to only `"acceptance rate %"` (all other Quality metrics are excluded from the overview card summary to keep it concise).

---

## 9. Global Filter Bar

Three dropdowns: Organization, DR, PM.
These filter all 6 initiative tables simultaneously.
Filter values are populated at build time from all rows across all sheets (sorted, deduplicated).

Client-side: changing any global dropdown calls `applyGlobalFiltersToAllTabs()` which calls `applyFilters(key)` for each sheet key, then `updateOverviewMetrics()`.

`window._allActivityData` is a JSON dict embedded in the page:

```json
{
    "BackEnd_Unit_Testing": [{"org": "...", "dr": "...", "pm": "..."}, ...],
    ...
}
```

This is used by the JavaScript to cascade/repopulate global filter dropdowns dynamically.

---

## 10. Tab Navigation

Tab buttons are generated with `data-tab` attribute and `onclick="switchTab('{key}')"`.
Each tab button except Overview shows the initiative's `overall_pct` in a `.tab-pct` badge span.

`switchTab(id)` in JavaScript:

1. Hides all `.tab-panel` elements.
2. Removes `active` from all `.tab-btn` elements.
3. Shows `#tab-{id}` and activates the matching button.
4. If switching to a non-overview tab, calls `applyFilters(key)` to initialize filter state.

---

## 11. Client-Side JavaScript: Filter Logic

All filtering is done client-side by toggling `display:none` on `<tr>` rows.

### Per-initiative filters

- Phase 1/2/3 status dropdowns → match `row.dataset.p1/p2/p3`
- Overdue dropdown → match `row.dataset.overdue` (`"yes"` / `"no"`)
- Free-text search → case-insensitive substring match against `row.dataset.search`
- Customer Approval (`data-ca`) → cascading dropdown — repopulates valid options based on other filters

### Global filters

- Org / DR / PM dropdowns → match `row.dataset.org / dr / pm`
- Applied to all tabs simultaneously

### After-filter updates (all called after each filter change)

1. `updatePhaseCards(key, visibleRows)` — recomputes phase completion % and updates donut arcs, stacked bars, legends, overdue badge counts using live DOM data.
2. `updateOverallMetrics(key, visibleRows)` — recomputes overall % and updates the initiative header donut + tab button badge.
3. `updateMetricsSummary(key, visibleRows)` — re-aggregates metrics from visible rows (reads `row.dataset.metrics` JSON) and rebuilds the metrics summary cards.
4. `updateOverviewMetrics()` — rebuilds the Overview tab KPIs and all activity card donuts/bars/metrics from the currently visible rows across all tabs.

### Donut arc update formula (JavaScript must match Python)

```javascript
// Phase cards: size=64, stroke=8, r=28
DONUT_CIRC = 2 * 3.14159 * 28 ≈ 175.9

arc.setAttribute('stroke-dasharray',
    (DONUT_CIRC * pct / 100).toFixed(1) + ' ' + DONUT_CIRC)

// Overall initiative ring: size=72, stroke=9, r=31.5
OVERALL_CIRC = 2 * 3.14159 * 31.5 ≈ 197.9

// Overview KPI ring: size=80, stroke=10, r=35
MAIN_CIRC = 2 * 3.14159 * 35 ≈ 219.9
```

### Column sort

Every `<th>` in every `.data-table` is made sortable by click. Sort is alphabetical with numeric detection (tries `parseFloat`; falls back to `localeCompare`). Toggling click alternates ascending/descending.

---

## 12. Drawer (Detail Panel)

A single shared drawer is used for all rows across all initiatives:

- `<div class="drawer-overlay" id="shared-overlay">` — backdrop, click closes
- `<div class="detail-drawer" id="shared-drawer">` — modal panel

`openDrawer(rid)`:

1. Looks up `window._rowData[rid]` (embedded at page load in `row_data_script`).
2. Sets `sd-title`, `sd-sub`, `sd-body` innerHTML.
3. Adds `.open` class to overlay and drawer (CSS transition handles animation).
4. Disables body scroll.
5. Focuses the close button after 50ms (accessibility).

`closeDrawer()`: removes `.open`, restores body scroll.
Keyboard: `Escape` key closes the drawer.

`window._rowData` is generated at Python build time:

```javascript
window._rowData = { "BackEnd_Unit_Testing_1": { title, sub, body, metrics }, ... }
```

`body` is a pre-rendered HTML string generated in Python — it is set via `.innerHTML` on the drawer body, so all content must be properly escaped at generation time.

---

## 13. Security Implementation

### HTML escaping

Two escaping paths exist:

1. `html.escape()` — Python stdlib, used in metric card name/value strings and tooltips.
2. Local `esc()` lambda — manual 4-character escape (`&`, `<`, `>`, `"`) used inside `render_tab()` for all row string fields written into `<tr>` attributes and cells.

These must be applied to **all** user-originated string data (Excel cell values) before writing into HTML. No raw string values should appear unescaped in any attribute or text node.

### JavaScript escaping

`escapeHtml()` function is defined in inline JS using `div.textContent = text; return div.innerHTML` — standard DOM-based escaping. Used when JavaScript dynamically rebuilds metric card HTML from `window._rowData` or `row.dataset.metrics`.

### Content Security Policy (meta tag)

```html
<meta http-equiv="Content-Security-Policy"
    content="default-src 'none'; script-src 'unsafe-inline'; style-src 'unsafe-inline';
             img-src data: blob:; font-src 'self' data:;">
```

Blocks all external resource loads. `unsafe-inline` is required for the self-contained design. No `nonce` or `hash` is used.

### No innerHTML sinks from user data

`openDrawer()` sets `sd-body.innerHTML = d.body` where `d.body` is pre-rendered Python HTML (already escaped at generation). No raw Excel string is ever passed directly to `innerHTML` at runtime.

---

## 14. Output

```python
with open("RLLOT_Dashboard.html", "w", encoding="utf-8") as f:
    f.write(html)
```

- Single file, fully self-contained (all CSS, JS, SVG, data inline).
- No external assets, CDN references, or API calls.
- Safe to open as `file://` or serve over HTTP.
- Typical file size: several hundred KB (scales with team count).

---

## 15. Key Implementation Constraints for Re-implementation

| # | Constraint | Why it matters |
|---|---|---|
| C-01 | `STATUS_ORDER` must be used everywhere statuses are rendered | Controls consistent visual order in bars, legends, dropdowns |
| C-02 | `ACTIVE_STATUSES` controls both numerator and denominator of completion % | NA/TBD/Rejected rows must not affect the % |
| C-03 | `overall_progress` = average of 3 phase %s, not flat total | Per-initiative % differs from Overview KPI aggregation |
| C-04 | `is_overdue` must return False for Completed/NA/TBD regardless of date | Past dates on completed rows are not alerts |
| C-05 | Donut arc IDs must match the pattern expected by JS | JS updates arcs by ID; mismatched IDs cause silent breakage after filtering |
| C-06 | `row.dataset.metrics` must be valid JSON with `"` → `&quot;` | JS parses this for live metric recalculation after filtering |
| C-07 | `window._rowData` must be embedded as `window._rowData = {...}` | `openDrawer()` reads from this global |
| C-08 | `window._allActivityData` must be embedded for global filter cascading | JS uses it to repopulate org/dr/pm dropdowns without reading the table |
| C-09 | `EXCLUDED_SUMMARY_METRICS` must be applied in both Python and JS | Both build-time cards and filter-update cards must exclude these |
| C-10 | `is_post_avg_metric` logic must be mirrored exactly in JS | JS recalculates metrics after filtering — must match Python's `display_kind` |
| C-11 | `is_lower_better` keyword list must be identical in Python and JS | Divergence causes filter-updated values to differ from initial render |
| C-12 | `norm_category` typo variant `"covergae"` must be handled | Appears in real data; must not be classified as `"Other"` |
| C-13 | Productivity column uses hardcoded metric name per initiative | Only one metric drives the table column value, not an average of all Productivity metrics |
| C-14 | Explanation deduplication uses insertion-order-preserving set | Multiple metrics share one explanation; only unique strings are shown |
| C-15 | All user strings from Excel must be HTML-escaped before any HTML context | OWASP XSS prevention — no exceptions |
