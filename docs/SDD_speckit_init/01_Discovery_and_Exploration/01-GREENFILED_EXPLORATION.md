# GenAI SDLC Rollout Monitor — Product Discovery

> **Status:** Pre-specification discovery exercise. **Purpose:** Understand the problem space from multiple perspectives before committing to any design, layout, or technology. **Scope:** Greenfield. Any existing application is treated as later reference evidence, not as the target design.

## Perspective 1 — Business / Product Analyst

### What business problem are we solving?

Organizations investing in GenAI tooling across their software development lifecycle face a fundamental visibility gap: they have committed to adoption targets (to leadership, steering committees, or vendors) but have no reliable, consolidated way to see whether those commitments are being met, where progress is stalling, or where investment is being under-utilized.

Without this visibility:

- Program managers are forced to chase status updates manually.
- Leadership receives anecdotal or lagging evidence of progress.
- Blocked or stalled teams go undetected until a milestone is missed.
- Resources (licenses, enablement effort, coaching) cannot be redirected to where they are needed most.
- The value case for GenAI investment cannot be substantiated.

The core problem is not a lack of data — it is a lack of *actionable situational awareness* across a complex, multi-team, multi-initiative rollout program.

### What decisions should users be able to make using this application?

| Decision | Who makes it |
|---|---|
| Where to direct enablement and coaching resources this week | Rollout Program Manager |
| Which teams require direct intervention | Delivery Manager, Program Lead |
| Whether an initiative milestone is at risk and needs escalation | Program Manager, Executive |
| Whether to adjust a rollout timeline or target | Program Lead, Executive |
| Whether to reallocate licenses from low-adoption to high-demand teams | Platform / Tooling Owner |
| How to communicate progress to executive leadership | Program Manager |
| Whether a team has fully adopted (and adoption is real, not nominal) | DR / Delivery Manager |
| Which blockers are systemic vs. isolated | Program Manager |

### What business outcomes should improve?

- Faster time-to-adoption across the organization.
- Higher realization of ROI on GenAI tooling investment.
- Earlier detection of blockers, before they become missed milestones.
- Accountability clarity: who owns what, and what is the current state.
- Reduction in manual status-collection overhead for program managers.
- Evidence base for executive and vendor reporting.

### What would make the product valuable rather than just a reporting screen?

A reporting screen shows what happened. A valuable product helps users *decide what to do next*. The difference lies in:

- **Proactive surfacing of exceptions**, not passive display of all data.
- **Contextual comparison**: actual vs. target, current vs. last period, this team vs. peer teams.
- **Trend signal**: is a team improving, stalling, or declining? A point-in-time number without trajectory is ambiguous.
- **Workflow support**: making it easy to record a blocker, log an intervention, or mark a milestone complete.
- **Accountability linkage**: surfacing who owns a gap, not just that the gap exists.
- **Data trustworthiness signals**: showing users when data is stale, incomplete, or missing — so they know when to trust what they see.

## Perspective 2 — End User / Manager

### What information would each actor actually need?

**Executive / Steering Committee**

- Are we on track to hit our organizational adoption commitments?
- What is the aggregate adoption rate today vs. target?
- What are the top blockers to overall program success?
- Which organizational units are significantly behind?

**Rollout Program Manager**

- Which teams are at risk and what is causing the risk?
- What has changed since my last review?
- Which milestones are approaching and what is their readiness?
- What is the status of open blockers?
- Where is my time best spent this week?

**Delivery Manager / Engineering Manager**

- What is the adoption status of my teams?
- What do I need to do to move my team forward?
- How do my teams compare to targets?
- What support or resources are available to me?

**Platform / Enablement Team**

- Which teams have not yet started or are in early stages?
- Where is engagement with enablement resources low?
- Are there clusters of teams with similar blockers that could benefit from a shared intervention?

**Team Lead (read-only, own team)**

- What is my team's current adoption status?
- What is expected of us and by when?

### What situations require action?

These are the scenarios the product should surface clearly:

- A team's adoption has been stagnant for N weeks.
- A milestone deadline is within N days and readiness is below threshold.
- A team was expected to advance phase but has not.
- A blocker has been open for more than N days without resolution.
- A team completed onboarding but usage data shows abandonment.
- A team's data has not been refreshed in more than N days (data confidence issue).
- A team was added to an initiative scope but has not yet started any onboarding activity.
- An initiative's aggregate adoption rate is declining.

