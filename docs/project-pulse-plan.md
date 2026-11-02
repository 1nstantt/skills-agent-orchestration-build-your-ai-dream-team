# Project Pulse implementation plan

## Goal

Build Mona's lightweight static Project Pulse dashboard so contributors can scan active projects, owners, status, recent activity, priority or risk, and a short summary. Use the existing brief in `.github/project-pulse-brief.md` as the product source of truth and the custom agents under `.github/agents/` through GitHub Copilot CLI in the Codespace.

## Responsibilities and file assignments

| Agent | Responsibilities | File assignments |
|---|---|---|
| **Orchestrator** | Coordinate phases, delegate with explicit file scopes, resolve interface questions, avoid conflicting edits, and validate the integrated app before reporting the result. | No implementation files; coordinate and review all four deliverables. |
| **Planner** | Confirm requirements and edge cases from the brief, preserve these dependencies and file boundaries, and flag ambiguity before implementation. | `docs/project-pulse-plan.md` |
| **Designer** | Define the information hierarchy, accessible and responsive card layout, typography, spacing, status and priority treatments, and visual details. Agree on the markup hooks with Coder. | `app/styles.css` |
| **Coder** | Build semantic markup and the data-driven card rendering, provide deterministic project data, and configure the VS Code preview. Keep the page and data schema aligned with Designer's agreed markup hooks. | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` |

## Ordered implementation phases

1. **Confirm requirements and interfaces.** Orchestrator and Planner review the brief. Use a top-level `projects` array with `name`, `owner`, `status`, `recentActivity`, and `priority` for each project. Include a short `summary` as well, because the brief asks for contributor-friendly summaries. Agree that the page will use `.dashboard` and `.project-card` hooks before integrating markup and styles.
2. **Work in parallel on independent files.**
   - Designer creates the responsive, polished card styling in `app/styles.css`, including `.dashboard`, `.project-card`, rounded corners, and shadows.
   - Coder creates representative, deterministic entries in `app/project-data.json` and the strict-JSON `.vscode/launch.json` preview configuration.
3. **Implement and integrate the page.** Once Designer has shared the layout and class contract, Coder creates `app/index.html` with the exact title “Project Pulse,” semantic structure, a reference to `styles.css`, and rendering of visible project cards from `project-data.json`. Display each project's name, owner, status, recent activity, priority, and summary. Include clear loading and fetch-error feedback.
4. **Review and validate together.** Orchestrator checks that data, markup, styles, and launch configuration work as one dashboard; address any mismatch within the owning agent's files.

## Dependencies and parallel work decisions

- The brief is the source for the required content and runtime behavior; review it before implementation.
- The project data schema and the `.dashboard` / `.project-card` class contract are interfaces shared by the files. The schema and required class names come from this plan and the brief; Designer should communicate any additional structure needed by the stylesheet before Coder completes the page.
- `app/index.html` depends on the JSON field names and Designer's markup/class contract. Complete it after those interfaces are confirmed.
- `app/styles.css` depends on the agreed page structure and class hooks. Designer can create the stylesheet in parallel with Coder's work on the independent JSON and launch files.
- `.vscode/launch.json` serves the `app/` directory, so it depends on the app directory being created, but not on the final visual design. Configure it to run `python3 -m http.server 5500` from `${workspaceFolder}/app` and use `serverReadyAction` to open `http://localhost:%s/index.html`, not the directory root.
- Do not parallelize edits to the same file. Run integration and browser verification only after all four files are present.

## Validation expectations

- Confirm `app/index.html` has the exact “Project Pulse” title, references `styles.css` and `project-data.json`, and renders cards with `.project-card`, showing status, recent activity, and priority.
- Confirm `app/styles.css` defines `.dashboard` and `.project-card`, includes `border-radius` and `box-shadow`, and provides readable, responsive layout and accessible contrast.
- Parse `app/project-data.json` with `python3 -m json.tool`; verify it has a top-level `projects` array and each entry has the required fields, plus the contributor-friendly summary.
- Parse `.vscode/launch.json` with `python3 -m json.tool`; verify the configuration is named **Run Project Pulse Dashboard**, serves from `app/`, and opens `index.html` at the server-ready URL.
- Run **Run Project Pulse Dashboard** from VS Code Run and Debug. Confirm the browser shows the dashboard rather than a directory listing, project cards populate from JSON, and the layout remains usable at narrow and wide viewport sizes. Check keyboard navigation and meaningful headings/labels.
- Run the repository's `scripts/validate-exercise.sh` if appropriate after implementation; treat its exercise-specific checks as supplemental to the JSON parsing and browser smoke test.

## Open question

The brief requests a contributor-friendly summary but its enumerated required project fields omit `summary`. This plan recommends adding `summary` to each project while retaining all required fields.
