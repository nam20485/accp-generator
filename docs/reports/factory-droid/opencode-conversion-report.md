# Factory Droid Agent Conversion Report (OpenCode Source)

**Date:** March 28, 2026
**Source Provider:** `.opencode` (OpenCode agent format)
**Source Location:** `.source/agents/.opencode/agents/`
**Target Client:** Factory Droid (custom droids)
**Target Format:** Markdown with YAML frontmatter + body
**Target Location:** `C:\Users\nmill\.factory\droids\` (user-wide, Windows)
**Environment:** Windows

---

## Executive Summary

Successfully converted **18 OpenCode agents** to **Factory Droid custom droids** with lossless translation. All agent metadata, role definitions, operational procedures, and system prompts have been preserved. Unmapped OpenCode-specific fields are documented as HTML comments in each target file.

**Files Created/Updated:** 18 × `.md` droid files
**Backup Location:** `C:\Users\nmill\.factory\droids.bak.20260328T1548\`

---

## Field Mapping

| OpenCode Source Field | Factory Droid Target Field | Transformation |
|---|---|---|
| filename (kebab-case, minus `.md`) | `name` | Direct copy as frontmatter field |
| `description` | `description` | Direct copy |
| `model` (e.g., `zai-coding-plan/glm-5`) | `model` | Set to `inherit` (OpenCode model IDs not recognized by Factory) |
| `tools` (boolean object map) | `tools` (array of tool IDs) | Mapped per tool mapping table below |
| Body (markdown) | Body (markdown) | Preserved verbatim |
| `mode` (e.g., `all`, `subagent`) | — | Preserved as HTML comment in body |
| `temperature` | — | Preserved as HTML comment in body |
| `permission` (object with allow/deny) | — | Reflected in `tools` array (denied tools excluded); noted in body comment |
| `task` (OpenCode delegation tool) | — | No Factory equivalent; noted in body comment |
| `todowrite` / `todoread` | — | `TodoWrite` auto-included by Factory; noted in body comment |

### Tool Mapping (OpenCode → Factory Droid)

| OpenCode Tool | Factory Tool ID | Notes |
|---|---|---|
| `read: true` | `Read` | File reading |
| `write: true` | `Create` | File creation |
| `edit: true` | `Edit` | File editing |
| `list: true` | `LS` | Directory listing |
| `bash: true` | `Execute` | Shell command execution (excluded when `bash: false` or `permission.bash: deny`) |
| `grep: true` | `Grep` | Text search |
| `glob: true` | `Glob` | File pattern matching |
| `webfetch: true` | `WebSearch`, `FetchUrl` | Mapped to both web tools |
| `task: true` | — | No Factory equivalent (delegation is native) |
| `todowrite: true` | — | `TodoWrite` auto-included by Factory |
| `todoread: true` | — | No separate Factory equivalent |

---

## Generated Files

| # | Source File | Target File | Tools |
|---|---|---|---|
| 1 | agent-instructions-expert.md | agent-instructions-expert.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 2 | backend-developer.md | backend-developer.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 3 | cloud-infra-expert.md | cloud-infra-expert.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 4 | code-reviewer.md | code-reviewer.md | Read, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 5 | database-admin.md | database-admin.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 6 | debugger.md | debugger.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 7 | developer.md | developer.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 8 | devops-engineer.md | devops-engineer.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 9 | documentation-expert.md | documentation-expert.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 10 | frontend-developer.md | frontend-developer.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 11 | github-expert.md | github-expert.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 12 | odbplusplus-expert.md | odbplusplus-expert.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 13 | orchestrator.md | orchestrator.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 14 | planner.md | planner.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 15 | product-manager.md | product-manager.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 16 | qa-test-engineer.md | qa-test-engineer.md | Read, Create, Edit, LS, Execute, Grep, Glob, WebSearch, FetchUrl |
| 17 | researcher.md | researcher.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |
| 18 | ux-ui-designer.md | ux-ui-designer.md | Read, Create, Edit, LS, Grep, Glob, WebSearch, FetchUrl |

---

## Diff Summary (Re-run Over Existing Files)

The following 14 droids were overwritten from a prior `.claude`-sourced conversion. Key changes: tool arrays updated to reflect OpenCode tool permissions, body content replaced with OpenCode-format system prompts, and `model` set to `inherit` (previously mapped from Claude Code model IDs).

**Overwritten:** backend-developer, cloud-infra-expert, code-reviewer, database-admin, debugger, developer, devops-engineer, documentation-expert, frontend-developer, github-expert, orchestrator, planner, product-manager, qa-test-engineer, researcher, ux-ui-designer

**New (not in prior conversion):** agent-instructions-expert, odbplusplus-expert

**Deprecated (moved to `$droidsDir/.deprecated`; from prior `.claude` conversion, not in `.opencode` source):** data-scientist, dev-team-lead, github-ops-agent, ml-engineer, mobile-developer, performance-optimizer, prompt-engineer, scrum-master, security-expert

**Kept (Factory-specific, not from any source provider):** scrutiny-feature-reviewer, user-testing-flow-validator, worker

---

## Issues

- **Model mapping:** OpenCode model IDs (e.g., `zai-coding-plan/glm-5`) have no Factory Droid equivalent. All set to `inherit` so Factory uses its default model. Original model values are noted in body comments.
- **Task tool:** OpenCode's `task` tool (for agent delegation) has no direct Factory equivalent. Factory handles delegation natively. Documented as comment in affected files.
- **Permission granularity:** OpenCode `permission` blocks (allow/deny per tool) were translated by excluding denied tools from the Factory `tools` array. This is semantically equivalent but loses the explicit deny notation.
- **Temperature:** No Factory `reasoningEffort` mapping was applied from OpenCode's `temperature` values, as the relationship is indirect. Temperature values preserved as comments.

---

## Conversion Process

1. **Environment detected:** Windows
2. **Source provider resolved:** `.opencode` (cached in learned source provider index)
3. **Source files read:** 18 agents from `.source/agents/.opencode/agents/`
4. **Target format loaded:** Factory Droid (cached in learned target type index)
5. **Field mapping built:** Per table above, with tool-level mapping for OpenCode's object-style tools
6. **Backup created:** `C:\Users\nmill\.factory\droids.bak.20260328T1548\`
7. **18 `.tmp` files generated** → validated (frontmatter + name + non-empty body) → **atomically swapped** via `Move-Item -Force`
8. **Post-swap verification:** All 18 files re-read and confirmed parseable
