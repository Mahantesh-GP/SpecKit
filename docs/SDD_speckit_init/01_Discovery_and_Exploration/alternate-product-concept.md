# Critical Product Review Panel — Three Concept Evaluation

## Concept 1 — Initiative-Centric Adoption Tracker

### 1. Primary User Value

Gives program managers a structured, accountable view of a rollout campaign: are enrolled teams hitting milestones, and who is causing risk within that initiative? Maps directly to how programs are funded and reported.

### 2. Management Decisions Supported Best

- Milestone readiness assessment before a review or board date.
- Scope management: which teams are in, what is each team's phase.
- Cross-team comparison within an initiative.
- Go/no-go decisions on phase gates.
- Initiative-level executive reporting.

### 3. Information Made Easy

- Initiative-level progress vs. committed targets.
- Which teams within the initiative are behind and by how much.
- Milestone timelines and impending deadlines.
- Team-to-team comparison when all teams share the same initiative context.
- Responsibility: who owns each team's outcome within this initiative.

### 4. Information Made Harder

- A team participating in three initiatives simultaneously — understanding that team's overall burden requires cross-initiative navigation.
- Org-unit aggregate health: "How is the Payments division doing?" has no natural home.
- Systemic, cross-initiative blockers that affect multiple programs but appear as isolated items in each.
- Long-term org capability build — once an initiative closes, the organizational learning signal disappears.
- Whether adoption from a completed initiative is being sustained.

### 5. Risks and Assumptions

- **Assumes initiatives are well-defined at program start.** Many rollouts begin informally; forcing an initiative model on an immature program creates overhead before any insight.
- **Assumes milestones are agreed and measurable.** If targets are aspirational or contested, initiative health scores become political artifacts rather than useful signals.
- **Assumes a team's participation in an initiative is clean.** Teams partially in scope, contractor-heavy teams, or teams mid-reorg challenge clean enrollment.
- **Risk of initiative proliferation**: if program managers create many small initiatives to maintain visibility, the overhead of managing the initiative model itself grows.
- **Initiative completion ≠ sustained adoption**: the model has no natural mechanism to track post-initiative behavior.

### 6. Complexity Introduced

- Many-to-many relationship between teams and initiatives requires careful design to avoid duplicate or conflicting data views.
- Phase status must be scoped to an initiative, not just a team — same team can be "Active" in Initiative A and "Onboarding" in Initiative B.
- Milestone attainment logic must distinguish which metric applies to which initiative.
- Access control must encode initiative scope as a permission boundary, not just org hierarchy.
- Closing or archiving an initiative without losing historical data requires deliberate data lifecycle design.

### 7. Dependence on Historical/Trend Data

**Medium.** Works acceptably from day one — an initiative with no historical data still exposes team enrollment, phase status, and milestone countdown. Trend signals (is adoption accelerating toward milestone date?) require 3–4 weeks of snapshots to become meaningful. Without history, the model degrades gracefully to a tracker, not a health monitor.

### 8. Dependence on Access-Control Sophistication

**High.** Initiative scope acts as a second permission axis alongside org hierarchy. A program manager for Initiative A should not see teams in Initiative B if they are not enrolled. A delivery manager must see all their teams' data regardless of which initiatives those teams are in. These two access models are additive, not interchangeable, and their intersection must be resolved for every data view.

### 9. Support for Future Requirement Change

**Medium.** Adding new initiatives is natural. Adding new metric types or phase definitions per initiative is natural if configurability is built in. Less natural: surfacing cross-initiative patterns, introducing trajectory-first views, or pivoting to action-first modes. The initiative-centric model will resist reorganization around org units or exceptions without significant rework of the navigation model.

### 10. Does It Solve the Actual Problem or Create Attractive Reporting?

**Partially.** It solves the program-manager-to-milestone accountability problem well. It is genuinely useful for "are we on track for this initiative's milestone?" However, it risks becoming a status tracker — a better-looking version of the program status spreadsheet — unless exception surfacing and action support are explicitly added on top. It does not, by itself, help a manager decide *what to do next*, only *how far behind a team is*.

