# Discovery vs. Implementation Report

> **Note:** The source screenshots for this transcription begin partway through the document, at Section 2. Section 1 was not captured and is not included below.

## 2. Data Model — Implemented vs. Discovered

### Concepts Present in the Implementation

| Discovery Concept | Implementation Detail |
|---|---|
| Initiative | 6 hardcoded sheets: Backend Unit Testing, Gen Test Cases, PostMan Scripts, Frontend Unit Testing, Integration Testing, Vulnerability Discovery |
| Team | Primary tracking unit with org, project, PM, DR, members fields |
| Rollout Phase | 3 phases (P1/P2/P3) per team per initiative, with ETA dates |
| Adoption Metric | Pre/Post GenAI metrics across Productivity, Quality, Coverage categories |
| Organization Unit / DR | Two-level: Org → DR, used as filter dimensions |
| Status / Health | Per-phase status: Completed, In Progress, Open, On Hold, TBD, NA, Not Filled |
| Customer Approval | Tracked as an additional approval gate (beyond discovery scope) |

### Concepts Missing from the Implementation

| Discovery Concept | Gap |
|---|---|
| Milestone | No milestone entity. ETA dates exist per-phase but there is no named, measurable milestone object with a threshold |
| Blocker | No blocker tracking — no record, owner, age, or resolution state |
| Engagement Event | Not tracked at all |
| Target | Phases are marked complete/incomplete; there are no numeric adoption targets to compare against |
| Historical Snapshot | Only current state is stored. No time-series data for trend computation |
| Data Source | All data sourced from a single Excel file; no multi-source abstraction |

## 3. Capability-by-Capability Gap Assessment

### Section I of Discovery — Candidate Capabilities

| Capability | Discovery Priority | Implementation Status | Notes |
|---|---|---|---|
| Exception surfacing | High | ⚠️ Partial | Overdue flagging implemented (21 teams flagged); no prioritized exception list or stall detection |
| Trend computation | High | ❌ Not Implemented | Only current state stored; no historical snapshots exist |
| Configurable health rules | High | ❌ Not Implemented | Completion thresholds hardcoded (`>=80` = green, `>=50` = amber, etc.) in `build_rllot_dashboard.py` |
| Multi-scope navigation | High | ✅ Implemented | Org, DR, PM, Phase Status, Overdue, and free-text search filters; global filters apply across all tabs |
| Milestone tracking & readiness | High | ⚠️ Partial | ETA-based overdue detection only; no milestone concept, no readiness score vs. date-adjusted target |
| Blocker lifecycle management | High | ❌ Not Implemented | No blocker entity; remarks field in team detail is the only proxy |
| Data freshness signaling | Medium | ⚠️ Partial | Single refresh date shown in header ("Refreshed on Aug 11, 2026"); no per-team or per-metric freshness |
| Scope management | Medium | ✅ Implemented | Team-initiative enrollment managed via Excel sheets; NA status used for out-of-scope teams |
| Temporal / historical record | High | ❌ Not Implemented | `rllot_data.json` reflects only the latest extraction |
| Role-based access control | High | ❌ Not Implemented | All data visible to all viewers; documented as accepted risk (JS-06 in README) |
| Audit trail | Medium | ❌ Not Implemented | No change tracking; Excel edits are not audited |
| Proactive alerting | Medium | ❌ Not Implemented | Dashboard is pull-only; no email, Teams, or push notification |
| Data completeness reporting | Medium | ⚠️ Partial | "Not Filled" status visible; metrics completion percentage calculated per team; no program-level coverage score |
| Configurable metric definitions | Low | ⚠️ Partial | Metrics driven by Excel column headers; but sheet names and category logic are hardcoded |
| Initiative and phase configuration | Low | ❌ Not Implemented | Adding a 7th initiative requires code change; phase names are hardcoded strings |

## 4. User Actor Coverage

| Actor | Discovery Needs | Current Coverage |
|---|---|---|
| Executive / Leadership | Aggregate progress vs. target, top blockers, at-risk org units | ⚠️ Partial — Overall % and KPI row visible, but no targets, no blocker list, no org-unit health |
| Rollout Program Manager | At-risk teams, changed-since-last-review, milestone readiness, blocker status | ⚠️ Partial — Overdue filter helps; no "changed since last review", no blockers |
| Delivery Manager / DR | Own team status, comparison vs. target, support requests | ✅ Good — DR filter, per-team detail modal, phase breakdown |
| Platform / Enablement | Early-stage teams, low-engagement clusters | ⚠️ Partial — Can filter by phase status but no cluster analysis |
| Team Lead (read-only) | Own team status, expectations, milestones | ✅ Good — DR + team search surfaces individual team view |
| System / Data Admin | Data quality, ingestion status, access management | ⚠️ Partial — "Not Filled" surfaces gaps; no ingestion pipeline status or admin UI |

## 5. Business Invariants — Compliance Check

The discovery (Section E) identified 8 business invariants that likely hold regardless of design. Current status:

