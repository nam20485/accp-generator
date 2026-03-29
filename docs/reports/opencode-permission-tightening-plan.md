# OpenCode Agent Permission Tightening Plan

Tighten `.source/agents/.opencode/agents/` permissions so each agent only has access to tools it **definitely needs**, reducing hallucination and off-task behavior.

> **Guiding principle:** Only deny permissions that are **clearly outside** the agent's mandate based on its description, mission, and system prompt. When in doubt, leave allowed.

**IMPORTANT:** You cannot use the ask permission value for any agent or tools, bc these agents are used automatically by the system and are not called by the user. For any ask permission, use deny instead if the agent definitely does not need the tool.

---

## Available Permission Keys

From <https://opencode.ai/docs/permissions#available-permissions>:

| Key | What it controls | Feedback |
|---|---|---|
| `read` | Reading files (matches file path) | |
| `edit` | All file modifications (`edit`, `write`, `patch`, `multiedit`) | |
| `glob` | File globbing (matches glob pattern) | |
| `grep` | Content search (matches regex pattern) | |
| `list` | Listing files in a directory (matches directory path) | |
| `bash` | Running shell commands (matches parsed commands) | |
| `task` | Launching subagents (matches subagent type) | |
| `skill` | Loading a SKILL.md file (matches skill name) | |
| `lsp` | Running LSP queries (currently non-granular, experimental) | |
| `webfetch` | Fetching a URL (matches the URL) | |
| `websearch` | Web search (matches the query) | |
| `codesearch` | Code search (matches the query) | |
| `external_directory` | Triggered when a tool touches paths outside the project | |
| `doom_loop` | Triggered when the same tool call repeats 3× with identical input | |

---

## Permissions That Stay `allow` (Default) for ALL Agents

| Permission | Rationale | Feedback |
|---|---|---|
| `read` | Every agent needs to read files | |
| `glob` | Every agent needs file discovery | |
| `grep` | Every agent needs content search | |
| `list` | Every agent needs directory listing | |
| `skill` | Loading skill definitions provides useful guidance | |
| `doom_loop` | Safety guard — defaults to `ask`, leave untouched | |
| `external_directory` | Safety guard — defaults to `ask`, leave untouched | |

---

## Group 1 — Non-implementing coordinators (no code, no shell)

Agents that explicitly delegate all implementation and produce artifacts as conversation responses.

| Agent | Current `permission` | Proposed additions | Rationale | Feedback |
|---|---|---|---|---|
| **orchestrator** | `bash: deny` | `edit: deny`, `websearch: deny`, `codesearch: deny`, `lsp: deny` | Prompt: "You never produce code, scripts, or any executable content directly." Delegates all research to researcher. No code navigation needed. | |
| **planner** | `bash: deny` | `lsp: deny` | Creates plans/roadmaps; may write plan documents directly. No code navigation via LSP. Keep `edit` for plan files, `websearch`/`webfetch` for estimation research. | |
| **product-manager** | `bash: deny` | `edit: deny`, `lsp: deny` | Strategy/roadmapping role. Partners with researcher for market insight; no code writing or navigation. Keep `websearch`/`webfetch` for market research. | |

---

## Group 2 — Read-only reviewers

| Agent | Current `permission` | Proposed additions | Rationale | Feedback |
|---|---|---|---|---|
| **code-reviewer** | `edit: deny` | `websearch: deny` | Reviews existing diffs; no general web search needed. Keep `bash` (runs linters/tests), `lsp` (go-to-definition, find-references), `codesearch` (find relevant code references), `webfetch` (check referenced URLs in code/docs). | |

---

## Group 3 — Research/analysis subagents (no code modification, no shell)

| Agent | Current `permission` | Proposed additions | Rationale | Feedback |
|---|---|---|---|---|
| **researcher** | `bash: deny` | `lsp: deny` | Gathers context and may write brief/summary files. No code navigation via LSP. Keep `edit` for writing research artifacts, `webfetch`/`websearch` (core to research). | |
| **ux-ui-designer** | `bash: deny` | `edit: deny`, `lsp: deny`, `codesearch: deny` | Design role — drafts flows/wireframes as conversation content. Reviews implementations visually but doesn't modify code or navigate it via LSP. Keep `webfetch`/`websearch` for design research. | |

---

## Group 4 — Leaf subagents (specialized retrieval, no delegation)

| Agent | Current `permission` | Proposed additions | Rationale | Feedback |
|---|---|---|---|---|
| **agent-instructions-expert** | `bash: deny`, `task: deny` | `edit: deny`, `websearch: deny` | Retrieves guidance from local canonical repo only. "Returns minimal focused responses." Should not write files or search the web. Keep `webfetch` (may fetch canonical repo URLs), `codesearch`/`lsp` (navigate code for relevant examples to cite). | |
| **odbplusplus-expert** | `bash: deny` | `task: deny`, `edit: deny`, `websearch: deny` | Leaf researcher on local ODB++ spec and OdbDesign codebase. No delegation, no file writes, no web search. Keep `webfetch` (might fetch GitHub issue URLs), `codesearch`/`lsp` (navigate OdbDesign codebase for relevant sections). | |