---

## Concept 2 — Exception / Action-First Rollout Manager

### 1. Primary User Value

Eliminates the need to scan all data to find what matters. A busy program manager arrives and immediately sees a ranked list of situations that require a decision or intervention. Time-to-action is minimized.

### 2. Management Decisions Supported Best

- **Triage**: what needs my attention today vs. this week.
- **Intervention dispatch**: which team gets a coaching call or resource.
- **Blocker escalation**: which blockers are aging without resolution.
- **Data quality investigation**: which teams have stale or missing data.
- **Phase advancement**: which teams have met criteria and need formal promotion.

### 3. Information Made Easy

- The list of things currently wrong, ranked by urgency.
- Whether a specific exception has worsened since last reviewed.
- Blocker ownership and age.
- The delta since last check-in — what changed.
- Whether an intervention improved the situation.

### 4. Information Made Harder

- Overall program health — there is no holistic "how are we doing?" view without building a separate summary layer.
- Whether the organization is generally improving — absence of exceptions does not mean good health, just no threshold breaches.
- Longitudinal view: an exception that has been open for four months looks the same as one opened yesterday unless age is a prominent signal.
- Understanding patterns — if ten teams are stalling for the same systemic reason, the exception list shows ten items; the pattern is implicit, not surfaced.
- Executive communication — a list of problems with no healthy reference point is a difficult basis for steering-committee reporting.

### 5. Risks and Assumptions

- **Alert calibration is the product's core competency.** If alerting rules are wrong — too sensitive, too permissive, wrong thresholds — the exception list becomes noise or gives false confidence. This is not a UX risk; it is a correctness risk.
- **Assumes program managers have the authority and means to act on exceptions.** If action requires navigating multiple other systems (Jira, email, HR), the product creates awareness but not resolution.
- **Assumes exceptions are discrete and classifiable.** Many real rollout problems are ambiguous ("team seems disengaged but metrics haven't crossed a threshold yet") and won't surface until they become crises.
- **Risk of exception fatigue.** If the list is consistently long, users will develop selection habits that miss important items — the same problem as email inboxes.
- **The "no exceptions" state is ambiguous.** An empty exception list could mean the program is healthy, or it could mean thresholds are miscalibrated, data is stale, or teams have been enrolled without telemetry.

### 6. Complexity Introduced

- Exception definition management: rules must be versioned, tested, and auditable. When a threshold changes, retroactive re-evaluation must be considered.
- Exception deduplication: a single team could generate three overlapping alerts (stall, aging blocker, missing data). Presenting these intelligently without overwhelming the user is non-trivial.
- Snooze/dismiss/acknowledge lifecycle: exceptions that are known and being handled must be distinguishable from new ones.
- False-positive management: a user who repeatedly dismisses a class of exception will route around the product.
- Summary views must be derived from the exception model, which is an inversion of the typical analytics pattern.

### 7. Dependence on Historical/Trend Data

**Very high.** Almost all exception types are trend-based: stall = N weeks without improvement; regression = worse than last period; at-risk milestone = current trajectory projects miss. At program launch with no history, the exception model has almost nothing to surface. This concept requires the most lead time before it delivers value.

### 8. Dependence on Access-Control Sophistication

**Medium-high.** Each exception must be visible only to the users whose scope covers the affected entity. Access control errors here are especially damaging: an exception visible to the wrong person creates both a security violation and an operational confusion. The exception model should not surface what a user cannot also act on.

### 9. Support for Future Requirement Change

**High, with a caveat.** Adding new exception types is natural if the alerting rules engine is configurable. Adding new actions (new workflow steps, new intervention types) is composable. The caveat: the underlying data model must still be sufficiently rich to support new exception types. If the data model is thin, new exception ideas will fail for lack of signal. Future evolution of the exception model is only as good as the data foundation beneath it.

### 10. Does It Solve the Actual Problem or Create Attractive Reporting?

