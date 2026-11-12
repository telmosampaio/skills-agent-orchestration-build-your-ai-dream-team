# Agent Team

This document describes the custom agent team orchestrated through the GitHub Copilot CLI in a Codespace to build Project Pulse, Mona's dashboard for managing AI projects.

## Agents

| Agent | Model | Responsibility | File Path | Description |
|-------|-------|-----------------|-----------|-------------|
| **Orchestrator** | Claude Opus 5.5 (copilot) | Project coordination and task delegation | [`.github/agents/orchestrator.agent.md`](./.github/agents/orchestrator.agent.md) | Breaks down complex requests into tasks and delegates to specialist subagents. Coordinates work across Planner, Coder, and Designer while managing execution phases, parallel vs. sequential work, and file scope ownership to prevent conflicts. |
| **Planner** | Claude Opus 5.5 (copilot) | Technical planning and strategy | [`.github/agents/planner.agent.md`](./.github/agents/planner.agent.md) | Researches the codebase, documentation, dependencies, and edge cases to produce practical implementation plans. Creates ordered steps, identifies file assignments, defines dependencies, and highlights risks without hiding uncertainty. |
| **Coder** | GPT-5.5 (copilot) | Code implementation and bug fixes | [`.github/agents/coder.agent.md`](./.github/agents/coder.agent.md) | Implements code-oriented tasks with clear structure, explicit errors, and testable behavior. Stays within assigned file scopes, follows repository patterns, and creates support configuration (like `.vscode/launch.json`) when needed for runnable applications. |
| **Designer** | Gemini 3.7 Flash (copilot) | UI/UX design and visual polish | [`.github/agents/designer.agent.md`](./.github/agents/designer.agent.md) | Handles usability, accessibility, information architecture, interaction flow, and visual design. Creates polished dashboards with project cards, status badges, responsive layouts, and clear visual affordances that deliver excellent user experience. |

## How the Team Works Together

The team operates through a coordinated workflow orchestrated by the Orchestrator:

1. **Planning Phase**: The Planner researches Project Pulse requirements, analyzes the codebase, identifies dependencies, and produces a detailed implementation strategy with clear file assignments.

2. **Coordination**: The Orchestrator parses the plan into phases and manages execution by:
   - Running tasks in parallel when file scopes don't overlap and there are no data dependencies
   - Running tasks sequentially when work overlaps, depends on earlier output, or needs approval
   - Assigning explicit file scopes to each specialist to prevent conflicts

3. **Parallel Implementation**: The Coder and Designer work together on Project Pulse:
   - The **Coder** implements the core logic, data structures, and functionality within assigned files
   - The **Designer** simultaneously works on UI/UX, styling, and visual presentation in design-specific files
   - Both teams validate their work independently

4. **Integration & Validation**: The Orchestrator verifies that the integrated result cohesively builds Project Pulse and reports the final outcome.

5. **Git Control**: All agents respect a key principle—they do not stage, commit, or push changes. The learner controls all git operations through Copilot CLI prompts in the Codespace.

## Project Pulse Context

Project Pulse is a dashboard that helps Mona manage AI projects with:
- Visible project cards and status badges
- Clear priority treatment and readable spacing
- Responsive design that works across devices
- Polished, production-ready appearance
- Clear information hierarchy and interaction flows

The agent team's combined expertise—strategic planning, robust implementation, and excellent design—ensures Project Pulse is built as a cohesive, high-quality application.
