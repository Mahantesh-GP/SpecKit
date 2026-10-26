# Repomix + Spec Kit: Step-by-Step Todo Experiment

**Purpose:** Test whether a focused Repomix snapshot helps GitHub Copilot understand an existing Todo repository before starting a brownfield SDD feature.

**Important:** Repomix packages source files as context. It does **not** analyze or modify your code by itself, and it is **not** a Spec Kit command. Follow your organization's security and third-party tooling rules before running it on company code.

## 1. Open the Todo repository

1. Launch **VS Code** and open the **root folder** of your Todo application.
2. Open **Terminal → New Terminal**.
3. Confirm your current directory and inspect its contents:

```powershell
Get-Location
Get-ChildItem
```

You should see your application folders and files. The exact folder names vary; do not assume `backend` and `frontend` exist.

## 2. Check prerequisites

```powershell
node --version
npm --version
```

If either command fails, check with your IT/team before installing Node.js. `npx` may download Repomix from the npm registry; obtain approval if required.

## 3. Protect secrets and generated outputs

Before packaging the repository, review `.env` files, connection strings, credentials, certificates, proprietary documents, and large generated directories. **Do not assume ignore rules will catch every sensitive file.**

To keep local generated context out of Git without changing the team's shared `.gitignore`, add patterns to `.git/info/exclude` (create the file if needed):

```gitignore
repomix-output.md
todo-context.md
```

Use this only if your repository uses Git. You can alternatively place output outside the repository.

## 4. Verify Repomix

```powershell
npx repomix@latest --version
```

If the command is blocked by corporate network/security controls, stop and request an approved method. Do not bypass company controls.

## 5. First run: repository inventory (recommended)

Generate a Markdown snapshot with conservative exclusions:

```powershell
npx repomix@latest . --style markdown --output repomix-output.md --ignore "**/bin/**,**/obj/**,**/node_modules/**,**/dist/**,**/build/**,**/.env*,**/*.pfx,**/*.pem,**/appsettings.*.json" --token-count-tree
```

**Check the output:** Open `repomix-output.md`, inspect which paths are included, and search for sensitive values before attaching it to any AI assistant. If the repository is large, prefer the focused run below rather than supplying this full snapshot.

> Note: Repomix CLI options can change by version. Run `npx repomix@latest --help` if your installed version rejects an option. The ignore list above is a precaution, **not** a guarantee that all secrets are excluded.

## 6. Better run: focus on the Todo feature

Find your actual project folders first:

```powershell
Get-ChildItem -Directory
```

Then replace the example folder names in the following command. **Do not run this example unchanged unless these folders exist:**

```powershell
npx repomix@latest . --include "backend/**/*.cs,backend/**/*.csproj,frontend/src/**/*" --ignore "**/bin/**,**/obj/**,**/node_modules/**,**/.env*,**/*.pfx,**/*.pem" --style markdown --output todo-context.md --token-count-tree
```

For a **Flask/Jinja Todo** repository instead, adapt the include paths to your actual Python application and template folders, for example:

```powershell
npx repomix@latest . --include "app/**/*.py,app/templates/**/*.html,tests/**/*.py" --ignore "**/.venv/**,**/__pycache__/**,**/.env*" --style markdown --output todo-context.md --token-count-tree
```

If the include pattern produces no useful files, inspect the repository layout and correct the paths. Never assume a snapshot covers dependencies that were not included.

## 7. Review the generated context

Open `todo-context.md` and check:

- Are the actual Todo API/controller/route, business logic, model and test files included?
- Are important shared dependencies missing?
- Are credentials, tokens, personal information, generated artifacts or unrelated modules present? **If yes, do not share the file.**
- How many files and tokens did Repomix report?

Record the counts and any exclusions. Keep the output local or within approved company tools.

## 8. Ask Copilot to analyze the snapshot (read-only)

If your organization's GitHub Copilot setup permits it, attach or reference `todo-context.md` in VS Code Copilot Chat and paste:

```text
We are evaluating Repomix as a context-preparation technique for
brownfield Spec-Driven Development in an existing Todo application.

Use the attached todo-context.md as a snapshot, not as proof that all
repository files or dependencies are included.

READ-ONLY TASK:
1. Describe the Todo application's current architecture.
2. Trace a Todo request from its entry point through the relevant code.
3. Identify models, persistence, external integrations and relevant tests.
4. List dependencies and likely regression risks for a small Todo change.
5. Give exact file paths/symbols supporting each finding.
6. Separate VERIFIED facts, ASSUMPTIONS and OPEN QUESTIONS.
7. Identify any missing files that need direct workspace inspection.

Do not edit files, run Spec Kit commands, or implement a feature.
Do not claim tests passed unless you have actual execution results.
```

**Expected result:** A short architecture/discovery report. Validate its claims by opening the referenced source files.

## 9. Compare with normal Copilot discovery (without Repomix)

Start a **new chat** without attaching the snapshot. Give Copilot the same discovery questions, allowing it to search the workspace normally. Compare both approaches:

| Metric | Normal Copilot workspace discovery | Repomix snapshot |
|---|---|---|
| Time to prepare and obtain a useful report | Record | Record |
| Correctly identified entry points | Record | Record |
| Missing or incorrect dependencies | Record | Record |
| Relevant tests identified | Record | Record |
| Snapshot size / reported token count | N/A | Record |
| Actual assistant token/credit usage (if available) | Record | Record |
| Security and context maintenance overhead | Record | Record |

**Do not conclude Repomix saves tokens or time without measuring.** A large snapshot can cost more context than focused workspace search.

## 10. Move to Spec Kit only after reviewing discovery

Once the feature's scope, architecture and relevant tests are understood:

1. Confirm the **ADO story and acceptance criteria** with the responsible BA/PO.
2. Confirm the repository's current constitution and any architecture constraints.
3. Use `/speckit.specify` to draft the feature specification.
4. Use `/speckit.clarify` to resolve ambiguities.
5. Use `/speckit.plan`, `/speckit.tasks`, and `/speckit.analyze` as appropriate to your team's installed Spec Kit workflow.
6. Obtain required reviews before `/speckit.implement`.
7. Run real builds, tests and normal PR reviews.

**Do not paste the entire Repomix snapshot into every Spec Kit prompt.** Reference only relevant files or a concise discovery summary; recheck any stale details.

## 11. Suggested first experiment

Choose a **small, bounded Todo change** (for example, a validation message or an approved pagination improvement). First run the read-only comparison above. You do **not** need to implement the change to assess Repomix's discovery value.

**Evidence to share with Bijesh:** Repository type, selected story, paths included, snapshot token count, what Copilot identified correctly, missing dependencies, time/usage comparison, and whether the technique is worth recommending to other brownfield teams.

## 12. Cleanup

If the output contains sensitive content, delete the generated file using your organization's handling procedures. Otherwise retain only the approved, sanitized experiment notes. Do not commit snapshots accidentally.

---

**References:** [Repomix documentation](https://repomix.com/guide/usage) · [Repomix GitHub](https://github.com/yamadashy/repomix) · [GitHub Spec Kit](https://github.com/github/spec-kit)

**Status:** Practical experiment guide; not an approved enterprise standard. Confirm CLI options against the installed version and organizational controls.