### What information should be immediately visible?

Without any navigation or drill-down:

- Overall program health vs. committed targets.
- The count and severity of items requiring attention right now.
- What has changed (improved or worsened) since the last review.
- The distribution of teams by phase or status across the organization.

### What drill-downs would users need?

- Initiative → milestones → contributing teams → team detail.
- Team → current phase → adoption metrics → assigned blockers → history.
- Blocker → owning team → age → mitigation status → comments.
- Org unit → all its teams → aggregated health → drill to specific team.
- Manager → their teams → adoption gaps.
- Metric → trend over time → comparison vs. target.

## Perspective 3 — UX / Information Architecture

### Meaningfully different structural models

The following are genuinely distinct organizing principles, not visual variants of the same thing:

**a) Overview-first (status dashboard)** A top-level summary of the whole program, with drill-down into components. The primary mental model is: *Here is everything; go explore what interests you.* Risk: important exceptions are buried in aggregates; busy managers spend time navigating rather than acting.

**b) Exception-first / action-first (management inbox)** The primary surface is the list of things that need attention — risks, stalls, missed milestones, aging blockers. Everything healthy is invisible or collapsed. The primary mental model is: *Here is what you need to deal with today.* Risk: users lose context of the overall program health; may create anxiety if exceptions are always the entry point.

**c) Initiative-centric tracker** Navigation begins from the initiative or campaign, not the organization. Each initiative has its own progress view, team roster, milestones, and status. The primary mental model is: *What is the state of Initiative X?* Good when there are multiple distinct campaigns with different goals, tools, and timelines. Risk: cross-initiative visibility (e.g., teams appearing in multiple initiatives) is harder.

**d) Org-tree / team-first navigator** Navigation begins from the organizational hierarchy — business unit → division → delivery resource → team. The primary mental model is: *Show me my part of the org and its health.* Good for delivery managers responsible for specific organizational units. Risk: initiative and milestone tracking becomes secondary.

**e) Trajectory / trend-first intelligence** The primary view is temporal. The central question is not "what is the status?" but "which way are things moving, and how fast?" Focus on acceleration, deceleration, stall detection, and predicted milestone attainment. Good for program health assessment and early warning. Risk: requires sufficient historical data; not useful at program launch.

**f) Milestone / commitment-first** The primary organizing element is time. Milestones and commitment dates are the spine of the product; teams and initiatives attach to them. The central question is: *Are we going to meet our commitments?* Good for board-level reporting and executive accountability. Risk: does not help understand why a milestone is at risk.

### What would reduce cognitive load and improve decision quality?

- Show fewer things by default; surface more on request.
- Use progressive disclosure: summary → exception list → detail → history.
- Make comparisons explicit: actual vs. target is more meaningful than actual alone.
- Anchor users in context: when viewing a team, show where it sits relative to peers and expectations.
- Distinguish informational content (status) from actionable content (what to do).
- Signal when data cannot be trusted (freshness, completeness).
- Avoid mixing multiple levels of aggregation on a single screen without clear separation.

## Perspective 4 — Data / Domain

### Major business / domain concepts

| Concept | Description |
|---|---|
| Organization Unit | A business unit, division, or department in the org hierarchy |
| Delivery Resource (DR) | An organizational unit below a division, likely containing multiple teams |
| Team | The primary unit of adoption — a group of developers with a manager |
| Initiative | A rollout campaign with a defined scope, goals, target dates, and enrolled teams |
| Tool / Practice | A specific GenAI capability being adopted (e.g., AI coding assistant, AI test generation) |
| Rollout Phase | A named stage in a team's adoption journey (e.g., Not Started, Onboarding, Active, Scaled) |
| Milestone | A time-bound, measurable target within an initiative |
| Adoption Metric | A measured indicator of adoption (e.g., active users, usage frequency, feature utilization rate) |
| Target | The numeric goal for a metric at a defined point in time |
| Health / Status | A derived assessment of a team's or initiative's position relative to plan |
| Blocker | An identified impediment to adoption, with an owner and resolution state |
| Engagement Event | A recorded interaction (training attended, enablement session, pilot kick-off) |
| Data Source | The origin of a specific metric (tool telemetry, manual entry, survey) |

### Important relationships

