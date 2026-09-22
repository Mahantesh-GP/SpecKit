13. ADO → Spec Kit Integration - Reference Capability and Extension Model
The pilot also explored a lightweight integration between Azure DevOps and Spec Kit. The purpose is to reduce manual copying while preserving the existing project operating model. This is one workable reference solution, not a mandatory organization-wide process. Projects may adopt, extend or omit integration behaviors based on their own workflow and governance.
13.1 Validated Pilot Capability
The Todo pilot validated the following integration path:
Start from an existing ADO User Story rather than creating the business requirement inside Spec Kit.
Use the Azure CLI with the Azure DevOps extension and the developer’s existing organizational authentication to read the selected work item.
Use the custom /ado-import Agent Skill to bring the selected ADO User Story into the local repository as structured requirement context, preserving the ADO Work Item ID for traceability.
Continue with the normal Spec Kit engineering journey: Specify → Clarify → Plan → Tasks → Analyze → Implement, as appropriate for the selected story/capability.
Use the custom /ado-clarification-sync capability to surface material clarification decisions/questions to the corresponding ADO User Story Discussion where useful. ADO remains the authoritative business/backlog record.
Reference flow
Select relevant ADO User Story ID(s) → Import ADO context → Define / Clarify → Record material decisions/questions in the relevant ADO story → Establish reviewed specification baseline → Continue normal development, QA, Git and PR flow.
13.2 One Story, Multiple Stories and Feature Boundaries
The integration should not force a one-to-one
 mapping when the product behavior does not justify it. A developer or team may select one story, or selected related stories, as input to refinement. The team still decides the appropriate specification boundary using the mapping guidance in Section 8.
One cohesive User Story can normally map to one Spec Kit feature.
Multiple related User Stories may contribute to one coherent feature/specification when their behavior belongs together; each source ADO story must remain traceable.
A broad story that contains independently valuable capabilities should normally return to refinement rather than being mechanically split by the tool.
The planned enhancement to /ado-import is to accept selected IDs in one invocation (for example: /ado-import 123 456 789). This is an extension direction, not a requirement to download an entire sprint.


Tooling and Developer Experience
The pilot implementation uses Azure CLI + the Azure DevOps extension as the connectivity layer because MCP is not currently the selected path for this experiment. The custom Agent Skills sit around standard Spec Kit rather than modifying the core Spec Kit skills. The intended adoption model is: platform/EA maintains the reusable integration capability; a project configures its ADO context; each developer completes prerequisites/authentication once and then works primarily through the exposed commands.
Prerequisites are installed/validated once per developer environment; they should not be rechecked for every User Story request unless execution reports a missing dependency or authentication problem.
ADO organization/project configuration should be centralized at project level rather than duplicated or hard-coded independently in every skill.
Credentials/PATs must not be stored in repository configuration. Authentication remains developer/environment specific.
If distributed through the EA Marketplace as a Copilot plugin, installation should make the approved Agent Skills available to the developer; project teams should not need to recreate the skills manually.
13.5 Project-Selectable Extensions
The current pilot should not be presented as the only possible integration model. Depending on project feedback and governance, the same integration layer can be extended later. These are options, not baseline requirements:
Publish or link the reviewed specification and supporting artifacts from ADO.
Create selected ADO Tasks from an agreed implementation breakdown, if the project wants task-level synchronization.
Synchronize selected task/User Story status transitions where that adds value.
Support automatic closure rules, PR/commit linking or other traceability automation.
Read later BA/PO answers from ADO and reconcile them into the local specification for asynchronous clarification round trips.
13.6 Community Reference vs Pilot Direction
The community pragya247/spec-kit-azure-devops approach was reviewed as a reference. Its demonstrated direction is primarily to synchronize Spec Kit artifacts such as spec.md/tasks.md toward Azure DevOps. The Todo pilot requirement starts from an existing ADO User Story and brings that context into the local SDD workflow. Therefore the community approach is useful reference material, but it is not treated as a drop-in fit for the pilot requirement.
Positioning for project teams
This integration is an optional, extensible bridge between ADO and the SDD engineering workflow. It does not require teams to replace their current Scrum, refinement, work-item, Git, QA or pull-request practices. Start with the validated minimum, gather project-team feedback, and extend only where a concrete need is demonstrated

