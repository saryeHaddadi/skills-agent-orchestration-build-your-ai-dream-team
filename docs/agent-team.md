# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard:

- **Planner** — targets **Claude Opus 4.7 (copilot)**. Researches the repository, documentation, dependencies, and edge cases, then produces an implementation plan with file ownership and validation expectations. Definition: `.github/agents/planner.agent.md`.
- **Designer** — targets **Gemini 3.1 Pro (copilot)**. Shapes the dashboard's user experience, accessibility, information hierarchy, interactions, responsive behavior, and visual design. Definition: `.github/agents/designer.agent.md`.
- **Coder** — targets **GPT-5.5 (copilot)**. Implements assigned application code and runnable-app support, following repository patterns and validating the changes. Definition: `.github/agents/coder.agent.md`.
- **Orchestrator** — targets **Claude Opus 4.7 (copilot)**. Coordinates the Planner, Designer, and Coder, assigns scoped work in dependency-aware phases, integrates their contributions, and reports the outcome. Definition: `.github/agents/orchestrator.agent.md`.

I am using **GitHub Copilot CLI in a Codespace** to orchestrate this work.