- A Team belongs to a Delivery Resource, which belongs to an Organization Unit.
- An Initiative enrolls many Teams; a Team may participate in more than one Initiative.
- An Initiative contains many Milestones, each with a target date and metric threshold.
- A Team occupies a Rollout Phase within the context of an Initiative (phase may differ by initiative).
- A Team has one or more Adoption Metrics, each compared to a Target.
- A Blocker is associated with a Team and optionally a specific Initiative or Milestone.
- A Tool / Practice may have multiple Adoption Metrics measuring different dimensions of adoption depth.

### Historical / trend information that would be useful

- Adoption rate snapshots over time per team and initiative.
- Phase transition history (when did a team move between phases, and how long did it stay in each?).
- Milestone attainment history (hit/miss, and by how much).
- Blocker open/close history and time-to-resolution.
- Engagement event log per team.
- Target revisions (if targets change, the history of target values matters for fair trend analysis).

### What information is needed to calculate rollout health?

A meaningful health score likely involves:

- **Adoption rate**: actual metric value ÷ target value, at the current point in time.
- **Time compliance**: expected adoption at this point in the rollout timeline vs. actual.
- **Phase compliance**: is the team in the phase expected for this point in the timeline?
- **Trend direction**: is adoption improving, flat, or declining over recent periods?
- **Blocker presence**: does an open blocker exist, how old is it, and is it progressing?
- **Data completeness**: how confident are we that the metric accurately reflects reality?

Health is inherently multi-dimensional; collapsing it to a single score risks hiding important signals.

### What should be configurable rather than hardcoded?

- Phase names and the number of phases.
- Health threshold definitions (e.g., what adoption % = "At Risk" vs. "On Track").
- The definition of "active user" or any adoption metric.
- Target values per initiative and milestone.
- Staleness thresholds (how many days before a data point is considered outdated).
- Which teams are in scope for which initiatives.
- Reporting periods (weekly, monthly, quarterly).
- Alerting rules (what conditions trigger a flag).

## Perspective 5 — Security / Access

### What kinds of access boundaries might exist?

- **Global / Executive**: full visibility across all initiatives, organizations, and teams.
- **Initiative-scoped**: a rollout PM sees only the teams and data within their initiative.
- **Organizational-scoped**: a DR or senior manager sees only their organizational subtree.
- **Team-scoped**: a team lead sees only their own team's data.
- **Platform / CoE**: cross-org visibility for enablement teams without org hierarchy authority.
- **Read vs. Write**: distinguishing who can view data vs. who can update status, log blockers, or modify targets.
- **Audit**: some users may have access only to audit trails or reports, not live operational data.

### Could visibility vary by org, DR, manager, project, team, or role?

Yes, and this intersection is the complexity. A single person may be:

- A DR who sees all teams in their delivery resource.
- A member of a cross-functional rollout team who needs cross-DR visibility for one initiative.
- A manager whose team participates in multiple initiatives with different owners.

This suggests that access cannot be modeled purely as a tree — there may need to be initiative-level permission grants that cross organizational boundaries.

### What must be clarified before designing authorization?

- Are individual developer-level records stored, and if so, who can see them? (HR / privacy implications.)
- Can a manager in Org A ever see data for Org B?
- Does an initiative PM have authority over all teams in their scope, regardless of org?
- Is there an audit requirement — specifically, should data changes be traceable to a user?
- Is there a "super user" admin role for data correction, and who holds it?
- Should there be time-limited access grants (e.g., for external reviewers)?
- Are there data residency or regulatory constraints on who can see which team's data?

## Perspective 6 — Operations

### How might data enter the system?

There are at least three distinct input modes, and likely the real system will combine them:

1. **Automated telemetry ingestion**: data pulled directly from GenAI tool platforms (e.g., usage APIs from AI coding assistants). High frequency, objective, but may not cover all tools or teams.
2. **Manual status entry**: rollout owners or managers update status, phase, and blockers. Low frequency, richer context, but subjective and dependent on discipline.
3. **Scheduled imports**: HR/org data feeds (headcount, team structure, reporting lines) on a periodic basis.
4. **Survey / self-assessment**: periodic check-ins where teams report their own perception of readiness or adoption barriers.

### Who owns source data?

- **Tool telemetry**: owned by the platform/tooling team or vendor.
- **Org hierarchy / headcount**: owned by HR or an enterprise directory system.
- **Targets and milestones**: owned by the initiative/program manager.
- **Phase and blocker status**: owned by the team lead or delivery manager, with program manager oversight.
- **Engagement events**: may be owned by the enablement or training team.