**Closest to solving the stated problem.** The discovery document is explicit: the goal is not a reporting screen, it is operational capability. This concept directly addresses that. However, it creates its own risk: it tells users what to look at, but does not ensure the underlying situation is actually understood or the right action taken. It can create the *impression* of control ("I cleared my exception list") without actual program health improvement. The quality of intervention, not the act of triage, determines outcomes.

---

## Concept 3 — Organizational Health Intelligence

### 1. Primary User Value

Gives delivery managers and executives a coherent picture of where adoption is going across the org — which units are accelerating, which are plateauing, and whether patterns across teams suggest systemic causes. The primary value is pattern recognition and early signal, not item management.

### 2. Management Decisions Supported Best

- **Resource allocation at scale**: not "which team needs help?" but "which cluster of teams in this org unit is underperforming and needs a structural intervention?"
- **Executive and steering committee reporting**: trajectory and comparative health are the language of board-level conversations.
- **Post-program retrospective and program design**: understanding what worked at the org level informs future rollout design.
- **Delivery manager accountability**: "Here is how your org unit compares to peers" creates accountability without confrontation.
- **Investment prioritization**: where are licenses underutilized at scale?

### 3. Information Made Easy

- Comparative health between org units at the same level.
- Trajectory: whether a unit is improving, stalling, or declining over a defined period.
- Which parts of the org are lagging most significantly.
- Time-in-phase distribution: are teams stuck in the same phase across a business unit?
- Macro program health for executive reporting.

### 4. Information Made Harder

- Specific, item-level action: "which team do I call today?" requires navigating from pattern to specific team — the concept does not naturally guide this.
- Milestone-specific accountability: is Team X going to hit the Q3 milestone?
- Blocker management: individual blockers are not naturally surfaced in an org-health view.
- Small teams: org-level aggregates can obscure a single team's severe problem if the unit is otherwise healthy.
- Cross-initiative analysis: if teams in the same org unit are on different initiatives, aggregating their health is methodologically complex.

### 5. Risks and Assumptions

- **Assumes the org hierarchy is a stable, meaningful navigation spine.** Reorgs invalidate historical comparisons. A team that moves from Division A to Division B mid-program contaminates trend lines for both.
- **Assumes sufficient historical data exists for trajectory to be meaningful.** This is the most data-hungry concept; it is weakest at program launch and may not deliver value for the first 6–8 weeks.
- **Assumes users think in org-tree terms.** In practice, many program managers think in initiative terms or relationship terms ("I know the teams in that DR"), not in formal hierarchy terms.
- **Risk of gameable metrics.** Comparative views between org units create competitive pressure, which can motivate manipulation of self-reported inputs to look better against peers.
- **Pattern identification is not the same as causal understanding.** Showing that a cluster of teams is stalling is not the same as explaining why.

### 6. Complexity Introduced

- Aggregate metric computation across org levels must be consistent and explainable. Users will ask "how is this unit score calculated?" and must get a credible answer.
- Org hierarchy versioning: when teams move, split, or merge, historical aggregates must be restated or caveatted.
- Multi-dimension health signal design: avoiding the single-score trap while still making comparisons legible is a significant UX design challenge.
- Trend computation needs decisions about smoothing, outlier handling, and minimum-data-point thresholds.
- Drill-down from org aggregate to individual team must be seamless and not produce inconsistencies (team metric ≠ unit aggregate).

### 7. Dependence on Historical/Trend Data

**Extremely high.** This concept is fundamentally a time-series product. Without 4–8 weeks of snapshots, there is no trajectory, no comparison over time, and no early-warning capability. At launch, this concept degrades to a static org-tree with current status indicators — significantly less valuable than its design intent. It requires the most patience before delivering its core value proposition.

### 8. Dependence on Access-Control Sophistication

**Medium.** The primary access model is org-tree-based: a user sees the subtree they are responsible for. This is a relatively natural implementation. Complexity arises when users need cross-org visibility for a specific initiative (a program manager without org authority) and when individual team data within an org unit must be selectively restricted.

