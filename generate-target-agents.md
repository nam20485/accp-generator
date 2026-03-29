# Generate Target Client Agents

Use the source custom agent files and translate them from the source format into the target's specific format, then add them to the target's custom agent location.

## Goal

Your goal is to create corresponding agent files in the target's format and place them at the target's user-wide custom agent location in the **same environment as the source**. First detect whether the source is being run from **Windows** or **Linux/WSL**, record that as the target environment, and then use the target's user-wide location for that same environment. Use the web fetch tools to discover the target docs and recursively follow any documentation links you find along the way, executing non-interactively without user intervention.

## Source provider selection

Source agent files are organized by provider under `.source/agents/`, with each provider in its own sub-directory:

```
.source/agents/
├── .claude/agents/       # Claude Code format agent definitions
├── .opencode/agents/     # OpenCode format agent definitions
├── list.md               # Shared agent index (provider-agnostic)
└── (future providers)/
```

The user specifies **both** a source provider and a target client type when requesting a conversion. The source provider determines which sub-directory to read from and what format/conventions to expect; the target type determines where and how to write the output.

### Default source provider

> **Default: `.claude`**

When no source provider is specified, use `.claude` as the source provider. This preserves backward compatibility with the original single-source workflow.

### Specifying a source provider

The user may specify the source provider by name (e.g., `.claude`, `.opencode`) or by the sub-directory path. The provider name corresponds to the sub-directory name under `.source/agents/`. Examples:

- "Convert agents from `.claude` to Kilo Code" → source = `.claude`, target = Kilo Code
- "Convert `.opencode` agents to Factory Droid" → source = `.opencode`, target = Factory Droid
- "Convert agents to VS Code Copilot" → source = `.claude` (default), target = VS Code Copilot

## Learned source provider index

Maintain an index of learned source providers in this section. Add a new subsection per source provider as you discover its agent file location, format, frontmatter fields, and conventions. Append new subsections in chronological order. Each entry records the provider's sub-directory, file format, required/optional fields, and any provider-specific conventions.

When a source provider is specified (or defaulted), check this index first. If a cached entry exists, use it. If not, read the source files in the provider's sub-directory to discover the format, then cache the result here for future runs.

### .claude

- Sub-directory: `.source/agents/.claude/agents/`
- Format: Markdown with YAML frontmatter followed by the system prompt body (Claude Code sub-agent format)
- Documentation URL: <https://code.claude.com/docs/en/sub-agents#write-subagent-files>
- Required frontmatter fields: `name`, `description`
- Optional frontmatter fields: `tools`, `disallowedTools`, `model`, `permissionMode`, `skills`, `hooks`
- Body: Markdown content serving as the agent's system prompt
- File extension: `.md`
- Naming: filename matches the agent `name` field (kebab-case)

### .opencode

- Sub-directory: `.source/agents/.opencode/agents/`
- Format: Markdown with YAML frontmatter followed by the system prompt body (OpenCode agent format)
- Documentation URL: <https://opencode.ai/docs/agents>
- Required frontmatter fields: `description`
- Optional frontmatter fields: `mode`, `model`, `temperature`, `permission` (object with tool-category: allow/ask/deny; categories: `edit`, `bash`, `webfetch`, `task`)
- Deprecated frontmatter fields: `tools` (object with tool-name: boolean pairs) — retained as YAML comments for reference; superseded by `permission`
- Body: Markdown content serving as the agent's system prompt
- File extension: `.md`
- Naming: filename matches the agent name (kebab-case)
- Notes: The `permission` field controls tool access with three levels: `allow` (default, can be omitted), `ask` (prompt before running), `deny` (disable). The `edit` permission covers write/edit/patch/multiedit. The `bash` permission supports per-command glob patterns. The legacy `tools` field (`read: true`, `bash: false`) is deprecated; `true` ≡ `allow` (default), `false` ≡ `deny`. `mode` field (e.g., `all`, `primary`, `subagent`) controls agent visibility.

## Target agent files location and format

