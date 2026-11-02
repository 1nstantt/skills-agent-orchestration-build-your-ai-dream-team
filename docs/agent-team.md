# Agent team

The custom agent team for Mona's Project Pulse dashboard is orchestrated with GitHub Copilot CLI in a Codespace.

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| **Orchestrator** | Claude Opus 5.5 (copilot) | Coordinates the specialists, divides work into phases with explicit file scopes, manages dependencies and parallel work, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 5.5 (copilot) | Researches the repository and relevant documentation, then produces an ordered implementation plan with file assignments, dependencies, risks, edge cases, and validation expectations. Does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.7 Flash (copilot) | Shapes the dashboard's user experience, visual hierarchy, accessibility, interaction flow, and responsive design, following existing product patterns. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements assigned application logic and runnable-app support with clear, testable code; follows repository patterns and validates changes. | `.github/agents/coder.agent.md` |

The Orchestrator gets a plan first, delegates design and implementation within non-overlapping scopes where possible, and integrates and verifies the result. The learner retains control of staging, commits, and pushes.