---

## Group 6 — Full implementation agents (no changes)

| Agent | Current `permission` | Proposed changes | Rationale | Feedback |
|---|---|---|---|---|
| **documentation-expert** | `bash: deny` | *(none)* | Writes docs, reads and navigates code. Needs `edit`, `lsp`, `codesearch` for finding relevant code examples. `bash: deny` already set. | |
| **backend-developer** | *(none)* | *(none)* | Full dev — needs all tools | |
| **cloud-infra-expert** | *(none)* | *(none)* | IaC architect — needs bash (terraform), edit, webfetch, task | |
| **database-admin** | *(none)* | *(none)* | DBA — needs bash (SQL, migrations), edit, task | |
| **debugger** | *(none)* | *(none)* | Needs bash (run tests), edit (write failing tests, apply fixes), task (delegate to dev) | |
| **developer** | *(none)* | *(none)* | Generalist — needs everything | |
| **devops-engineer** | *(none)* | *(none)* | CI/CD — needs bash, edit, webfetch, task | |
| **frontend-developer** | *(none)* | *(none)* | Full frontend dev — needs everything | |
| **github-expert** | *(none)* | *(none)* | GitHub ops — needs bash (git/gh CLI), edit (workflow files), webfetch (GitHub API docs) | |
| **qa-test-engineer** | *(none)* | *(none)* | Testing — needs bash (run tests), edit (write tests), task (coordinate) | |

---

## Full Permission Matrix

`✓` = allow (default), `✗` = deny, **bold** = newly proposed deny.

| Agent | `edit` | `bash` | `task` | `webfetch` | `websearch` | `codesearch` | `lsp` | Feedback |
|---|---|---|---|---|---|---|---|---|
| orchestrator | **✗** | ✗ | ✓ | ✓ | **✗** | **✗** | **✗** | |
| planner | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | **✗** | |
| product-manager | **✗** | ✗ | ✓ | ✓ | ✓ | ✓ | **✗** | |
| code-reviewer | ✗ | ✓ | ✓ | ✓ | **✗** | ✓ | ✓ | |
| researcher | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | **✗** | |
| ux-ui-designer | **✗** | ✗ | ✓ | ✓ | ✓ | **✗** | **✗** | |
| agent-instructions-expert | **✗** | ✗ | ✗ | ✓ | **✗** | ✓ | ✓ | |
| odbplusplus-expert | **✗** | ✗ | **✗** | ✓ | **✗** | ✓ | ✓ | |
| documentation-expert | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| backend-developer | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| cloud-infra-expert | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| database-admin | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| debugger | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| developer | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| devops-engineer | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| frontend-developer | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| github-expert | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |
| qa-test-engineer | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | |

---

## Execution Steps

1. ~~Edit all 8 affected agents' `permission:` blocks in `.source/agents/.opencode/agents/`~~ ✅ done — commit `31ff190`
2. ~~Validate all 18 files parse correctly (frontmatter intact, no active `tools:` key)~~ ✅ done
3. ~~Spot-check: confirm no agent lost a permission it needs~~ ✅ done
4. Update `.opencode` cached entry in `generate-target-agents.md` if needed

## Status

**Implemented** — 2026-03-28, commit `31ff190` ("tighten opencode agent permissions per plan")

Summary of changes applied to `.source/agents/.opencode/agents/`:

| Agent | Permissions added |
|---|---|
| `orchestrator.md` | `edit: deny`, `websearch: deny`, `codesearch: deny`, `lsp: deny` |
| `planner.md` | `lsp: deny` |
| `product-manager.md` | `edit: deny`, `lsp: deny` |
| `code-reviewer.md` | `websearch: deny` |
| `researcher.md` | `lsp: deny` |
| `ux-ui-designer.md` | `edit: deny`, `codesearch: deny`, `lsp: deny` |
| `agent-instructions-expert.md` | `edit: deny`, `websearch: deny` |
| `odbplusplus-expert.md` | `task: deny`, `edit: deny`, `websearch: deny` |

---

## Risk Mitigation

- All denials are conservative — only applied where the agent's own system prompt explicitly excludes the activity
- `webfetch` is kept for every agent (URLs may appear anywhere)
- `websearch` is only denied for agents that work from local sources or have no research mandate
- `task` is only denied for the 2 leaf subagents (+ existing agent-instructions-expert)
- If any denial proves too restrictive, changing `deny` → `allow` (or `ask` for a softer approach) is a one-line fix per agent