Given the target (name/version or vendor docs URL), automatically search the web for the target's documentation site **without asking the user for URLs**. Use the web fetch tools to locate and read the documentation, then search for the section on custom agents/sub-agents. In that section you need to find:

0. Environment-specific locations: determine and record the target's user-wide location for **Windows** and for **Linux/WSL** when both are documented.
1. Location: the user-wide location and directory name for its custom agent files (NOT local workspace directory)
2. File format, valid config fields, and file naming rules

After reading all of this information (gathered non-interactively via web fetch tools):

1. Create a model and mapping between each field in the source file (frontmatter + body) and the most appropriate field in the target's format.
2. Document how unmapped fields are preserved (e.g., comments or extra metadata fields) to ensure a lossless translation.

## Generate Target Agent Files

For each agent file in the resolved source provider's agent file location (see "Learned source provider index"), generate a corresponding target agent file by translating from the source provider's format into the target agent's format using the model mapping from the previous section.

**IMPORTANT** The translation must be lossless—do not leave out any info from the source file.

**CRITICAL — Backup and safe-write procedure:**

1. **Backup first.** Before making any changes, copy each target file (or the entire target directory) to a timestamped backup (e.g., `<filename>.bak.20260301T1423`). Never modify the original without a backup in place.
2. **Double-buffer edits.** Copy the target file to a temporary working file (e.g., `<filename>.tmp` in the same directory). Apply all changes to the temp copy. Only after the temp copy is verified correct, atomically replace the original with the temp copy (e.g., `Move-Item -Force` on Windows). This prevents a half-written or corrupt file if the process is interrupted.
3. **Verify after swap.** After the swap, read back the target file and confirm it parses correctly. If verification fails, restore from the backup immediately.

Create the files at the target's **user-wide** location in the **same environment as the source** (Windows source -> Windows target location, Linux/WSL source -> Linux/WSL target location), not the local workspace directory.

If validation or checks are required, ask the user before running any commands.

Use Windows-first (PowerShell) guidance when providing any shell instructions.

## Learned target type index

Maintain an index of learned target types in this section. Add a new subsection per target type as you learn its user-wide location, file format, and valid fields. Append new subsections in chronological order so the most recently learned target types are last. Include the URL of the target type's documentation where the information was found in each index entry.

Record the **environment** for each learned entry (`Windows` or `Linux/WSL`). If paths differ by environment, create separate entries for the same target type (for example, one Windows entry and one Linux/WSL entry) rather than collapsing them into a single path.

> **Note:** The source provider index (above) and target type index (below) serve analogous caching roles. The source provider index caches how to _read_ agent definitions; the target type index caches how to _write_ them. A conversion run uses exactly one entry from each index.

## Conclusion

After each run, generate a markdown report detailing the conversion process. Include the source provider used, a list of generated files, the target locations you used, and a summary of the conversion process. Also include any issues you faced and whether you overcame them or they still persist.

### Claude Code (subagents)

- Documentation URL: https://code.claude.com/docs/en/sub-agents
- User-wide location: `~/.claude/agents/` (available across all projects).
- Project-level location (for reference): `.claude/agents/` (project-specific, version-controlled).
- File format: Markdown file with YAML frontmatter followed by the system prompt body.
- Required frontmatter fields: `name`, `description`.
- Optional frontmatter fields: `tools`, `disallowedTools`, `model`, `permissionMode`, `skills`, `hooks`.
- Notes: the frontmatter defines subagent metadata and configuration; the body becomes the subagent’s system prompt. CLI-defined subagents can be provided via `--agents` JSON with `description`, `prompt`, `tools`, and `model` fields.

### Factory (custom droids)

- Documentation URL: https://docs.factory.ai/cli/configuration/custom-droids
- User-wide location: `~/.factory/droids/`.
- Project-level location: `.factory/droids/`.
- File format: Markdown with YAML frontmatter followed by the prompt body; body must be non-empty.
- Required frontmatter fields: `name` (lowercase letters, digits, hyphens, underscores; drives filename/`subagent_type`).
- Optional frontmatter fields: `description`, `model` (`inherit` or explicit model ID), `reasoningEffort` (e.g., low/medium/high), `tools` (omit for all tools; use category string like `read-only` or an array of tool IDs such as `Read`, `LS`, `Grep`, `Glob`, `Edit`, `Create`, `ApplyPatch`, `Execute`, `WebSearch`, `FetchUrl`; `TodoWrite` is auto-included).
- Naming rules: top-level `.md` files only; CLI normalizes filenames to lowercase, hyphenated slugs.
- Notes: project droids override personal droids on name conflicts; validator flags unknown models/tools.

