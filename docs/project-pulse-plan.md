# Project Pulse dashboard implementation plan

## Goal and repository findings

Build a lightweight, responsive static dashboard that lets Mona's contributors scan active projects, owners, status, recent activity, priority/risk, and concise summaries. The source brief is `.github/project-pulse-brief.md`; `.github/steps/3-step.md` adds concrete UI and run-preview expectations.

This repository is an exercise template rather than an existing web application. It currently has no `app/` directory, no `package.json` or other app dependency manifest, and no app framework, build system, or app tests. Existing VS Code configuration is limited to `.vscode/tasks.json`, which opens the Copilot CLI exercise terminal; `.vscode/launch.json` does not exist. The implementation should therefore remain a dependency-free static app and avoid changing the exercise task. The repository's `scripts/validate-exercise.sh` is the existing exercise-level validation script.

## File ownership and responsibilities

| File | Owner | Scope |
|---|---|---|
| `app/styles.css` | **Designer** | Define the visual system, information hierarchy, responsive layout, readable spacing and contrast, visible status/priority treatments, and keyboard/focus affordances. Include deterministic `.dashboard` and `.project-card` selectors, rounded card treatment, and `box-shadow`. Keep the design usable on narrow screens and avoid relying on color alone to convey status. |
| `app/index.html` | **Coder** | Create semantic page structure with the exact document title and visible heading “Project Pulse”; link `styles.css`; load `project-data.json`; render a `.project-card` for each project and visibly present name, owner, status, recent activity, priority, and a contributor-friendly summary. Provide loading and useful data-load/empty/error states. Keep markup hooks aligned with the Designer's agreed CSS selectors. |
| `app/project-data.json` | **Coder** | Supply representative, contributor-friendly project entries in a top-level `projects` array. Each entry must have `name`, `owner`, `status`, `recentActivity`, and `priority`; include a short summary to meet the brief. Use consistent values and strings suitable for display. |
| `.vscode/launch.json` | **Coder** | Add strict JSON configuration named `Run Project Pulse Dashboard`. Serve the `app/` directory with `python3 -m http.server 5500`, set the working directory to `${workspaceFolder}/app`, and open `http://localhost:%s/index.html` through `serverReadyAction` so the browser displays the dashboard, not a directory listing. |

**Designer** owns design decisions and the stylesheet only. **Coder** owns application markup, its JSON data contract/sample data, and the launch configuration. The Orchestrator coordinates the shared contract and integration; it should not take ownership of implementation files.

## Ordered phases, dependencies, and parallel work

1. **Confirm the implementation contract (Orchestrator; no app-file edits).** Use this plan and the brief as requirements. Agree that `projects` is an array of objects with the five required display fields plus a short summary, and agree on stable markup hooks including `.dashboard` and `.project-card`. No third-party packages or build step are planned.
2. **Produce independent assets in parallel.** Once the contract is clear, Designer can implement `app/styles.css` while Coder creates `app/project-data.json` and `.vscode/launch.json`. These tasks use agreed selectors, fields, and launch requirements and do not edit the same files. Coder may prepare `app/index.html` structure in parallel only if it follows the already-agreed contract; final integration waits for the assets.
3. **Integrate the dashboard markup (Coder).** Complete `app/index.html` against the data contract and CSS hooks. This depends on the agreed data shape and selector names, not on a framework. Ensure the browser fetches the JSON rather than assuming it can be read from a `file://` URL.
4. **Review and validate the integrated result (Orchestrator coordinating Designer and Coder).** Check the four assigned files together, correct any contract or accessibility mismatch with the owning agent, then report results and remaining uncertainties. Do not stage, commit, or push.

**Parallel/sequential decision:** CSS, data, and launch configuration can be implemented in parallel after the shared interface is fixed. Markup can be drafted in parallel but its final integration is sequential after confirming the data field names and CSS hooks. Browser validation is necessarily sequential after all four files are present, because it tests the combined page, data fetch, stylesheet, and launcher.

## Functional, accessibility, and edge-case requirements

- Render all project data dynamically from the JSON file, not only hard-coded example cards. Handle a failed or malformed fetch with a clear message, and handle a valid but empty `projects` array with a useful empty state.
- Keep the page useful at mobile and desktop widths. Use semantic headings and sections, readable contrast and type sizes, visible keyboard focus, and text or labels in addition to status/priority colors.
- Give status and priority consistent visual treatment without obscuring their text. Long names, summaries, and activity strings should wrap without overflowing cards.
- Serve from `app/` and open `index.html` explicitly. A local HTTP server is required for a normal browser `fetch()` of `project-data.json`; opening the HTML file directly is not an equivalent integration test.
- There is no known app-specific test runner or dependency set to extend. Keep implementation and validation within tools already present (Python 3, browser/VS Code in the Codespace); no dependency installation is currently justified.

## Concrete validation expectations

1. **Files and workflow contract:** Confirm all four assigned paths exist. Check that `app/index.html` has the exact title “Project Pulse”, references `styles.css` and `project-data.json`, and renders `.project-card` elements with status, recent activity, and priority. Check `.dashboard`, `.project-card`, `border-radius`, and `box-shadow` in `app/styles.css`.
2. **Data:** Run `python3 -m json.tool app/project-data.json`; confirm a top-level `projects` array and verify each entry includes `name`, `owner`, `status`, `recentActivity`, `priority`, and a short summary.
3. **Launch JSON:** Run `python3 -m json.tool .vscode/launch.json`; confirm the configuration name is exactly `Run Project Pulse Dashboard`, its server command is `python3 -m http.server 5500`, its `cwd` is `${workspaceFolder}/app`, and its server-ready browser URL targets `http://localhost:%s/index.html`. Verify that the selected launch type and `serverReadyAction` are supported in the target Codespace/VS Code environment; no launch extension or debugger type is declared by the current repository, so this compatibility point must be confirmed rather than assumed.
4. **Integrated browser smoke test:** Start the named VS Code launch configuration. Confirm the server becomes ready, the browser opens `/index.html`, the page shows the Project Pulse interface rather than a directory listing, project cards display all required fields from the JSON, and the stylesheet is applied. Also inspect a narrow viewport and keyboard navigation; verify clear empty/error behavior if practical. Stop the preview server afterward.
5. **Repository check:** Run `bash scripts/validate-exercise.sh` after creating the plan and implementation. This validates the exercise repository and its learner-file conventions; it is not a substitute for the browser smoke test.

The exercise workflow `.github/workflows/3-step.yml` independently checks the four file paths, key required markup/style/data/launch phrases, and JSON syntax for both JSON files. Those automated checks are a baseline, not proof of rendering, accessibility, responsiveness, or launch compatibility.

## Risks and open uncertainties

- No app files, package manifest, app tests, or pre-existing app patterns are present, so the plan assumes a small static HTML/CSS/JSON implementation without a framework.
- The brief requires a runnable launch configuration and the step prompt specifies the Python server command and URL, but this repository does not declare which VS Code debugger extension provides the launch type. Validate the chosen configuration in the Codespace and adjust only as needed to preserve the specified command, working directory, and browser target.
- The brief names the required project fields but does not prescribe exact summary property naming or project sample content. This plan recommends a concise `summary` property; confirm the final display and field name remain consistent between the JSON and markup.
