# Available Agents

This document lists all available agents organized by provider under `.source/agents/<provider>/agents/` with their names, descriptions, and primary use cases.

## Agent Directory

| Agent Name                | Description                                                                                                     | Primary Use Case                                    |
| ------------------------- | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **backend-developer**     | Designs and delivers backend services with robust testing, resiliency, and observability.                       | Backend API development, service architecture       |
| **cloud-infra-expert**    | Architects resilient, secure, and cost-efficient cloud infrastructure with IaC and governance controls.         | Cloud infrastructure, IaC, cloud migrations         |
| **code-reviewer**         | Provides rigorous code reviews covering correctness, security, performance, and documentation.                  | Code review, PR analysis, quality assurance         |
| **database-admin**        | Designs, optimizes, and safeguards relational/NoSQL data stores with strong governance.                         | Database design, optimization, migrations           |
| **debugger**              | Reproduces issues, writes minimal failing tests, proposes and validates fixes.                                  | Bug investigation, root cause analysis, fixes       |
| **developer**             | Generalist engineer delivering small, cross-cutting enhancements with quality safeguards.                       | General development, feature implementation         |
| **devops-engineer**       | Designs and maintains CI/CD pipelines, environments, and automation with observability and security baked in.   | CI/CD, automation, infrastructure as code           |
| **documentation-expert**  | Writes developer and user docs, quickstarts, runbooks, and maintains knowledge base consistency.                | Documentation, technical writing, runbooks          |
| **frontend-developer**    | Builds accessible, performant UI components and flows with thorough testing and documentation.                  | UI development, frontend features                   |
| **github-expert**         | Manages GitHub repositories, workflows, issues, PRs, and team collaboration patterns with automation expertise. | GitHub operations, workflows, repository management |
| **orchestrator**          | Portfolio conductor for AI initiatives; plans, delegates, and approves without direct implementation.           | High-level coordination, strategic delegation       |
| **planner**               | Converts strategic goals into sequenced milestones with dependencies and acceptance criteria.                   | Project planning, milestone definition              |
| **product-manager**       | Outcome-oriented strategist; captures customer value and aligns delivery plans.                                 | Product strategy, roadmap planning, requirements    |
| **qa-test-engineer**      | Defines test strategies, executes validation suites, and enforces quality gates before release.                 | Test automation, quality assurance, validation      |
| **researcher**            | Gathers, synthesizes, and distills information from multiple sources into actionable insights with citations.   | Research, information synthesis, documentation      |
| **ux-ui-designer**        | Designs user flows, wireframes, interaction patterns, and conducts accessibility and design QA reviews.         | UX/UI design, user flows, accessibility             |

## Usage

To invoke a specific agent, use:
```
> Use the [agent-name] agent to [task description]
```

Examples:
```
> Use the code-reviewer agent to check my recent changes
> Use the debugger agent to investigate this test failure
> Use the planner agent to break down this feature into milestones
```

## Agent Categories

### Leadership & Coordination
- **orchestrator** - High-level portfolio coordination
- **planner** - Strategic planning and milestones

### Development
- **developer** - General development
- **backend-developer** - Backend services
- **frontend-developer** - UI/UX implementation

### Quality & Review
- **code-reviewer** - Code review and analysis
- **debugger** - Bug investigation and fixes
- **qa-test-engineer** - Testing and validation

### Infrastructure & Operations
- **devops-engineer** - CI/CD and automation
- **cloud-infra-expert** - Cloud infrastructure
- **database-admin** - Database management

### Specialized Roles
- **documentation-expert** - Technical documentation
- **researcher** - Research and synthesis
- **product-manager** - Product strategy
- **github-expert** - GitHub operations
- **ux-ui-designer** - User experience design

## Configuration

Each agent is configured with:
- **name**: Unique identifier
- **description**: When the agent should be invoked
- **tools**: Specific tools the agent can access (optional)
- **model**: AI model to use (optional, defaults to sonnet)

Agents are stored in `.source/agents/<provider>/agents/` and follow the format:
```markdown
---
name: agent-name
description: Description of when this agent should be invoked
tools: Tool1, Tool2, Tool3  # Optional
model: sonnet  # Optional
---

Agent's system prompt and detailed instructions...
```