### Kilo Code (custom modes)

- Documentation URL: https://kilo.ai/docs/customize/custom-modes
- User-wide location: `~/.kilo/` (Windows: `C:\Users\{username}\.kilo\`).
- Project-level location: `.kilocodemodes` (YAML or JSON file in workspace root).
- File format: YAML (preferred) or JSON configuration file with custom mode definitions.
- Main config file: `custom_modes.yaml` (global user-wide) or `.kilocodemodes` (project-level).
- Required fields: `slug` (unique identifier, pattern `/^[a-zA-Z0-9-]+$/`), `name` (display name), `description` (user-friendly summary), `roleDefinition` (core identity for system prompt), `groups` (tool access array).
- Optional fields: `whenToUse` (guidance for automated mode selection), `customInstructions` (additional behavioral guidelines), `model` (AI model override, defaults to `inherit`).
- Tool groups: `"read"`, `"edit"`, `"command"`, `"browser"`, `"mcp"`.
- File restrictions: Use tuple format `["edit", { fileRegex: "pattern", description: "text" }]` for edit group restrictions.
- Mode-specific instructions: Preferred method is directory-based at `~/.kilo/rules-{slug}/` (global) or `.kilo/rules-{slug}/` (project). Fallback is single file `.kilorules-{slug}`.
- Naming rules: Slugs must be lowercase letters, numbers, and hyphens only. Names support spaces and emojis for UI display.
- Notes: YAML uses single backslash escaping for regex (e.g., `\.md$`); JSON requires double backslash (e.g., `\\.md$`). Project modes override global modes with same slug. Can override built-in modes by using matching slugs. Instruction files are read recursively in alphabetical order.

### VS Code GitHub Copilot Chat (custom agents)

- Documentation URL: https://code.visualstudio.com/docs/copilot/customization/custom-agents
- Subagents documentation: https://code.visualstudio.com/docs/copilot/agents/subagents
- User-wide location: VS Code user profile `agents/` folder (Windows: `%APPDATA%\Code - Insiders\User\agents\` for Insiders, `%APPDATA%\Code\User\agents\` for stable).
- Workspace location: `.github/agents/` folder (also detects `.md` files in `.github/agents/`).
- Claude compatibility: Also detects `.md` files in `.claude/agents/` folder using Claude sub-agents format.
- File format: Markdown with `.agent.md` extension, YAML frontmatter + body.
- Required frontmatter fields: none strictly required; `name` and `description` recommended.
- Optional frontmatter fields: `name` (display name; filename used if omitted), `description` (placeholder text in chat input), `argument-hint` (input guidance text), `tools` (array of tool set names, MCP tool refs, or extension tools), `agents` (array of allowed subagent names; `'*'` for all, `[]` for none), `model` (string or prioritized array; format `Model Name (vendor)`), `user-invokable` (boolean, default `true`), `disable-model-invocation` (boolean, default `false`), `target` (`vscode` or `github-copilot`), `mcp-servers` (MCP server config JSON), `handoffs` (list of handoff definitions with `label`, `agent`, `prompt`, `send`, `model`).
- Tool sets: `'read'`, `'edit'`, `'search'`, `'command'`, `'fetch'`, `'agent'`. MCP tools via `<server-name>/*` format.
- Naming rules: File extension must be `.agent.md`. Filename (minus extension) is the default agent name.
- Notes: Unavailable tools are silently ignored. `chat.agentFilesLocations` setting can configure additional search locations. Previously known as "custom chat modes" (`.chatmode.md`). `infer` field is deprecated in favor of `user-invokable` and `disable-model-invocation`.