### 9. Support for Future Requirement Change

**High for strategic capability; low for operational capability.** Adding new metrics, new org levels, and new time dimensions is natural. Adding predictive attainment is a natural extension of the trajectory model. However, adding initiative-centric views, blocker management, or action-first workflows would require significant product extension — the org-health mental model and the workflow mental model are in tension.

### 10. Does It Solve the Actual Problem or Create Attractive Reporting?

**Highest risk of creating attractive reporting.** The discovery document identifies "attractive reporting" as the failure mode to avoid. Trend charts, org-health heatmaps, and comparative benchmarking look compelling and may answer questions that executives ask in presentations rather than questions that program managers act on daily. Without explicit exception surfacing and workflow support, this concept produces insight without systematically converting it to action.

---

## Cross-Concept Analysis

### Capabilities Common to All Three Concepts

These must be built regardless of which concept is chosen or how they are combined:

| Capability | Why Universal |
|---|---|
| Team enrollment and phase tracking | Every concept surfaces team status at some level |
| Adoption metric storage and display | Every concept displays metrics; differs only in how they are framed |
| Milestone definition and countdown | Time-bound commitments appear in all three |
| Blocker record (at minimum read) | All three require understanding why things are wrong |
| Data freshness signaling | All three risk misleading users if data staleness is not visible |
| Role-and-scope-based data visibility | Required by all three; the model differs, not the requirement |
| Historical snapshot storage | All three degrade without it; the degree varies |
| Configurable phase names and thresholds | All three expose phase status; definition must not be hardcoded |
| Org hierarchy as context | All three reference org structure even if they navigate it differently |
| Audit trail for manual changes | Required regardless of primary mental model |

### Capabilities Unique to Each Concept

**Concept 1 — Initiative-Centric only:**

- Initiative-as-primary-navigation-object (initiative roster, initiative detail view)
- Initiative-scoped milestone attainment tracking
- Team enrollment management per initiative (adding/removing teams from scope)
- Cross-initiative comparison for a single program manager
- Initiative lifecycle (draft → active → closed → archived)

**Concept 2 — Exception-First only:**

- Configurable alerting rule engine (conditions, thresholds, rule types)
- Exception lifecycle (new → acknowledged → investigating → resolved → dismissed)
- Exception prioritization and ranking logic
- Snooze / suppress with justification
- "Last reviewed" and delta-from-last-review surfacing
- Intervention logging linked to exceptions
- Exception deduplication and grouping logic

**Concept 3 — Org Health Intelligence only:**

- Org-tree navigation as primary entry point
- Peer comparison across sibling org units
- Multi-dimension trajectory visualization (per org unit, per level)
- Cluster pattern identification (groups of teams with similar stall characteristics)
- Time-in-phase distribution across an org unit
- Org hierarchy version management (handling reorgs without corrupting history)

### Requirements That Must Be Clarified Before Choosing a Concept

**About the primary user workflow:**

1. Do program managers currently operate by scanning all data and filtering, or by responding to escalations and alerts? Their current workflow will determine which concept requires the least behavior change.
2. When a program manager starts their day, what is the first question they actually ask? (Not what they *should* ask.)
3. Is the organizational hierarchy stable enough to be the primary navigation spine for the next 18 months?

**About the data foundation:**

4. How many weeks or months of data are already available, or will be available at launch? This determines whether Concept 3 delivers value at go-live.
5. How reliable and timely is the telemetry data from GenAI tools? Concept 2 fails catastrophically if the data it raises exceptions on is unreliable.
6. Will org hierarchy data be available programmatically (directory feed), or will it require manual maintenance?

**About organizational behavior:**

7. How many concurrent initiatives will be active at any given time? If the answer is one or two, initiative-centric navigation is low-value; if the answer is eight or ten, it is essential.
8. Is there genuine organizational accountability pressure tied to adoption targets, or are targets aspirational? Comparative features (Concept 3) are only useful if comparison creates accountability.
9. Do program managers have the authority to act on the exceptions the product surfaces, or do they need to escalate? This determines whether action-support features add value or create frustration.

