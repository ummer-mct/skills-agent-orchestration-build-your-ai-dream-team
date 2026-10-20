# Agent team

The custom team builds Mona's Project Pulse dashboard through a coordinated plan-design-implementation workflow. The **Orchestrator** starts by asking the **Planner** to research the repository and produce an actionable plan, then assigns non-overlapping file scopes to the **Designer** and **Coder**. It runs independent work in parallel, sequences dependent work, and verifies the integrated result; it coordinates rather than writing code itself.

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| Planner | Claude Opus 5.5 (copilot) | Reads the relevant code and documentation, checks dependencies and edge cases, and returns ordered steps, file assignments, dependencies, parallelizable work, risks, validation expectations, and open questions. Plans only; does not write code. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Defines an accessible, responsive, clear dashboard experience. Project Pulse styling should include visible project cards, status badges, priority treatment, spacing, and deterministic `.dashboard` and `.project-card` CSS hooks. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements assigned logic and support files with explicit errors, predictable behavior, and validation. For assigned runnable-app work, can configure `.vscode/launch.json` to use `${workspaceFolder}/app` and open `index.html`. | `.github/agents/coder.agent.md` |
| Orchestrator | Claude Opus 5.5 (copilot) | Breaks the plan into phases, delegates with explicit file scopes, respects dependencies and overlap, and checks that the integrated result works. Coordinates but does not implement. | `.github/agents/orchestrator.agent.md` |

All agents leave staging, committing, and pushing to the learner. I am using **GitHub Copilot CLI in a Codespace** to orchestrate this work with the custom agent team.