| # | Invariant | Status |
|---|---|---|
| 1 | Phase can only advance forward (no regression without justification) | ❌ Not enforced — Excel edits can freely regress phases |
| 2 | Milestone target/date changes must have an audit record | ❌ Not enforced — no audit trail |
| 3 | Team data not visible outside authorization boundary | ❌ Not enforced — all-or-nothing visibility; accepted risk |
| 4 | Metrics must show data freshness indicator | ⚠️ Partial — single dashboard-level date; no per-metric freshness |
| 5 | Initiative cannot be complete if teams haven't reached required phase | ⚠️ Partial — completion % visible; no hard enforcement |
| 6 | Blocker must have an owner | ❌ N/A — blockers not tracked |
| 7 | Headcount sourced from authoritative HR system, not manual entry | ❌ Not met — team members manually entered in Excel |
| 8 | Historical data not edited without audit trail | ❌ Not met — no historical storage at all |

## 6. UX / Information Architecture

| Discovery Recommendation | Implementation Status |
|---|---|
| Progressive disclosure (summary → exception → detail → history) | ⚠️ Partial — summary and detail implemented; no exception layer or history view |
| Fewer things by default | ✅ Good — Overview tab aggregates; drill-down via tab/filter |
| Explicit comparison (actual vs. target) | ❌ Missing — pre/post GenAI metric improvement shown, but no adoption rate vs. target |
| Distinguish informational from actionable content | ⚠️ Partial — overdue badge is actionable; rest is informational |
| Contextual peer comparison | ❌ Missing — no benchmarking between teams or DRs |
| Data trust signals | ⚠️ Partial — "Not Filled" status exists; stale data not distinguished |
| Avoid mixing aggregation levels | ✅ Good — Overview is aggregate; initiative tabs show team-level rows separately |

## 7. What the Current Implementation Does Well

These are areas where the implementation is effective, pragmatic, or ahead of the discovery's expectations:

1. **Pre/Post GenAI metric comparison** — The discovery talks about "adoption metrics" generically; the implementation captures concrete Productivity, Quality, and Coverage improvements with before/after values, which is valuable evidence for ROI reporting.
2. **Customer approval gate** — An additional accountability step not in the discovery, reflecting real operational need.
3. **Overdue detection** — ETA-based flagging with visual highlighting (⚠️ Due badge, red rows) is a practical exception signal.
4. **Security baseline** — The README documents accepted risks (JS-06, CSS-03, CSS-04) transparently, with mitigation conditions clearly stated. This is good operational hygiene.
5. **Self-contained distribution** — A single HTML file requiring no server infrastructure is well-suited for the current deployment context (OfficeTime server).
6. **Global cross-initiative filters** — Filtering by Org or DR across all 6 initiatives simultaneously is a capability the discovery did not explicitly specify but adds real value for DR-level users.

## 8. Highest-Priority Gaps (Recommended for Next Phase)

Ranked by decision-making impact per the discovery document:

| Priority | Gap | Discovery Reference | Why It Matters |
|---|---|---|---|
| 1 | Historical snapshots / trend data | Perspectives 2, 4, 8; Section I | Without history, trend computation and stall detection are impossible. This is foundational for all future analytical capability. |
| 2 | Blocker tracking | Sections B, D, I; Invariant 6 | Program managers currently have no in-system way to record or track impediments. Remarks field is a proxy that cannot be queried or reported on. |
| 3 | Adoption targets per initiative | Perspectives 2, 4; Section I | Showing 84.2% completion is ambiguous without a target. Is that on track? The discovery says comparison vs. target is more meaningful than absolute. |
| 4 | Proactive alerting | Perspective 2; Section I | The dashboard is pull-only. Program managers must remember to visit it; stalls and risks are not pushed. |
| 5 | Configurable health thresholds | Perspective 4; Section I | `>=80%` = green is hardcoded. Different initiatives or milestones may have different risk tolerances; this should be data-driven. |
| 6 | Data freshness per team/metric | Perspective 6; Invariant 4 | Showing a single "Refreshed Aug 11" date is insufficient when individual teams may not have updated their data in weeks. |
| 7 | Phase-advance enforcement | Invariant 1 | Without enforcement, the data integrity of phase status depends entirely on Excel discipline. |

## 9. Out-of-Scope Items (Discovery Identified as Future Evolution)

These items from the discovery are deliberately not yet in scope and should remain deferred:

- Predictive milestone attainment (ML/forecasting)
- Comparative benchmarking across organizations
- Value realization linkage (adoption vs. velocity/quality)
- Automated nudging / outbound messaging
- Self-service initiative configuration UI
- ADO/Jira blocker integration
- API for external consumers
- Developer-level individual usage records

## 10. Summary Scorecard

| Dimension | Score | Notes |
|---|---|---|
| Initiative tracking | 8/10 | Core loop well implemented; milestone concept missing |
| Team-level visibility | 7/10 | Good detail; headcount and target gaps |
| Exception surfacing | 4/10 | Overdue only; no stall, regression, or blocker detection |
| Trend / history | 1/10 | Current state only; foundational gap |
| Blocker management | 0/10 | Not implemented |
| Access control | 1/10 | Accepted risk; documented but unmitigated |
| Data quality signaling | 4/10 | "Not Filled" visible; no per-team freshness |
| Alerting / proactivity | 1/10 | No push mechanism |
| Configurability | 3/10 | Metrics from Excel; phases/thresholds hardcoded |
| Audit trail | 0/10 | Not implemented |
| **Overall** | **~30/100** | Solid Phase 1 tracker; pre-specification vision ~30% realized |

> *This report reflects the state of the implementation as of August 2026. It is intended to inform prioritization of future development sprints, not to criticize the current build...* [text cut off in source screenshot]