Multiple owners means the product must handle conflicting or inconsistent data gracefully.

### How fresh must data be?

- **Executive reporting**: daily refresh is typically acceptable.
- **Operational use (program managers)**: same-day or prior-day data.
- **Alerting on stall or regression**: needs to detect change within a defined window (e.g., stall = no progress in 2 weeks), so detection cadence matters.
- **Trend analysis**: weekly snapshots are sufficient.
- **Org hierarchy**: refreshed on a schedule; changes are infrequent but impactful.

### How should incomplete or invalid data be handled?

- Teams not yet enrolled in data collection should appear with a clear "no data / pending" state rather than being omitted silently.
- Missing org hierarchy linkage should be surfaced as a data quality issue, not silently ignored.
- Metrics unavailable for a given tool should be acknowledged in the UI rather than treated as zero adoption.
- If a team undergoes reorg (split, merge, rename), historical data must be attributed correctly — this is a significant domain event, not a routine update.
- Stale data should be visually distinguished from current data.

### What operational visibility is needed?

- Data freshness indicators per team, per metric, per source.
- Coverage metric: what percentage of in-scope teams have current data?
- Ingestion pipeline status (if automated): last successful run, error counts.
- Data completeness score per initiative.
- Audit log of manual data entries and changes.

## Perspective 7 — Architecture Capabilities

> No technology choices. Capabilities only.

The solution may require:

- **Multi-dimensional data navigation**: users navigate across organization, initiative, time, and team dimensions interchangeably.
- **Derived metric computation**: health scores, trend calculations, phase compliance, and target attainment are computed from raw inputs, not stored as opinions.
- **Configurable rule definitions**: health thresholds, phase progressions, and alerting rules must be changeable without code changes.
- **Temporal data storage**: historical snapshots must be preserved so trends can be computed and past states can be audited.
- **Row / scope-level authorization**: access control is not simply role-based; it must enforce data visibility at the team, organization, and initiative level.
- **Data quality signaling**: the system must be able to represent the confidence level of any data it displays (freshness, completeness, source type).
- **Audit trail**: all data changes, especially manual ones, should be attributable to a user and timestamp.
- **Proactive alerting / notification**: the system must detect condition changes and surface them without waiting for a user to log in and navigate.
- **Integration boundary abstraction**: the system should be able to receive data from multiple sources (telemetry, HR, manual) without tightly coupling the data model to any specific source.
- **Extensibility**: new tools, practices, phases, or metrics must be addable without redesigning core concepts.
- **Self-describing data gaps**: the system must distinguish "no data yet" from "data confirmed as zero" from "data source unavailable."

## Perspective 8 — Future Evolution

### What is likely to change?

- The specific GenAI tools being tracked will change as the market and organization evolve.
- The definition of "adoption" will mature — initial phases may track activation; later phases may track depth of use, quality impact, or value realization.
- Targets will shift as baseline data improves or organizational ambitions change.
- Organizational structure will change (reorgs, acquisitions, team restructuring).
- New rollout initiatives will launch; old ones will close.
- Reporting audiences may expand beyond internal stakeholders (e.g., vendor SLAs, board reporting).

### What should not be hardcoded early?

- The names and count of rollout phases.
- What constitutes an "active user" or any adoption metric.
- The formula for health scoring.
- The depth of the org hierarchy.
- The specific set of tools or practices being tracked.
- Milestone definitions and target thresholds.
- The frequency of data collection.

### What future capabilities may reasonably appear?

- **Predictive attainment**: given current trajectory, will this team hit its target by the milestone date?
- **Comparative benchmarking**: how does adoption in this org compare to similar organizations or prior rollouts?
- **Value realization linkage**: correlating adoption level with development velocity or quality metrics.
- **Automated nudging**: proactively contacting team leads or managers when action is needed.
- **Self-service configuration**: allowing program managers to define new initiatives, milestones, and targets without admin involvement.
- **Integration with work tracking**: linking blockers to tickets in existing systems (Jira, ADO).
- **API for external consumers**: enabling downstream dashboards or reporting tools to consume rollout data.

### Where could today's assumptions cause future rework?

