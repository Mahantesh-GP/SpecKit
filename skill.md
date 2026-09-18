Create a constitution for this existing configuration-driven workflow orchestration project.

Principles:
- Preserve the existing architecture and established contracts.
- Prefer existing activity and business-rule extension mechanisms for new functionality.
- Keep external integrations inside activity implementations, not orchestration logic.
- Preserve Durable Functions determinism and existing reliability patterns.
- Do not change core orchestration/framework components unless the requirement explicitly requires it.
- Do not invent missing business or integration requirements; surface them for clarification.
- Reuse existing project patterns and add/update tests for changed behavior.