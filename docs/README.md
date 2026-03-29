# Source Agent Definitions

This project defines source agent definitions organized by provider, used to generate target client agent files via the accp-generator workflow on Windows (PowerShell/pwsh).

- Source agents are organized by provider under `.source/agents/<provider>/agents/`.
  - `.source/agents/.claude/agents/` — Claude Code format
  - `.source/agents/.opencode/agents/` — OpenCode format
- Format: Markdown with YAML frontmatter (provider-specific fields; see files for examples).
- Shell defaults: Windows, prefer PowerShell (pwsh). Avoid bash-only commands.

Quick start:
1) Choose a source provider (default: `.claude`) and a target client type.
2) Run the `generate-target-agents` prompt or skill to translate agents to the target format.
3) See `generate-target-agents.md` for the full workflow, source provider index, and target type index.

Primary references:
- `.source/agents/list.md` (agent index)
- `generate-target-agents.md` (task guide with source provider and target type caches)
- https://github.com/nam20485/agent-instructions (canonical instruction modules)

---

## Agent index

Core
- orchestrator — Plans, delegates, approves; avoids direct implementation.
- researcher — Uses gemini-mcp to research and produce citation-rich briefs.
- code-reviewer — Reviews diffs for correctness, security, performance, and style.

Build & Quality
- qa-test-engineer — Designs and runs tests; validates green builds.
- devops-engineer — CI/CD, reproducible builds, observability basics.
- frontend-developer — UI components/pages with component tests.
- backend-developer — Endpoints/modules with unit/integration tests.

Planning
- planner — Breaks work into tasks with acceptance criteria.
- product-manager — Defines goals, constraints, acceptance criteria.
- scrum-master — Facilitates cadence; removes blockers; enforces DoD.

Specialized
- cloud-infra-expert — Cloud architecture, IaC patterns, security baselines.
- performance-optimizer — Profiles and enforces performance budgets.
- security-expert — Threat modeling, secrets hygiene, dependency risk.
- database-admin — Schema/migrations, performance, backup/restore.
- data-scientist — Data pipelines, metrics, experiments, reproducibility.
- ml-engineer — Model training/inference, evaluation, deployment readiness.
- ux-ui-designer — Wireframes, flows, accessibility, design QA.
- mobile-developer — Platform-specific builds and store readiness.
- debugger — Repro steps, minimal failing tests, fix validation.
- developer — Generalist for small, scoped tasks.
- documentation-expert — Writes developer and user docs, quickstarts, and runbooks.
- prompt-engineer — System prompts, tool routing, guardrails.