- Treating "GenAI SDLC adoption" as a single monolithic concept rather than a set of independently trackable practices will make it hard to disaggregate results later.
- Hardcoding the org hierarchy depth assumes the organization structure is stable — it is not.
- Building only a read-only view (no workflow) forces program managers to maintain parallel tracking systems (spreadsheets, email), making the product feel marginal.
- Storing only current state without history makes trend analysis retroactively impossible.
- Treating the current set of tools as exhaustive creates rework when new tools are adopted.
- Assuming a single, universal health threshold ignores that different initiatives may have different risk tolerances.

## Section A — Problem Statement

Organizations committing to GenAI SDLC adoption across multiple teams and initiatives have no reliable, consolidated way to monitor whether those commitments are being met. Program managers, delivery leaders, and executives lack the situational awareness to make timely resource-allocation and intervention decisions. The result is manual status-chasing, delayed detection of blockers, and an inability to substantiate the value of GenAI investment.

The goal is not a reporting screen — it is an operational capability that surfaces what needs attention, shows where adoption is heading, and enables the people responsible for the rollout to make better decisions faster.

## Section B — User / Actor Map

```
Executive / Leadership
(aggregate progress, risk, investment realization)
        |
        ├─────────────────────────────┐
        ▼                             ▼
Rollout Program Mgr           Delivery Manager / DR
(initiative health,           (org subtree health,
 milestone tracking,           team-level gaps,
 blocker management)           resource needs)
        |                             |
        ▼                             ▼
Enablement / CoE               Engineering Manager
(engagement gaps,               (team status, blockers,
 cluster interventions)          next steps for their team)
                                      |
                                      ▼
                              Team Lead (read-only)
                              (own team status,
                               expectations, milestones)

System / Data Admin
(data quality, source ingestion, access management)
```

## Section C — Business Outcomes

| Outcome | How the product contributes |
|---|---|
| Faster time-to-adoption | Stalls detected and addressed earlier |
| Higher ROI realization on GenAI investment | Underutilized licenses and teams identified and redirected |
| Reduced manual status-collection | Automated ingestion and consolidated view eliminates status meetings |
| Accountability clarity | Blocker ownership, phase assignment, and milestone tracking make responsibility visible |
| Executive confidence in program health | Trustworthy, current data replaces anecdote and estimation |
| Evidence base for vendor and board reporting | Historical snapshots provide auditable progress evidence |
| Earlier intervention on at-risk milestones | Proactive alerting gives leads time to act before deadlines |

## Section D — User Decision / Use-Case Map

**High-frequency decisions (operational, frequent)**

- **Triage**: Which teams or initiatives need my attention today?
- **Resource dispatch**: Where should I send enablement/coaching support this sprint?
- **Blocker action**: Is there a new blocker I need to assign, escalate, or close?
- **Phase advancement**: Has a team earned its next phase designation?
- **Data quality check**: Can I trust the numbers I'm seeing right now?

**Medium-frequency decisions (weekly / milestone-driven)**

- **Milestone readiness**: Which milestones are in the next 30 days and what is the risk level for each?
- **Progress communication**: What is the right summary to share in next week's steering meeting?
- **Trend assessment**: Is adoption accelerating, holding, or declining across the program?
- **Target adjustment**: Does a target need to be revised based on new information?

**Low-frequency decisions (strategic / periodic)**

- **Scope changes**: Which teams should be added or removed from an initiative?
- **Initiative design**: Are current milestones and targets still realistic?
- **Investment decisions**: Should we purchase more licenses, expand tooling, or redirect budget?
- **Retrospective**: What did we learn from this rollout that should inform the next one?

## Section E — Business Invariants That Appear Likely

These are constraints that likely hold regardless of design choices:

1. A team's adoption phase within an initiative can only advance forward (no regression without explicit justification).
2. A milestone's target date and metric threshold should not change without an audit record of who changed it and why.
3. A team's data should not be visible to someone outside its organizational authority boundary or initiative scope, unless explicitly granted.
4. A metric displayed without a corresponding data freshness indicator is potentially misleading.
5. An initiative cannot be marked complete if any of its enrolled teams have not reached the required phase.
6. A blocker must have an owner to be actionable — ownerless blockers are an incomplete record.
7. A team's headcount (denominator for adoption rate) should be sourced from the authoritative HR/directory system, not entered manually, to prevent gaming.
8. Historical data points, once recorded, should not be retroactively edited without an audit trail.

## Section F — Assumptions

The following are assumptions embedded in the problem statement that have not been confirmed:

1. There is a single, shared definition of "GenAI SDLC adoption" — it may actually differ by initiative, business unit, or tool.
2. The rollout is top-down and managed — it may in practice be partially organic, making "enrollment" ambiguous.
3. Teams are the right unit of adoption tracking — the actual unit may be individuals, squads, or cost centers.
4. A single application can serve all actors — executives, program managers, and team leads may need meaningfully different experiences.
5. Adoption can be measured numerically — some practices (e.g., prompt quality, AI-assisted code review) may resist clean quantification.
6. The org hierarchy is stable enough to be a navigation spine — reorgs happen and can invalidate historical team attribution.
7. The rollout has defined initiatives with scopes and milestones — this may not be true at program launch.
8. A "complete" rollout remains complete — tool abandonment after initial adoption is a real risk not yet surfaced.
9. All relevant teams are known and enumerable — shadow teams, contractors, or externally embedded teams may be excluded.
10. Data can be collected at a cadence that supports the product's needs — vendor API access, telemetry quality, and HR feed frequency are unconfirmed.

## Section G — Stakeholder Questions

These questions must be answered before a specification can be written:

**Program / Scope**

1. What is the complete list of GenAI tools and practices being tracked? Are they all treated equivalently, or do they have different adoption models?
2. How many concurrent initiatives will exist? Do they share teams and metrics, or are they fully independent?
3. Who officially defines when a team has "completed" a phase — the team, their manager, or the program manager?
4. What is the authoritative source for organizational hierarchy and team headcount?
5. Is there an official, agreed-upon definition of "active user" for each tool, or is this still open?

**Data**

6. Which GenAI tool vendors provide usage APIs, and what is the data contract (fields, frequency, access model)?
7. Is there existing usage telemetry being collected anywhere today (even informally)?
8. How are teams that opt out of telemetry handled?
9. What happens to historical data when a team is reorganized, merged, or renamed?
10. Who is responsible for data quality, and what is the remediation path when data is wrong?

**Access and Security**

11. Should individual developer-level records be visible to anyone other than that developer and their manager?
12. Can a program manager in one business unit view teams from another business unit? Under what conditions?
13. Is there an audit requirement for data changes? For access events?
14. Are there compliance or data residency constraints (e.g., region-specific data handling)?

**Workflow**

15. Is there an expectation of workflow support (logging blockers, recording interventions, updating status) within this product, or is it read-only?
16. Who owns blocker resolution — the team, the manager, or the program team?
17. Should the product send proactive notifications (email, Teams message) or is passive in-app visibility sufficient?

**Success**

18. How will we know this product is valuable? What behavior change in program managers would constitute success?
19. Are there existing manual processes (spreadsheets, status decks) that this should replace? What are their formats?
20. What is the expected reporting cadence for executive audiences?

## Section H — Potential Edge Cases

1. **Team in multiple initiatives simultaneously** with conflicting phase designations or targets — which takes precedence in aggregate views?
2. **Team headcount drops to zero** (team dissolved mid-rollout) — how is historical data preserved, and does adoption rate become undefined?
3. **A tool is deprecated or replaced mid-initiative** — how is continuity of adoption measurement handled?
4. **Adoption rate exceeds 100%** — possible if headcount denominator is lagged or if contractors are included in usage but not headcount.
5. **A blocker is marked resolved but adoption doesn't recover** — the product may need to distinguish blocker closure from actual recovery.
6. **Data is backdated** — if a source provides data for past periods, how are historical snapshots updated without invalidating trend analysis?
7. **An initiative's scope grows mid-flight** — newly added teams start at zero, which can artificially depress overall progress metrics.
8. **Self-reported data contradicts telemetry data** — the product must handle and surface conflicting signals rather than silently choosing one.
9. **A team reports a blocker and it's immediately resolved by coincidence** — short-lived blockers may create noise in alerting.
10. **No rollout owner is assigned to an initiative** — all accountability-linked features break without an owner.
11. **Targets are set retroactively** after actual data is already available — creates appearance of success that isn't real progress.
12. **A manager is responsible for teams across multiple DRs** — their visibility may not align cleanly with the org hierarchy.

## Section I — Candidate Capabilities

These are capabilities that should be considered during specification, independent of implementation approach:

| Capability | Rationale |
|---|---|
| Exception surfacing | Program managers cannot process all teams manually; the system must filter to what matters |
| Trend computation | Point-in-time status is insufficient for predicting milestone attainment or detecting stalls |
| Configurable health rules | Threshold definitions will change; they must not be hardcoded |
| Multi-scope data navigation | Users need to navigate by org, initiative, milestone, team, and time |
| Milestone tracking and readiness | Time-bound commitments are the primary accountability mechanism |
| Blocker lifecycle management | Blockers need to be recorded, owned, tracked, and resolved — not just listed |
| Data freshness signaling | Users must be able to trust what they see; stale data without labeling is harmful |
| Scope management | Which teams are enrolled in which initiatives must be configurable, not hardcoded |
| Temporal / historical record | Required for trend analysis, audit, and post-program retrospectives |
| Role-and-scope-based access control | Access must enforce org and initiative boundaries, not just role membership |
| Configurable metric definitions | What counts as "active" will differ by tool and will change over time |
| Initiative and phase configuration | New initiatives and phase models should not require engineering intervention to set up |

## Section J — Current Ideas That May Be Implementation or UX Assumptions Rather Than Real Requirements

The following are ideas that often appear in early thinking about products like this, but which should be treated as *implementation hypotheses to test*, not confirmed requirements:

| Idea | Concern / Challenge |
|---|---|
| A dashboard with summary cards | Dashboards are passive; the real need may be action-oriented, not information-display |
| Traffic-light / RAG status indicators | Collapsing a multi-dimensional situation to red/amber/green loses nuance and may trigger wrong actions |
| A single health score per team | Aggregating multiple signals into one number hides which dimension is the problem |
| Percentage adoption as the primary metric | Percentage is meaningless without denominator clarity; different metrics may better represent readiness |
| A progress bar per initiative | Progress bars imply linearity; adoption is not always linear |
| Sortable / filterable tables | Users may not know what to filter for; exception-first surfacing may be more effective |
| One product for all roles | Executive and team-lead needs may be different enough to warrant different entry points |
| Rollout phases as a fixed list | The right phases differ by tool, initiative, and organizational maturity |
| Weekly as the default reporting period | The right frequency depends on the pace of the rollout; one size does not fit all |
| Metrics pulled from a single tool | Multi-tool rollouts require multi-source data; assuming one telemetry source is a brittleness risk |

## Section K — Three Meaningfully Different Product Concepts

These three concepts differ in their organizing principle, primary user workflow, and mental model — not in technology or visual style.

### Concept 1 — Initiative-Centric Adoption Tracker

**Core organizing principle**: The initiative (rollout campaign) is the primary object. Everything else — teams, milestones, metrics, blockers — attaches to an initiative.

**Primary workflow**: A user selects an initiative, sees its health in context of its milestones and timeline, then navigates to individual teams within that initiative to understand contributing factors. A program manager who owns multiple initiatives can compare them. A team that participates in multiple initiatives appears in each with its respective adoption status for that initiative.

**Primary question answered**: *How is Initiative X progressing toward its committed milestones, and which teams within it are causing risk?*

**Strengths:**

- Maps naturally to how rollout programs are organized and funded.
- Makes milestone accountability clear.
- Scales well when there are multiple concurrent initiatives.
- Supports comparison between initiatives.

**Weaknesses:**

- Cross-initiative visibility of a single team requires navigation effort.
- Less useful for org leaders whose responsibility is people, not initiatives.
- Does not naturally surface macro patterns across the whole organization.

**Key open question**: Are initiatives truly the organizing unit of the business, or are they project-management artifacts that don't reflect how people think about adoption?

### Concept 2 — Exception / Action-First Rollout Manager

**Core organizing principle**: The primary surface is a live, prioritized list of situations requiring attention. Healthy items are not shown. The product is organized around what users need to *do*, not what they need to *know*.

**Primary workflow**: A user arrives at a prioritized list of active exceptions: stalled teams, approaching milestones below readiness threshold, aging blockers, missing data, abandoned adoptions. Each exception item links to the context needed to act on it. Resolving an exception (assigning a resource, logging an intervention, closing a blocker) is a first-class action, not an afterthought. Summary views of overall health exist but are not the starting point.

**Primary question answered**: *What needs my attention right now, and what do I do about it?*

> **Note:** This document (photographed from screen) ends here — the transcription covers everything captured in the source screenshots. Concept 2's Strengths/Weaknesses and Concept 3 were not visible in the captured images.