**About the user population:**

10. Are the heaviest users program managers, delivery managers, or executives? Each concept serves a different primary user.
11. What is the expected read/write split? Is this primarily a monitoring product with occasional manual updates, or a workflow product with frequent status changes?

**About the operational model:**

12. What happens when alerting thresholds need to change — who owns that, and how often does it happen? This determines the design requirements for the configuration layer in Concept 2.
13. Is there an expectation of proactive notification (push alerts) or is the product passive (users come to it)?

### Things That Should Remain Configurable

These must not be designed as fixed values in any concept:

| Item | Reason |
|---|---|
| Phase names and count | Different initiatives, tools, and organizational maturity levels require different phase models |
| Health threshold definitions | What is "at risk" in a fast-moving rollout differs from a slow, compliance-driven one |
| Adoption metric definitions | "Active user" varies by tool, initiative, and regulatory context |
| Alerting rule thresholds | Stall = 2 weeks in one context, 4 weeks in another; these will need tuning post-launch |
| Staleness threshold per data source | Telemetry may refresh daily; manual updates may be acceptable at weekly cadence |
| Milestone targets and dates | These legitimately change; the change itself needs audit, but the values must be editable |
| Team enrollment per initiative | Scope changes are a routine program management event |
| Reporting period (weekly, monthly, quarterly) | Depends on rollout pace and leadership cadence |
| Health aggregation method per initiative | Some initiatives may weight metrics differently |
| Org hierarchy depth displayed by default | Deep hierarchies create visual noise; truncation level should be user-settable |

### Things That Would Be Dangerous to Hardcode

| Hardcoded Item | Consequence |
|---|---|
| Phase count fixed at N | First initiative redesign requires engineering work; adoption programs routinely evolve their phase models |
| "Active user" defined as a specific number of interactions | Definition changes (vendor API changes, practice maturity, regulatory interpretation) make historical data incompatible with current data |
| Health = single percentage score | Forces compression of multi-dimensional reality into one number; creates arguments about the formula instead of about the program |
| Org hierarchy max depth | Acquisitions and org restructures will exceed or truncate any assumed depth |
| A fixed set of tracked tools | The GenAI tooling market is moving; new tools will be adopted; old ones will be replaced |
| Health thresholds baked into code | Tuning requires a deployment; thresholds will need tuning post-launch |
| Stall detection window (N weeks) | Different initiatives have different cadences; one stall definition fits none |
| Alert rule logic embedded in queries | Alerting rules must be testable and changeable without code changes; they are configuration, not logic |
| The list of exception types | New failure modes will be discovered post-launch; the exception model must be extensible |
| The rollout target (percentage, headcount) | Targets will change; historical analysis against old targets must remain auditable |
| Access control rules as code | Business rules about who sees what will change when org structure, initiative ownership, or staffing changes |
| The assumption that team = unit of adoption | If the business later decides to track by squad, individual, or cost center, a team-hardcoded model requires architectural change |

### Synthesis Observation

None of the three concepts is complete as stated. Each addresses a distinct, real need:

- **Concept 1** serves milestone accountability and initiative governance.
- **Concept 2** serves operational triage and action dispatch.
- **Concept 3** serves strategic pattern recognition and executive communication.

The failure mode of choosing only one concept is building a product that is excellent for one actor (program manager, or executive, or delivery manager) and marginal for the others. The discovery document identifies a user set with genuinely different information needs; a product architecture that shares the core data model and configurable rules but offers role-differentiated entry points is a meaningful design hypothesis to test before specification begins.

Before a specification can be written, the single most important clarification needed is: **who is the primary user the first release must serve, and what is the one decision that user makes most frequently that currently requires the most wasted time?** That answer determines which concept's organizing principle becomes the spine, and which capabilities from the other two are layered on top.
