---
name: generate-target-agents
description: "Translate source agent files from a specified source provider into a target client's custom agent format. Use when: converting agents to VS Code Copilot, Factory Droid, Kilo Code, or other targets; generating target agent files; migrating agents cross-platform; building field mappings from source to target format."
argument-hint: "Source provider and target client (e.g., '.opencode to Factory Droid', 'Kilo Code' — defaults to .claude source)"
tools:
  - read
  - edit
  - search
  - command
  - fetch
---

# Generate Target Client Agents

Translate source agent files from a specified source provider into a target client's custom agent format, then place them at the target's user-wide location.

## When to Use

- Converting source agent files to a new target client format (from any supported source provider)
- Re-running a conversion after source agents have been updated
- Adding support for a new target client type or a new source provider
- Verifying or repairing a previous conversion

## Procedure

### 1. Detect Environment

Detect whether the source is being run from **Windows** or **Linux/WSL**. Record this as the target environment—target files will be placed at the target's user-wide location for this same environment.

### 2. Resolve Source Provider

Determine which source provider to use. The user may specify a source provider explicitly (e.g., `.claude`, `.opencode`) or omit it.

> **Default source provider** is defined in [generate-target-agents.md](../../../generate-target-agents.md) under "Source provider selection → Default source provider". If the user does not specify a source provider, use that default. Do **not** hardcode the default here — always read it from the instructions file.

Once the source provider is determined:

1. **Check the learned source provider index first.** Look in [generate-target-agents.md](../../../generate-target-agents.md) under "Learned source provider index" for a cached entry matching the provider name. If found, use that cached information (sub-directory, format, fields, conventions) and proceed to step 3.
2. **If no cached entry exists, discover from the source files.** Read the agent files in `.source/agents/<provider>/agents/` to discover the format: parse frontmatter fields, identify required vs optional fields, note naming conventions. Then record the newly discovered provider as a new subsection in the "Learned source provider index" of [generate-target-agents.md](../../../generate-target-agents.md). This prevents re-discovery on future runs.

**If the provider sub-directory does not exist** (e.g., `.source/agents/.foo/agents/`), **stop the run** and report that the specified source provider is not available. List the available providers by enumerating sub-directories under `.source/agents/`.

### 3. Identify Source Files

Read all agent files from the resolved source provider's agent directory (e.g., `.source/agents/.claude/agents/` or `.source/agents/.opencode/agents/`).

Use the format and field definitions from the source provider's cached entry (step 2) to parse each file. Each file will have:

- YAML frontmatter with provider-specific fields (see the source provider index for the field list)
- Markdown body containing the agent's system prompt

If a source file has malformed frontmatter or cannot be parsed, **skip it**, log the error, and continue with the remaining files. Include skipped files and error details in the conversion report.

Reference: [Source agents index](../../../.source/agents/list.md)

### 4. Discover Target Format

Given the target client name or docs URL:

1. **Check the learned target index first.** Look in [generate-target-agents.md](../../../generate-target-agents.md) under "Learned target type index" for a cached entry matching the target. If found, use that cached information and skip to step 5.
2. **If no cached entry exists, discover via web fetch.** Use web fetch tools to locate the target's documentation site **non-interactively** (do not ask the user for URLs). Search for and recursively follow documentation links to find the section on custom agents/sub-agents. Extract:
   - User-wide location (for both Windows and Linux/WSL when documented)
   - File format and extension
   - Valid config fields and which are required vs optional
   - File naming rules and conventions
3. **Cache the discovery.** Record the newly discovered target type as a new subsection in the "Learned target type index" of [generate-target-agents.md](../../../generate-target-agents.md), including the documentation URL, environment, and all extracted details. This prevents re-fetching on future runs.

**If web fetch fails** (rate limited, docs moved, site down), **stop the entire run** — the target format is a hard dependency. Report the failure clearly with the URL attempted and the error received.

### 5. Build Field Mapping

Create a mapping between each source field (from the resolved source provider's format) and the most appropriate target field:

| Source (from provider) | Target | Transformation |
| ---------------------- | ------ | -------------- |
| `name` / filename | *(target's name field)* | *(document rule)* |
| `description` | *(target's description field)* | *(document rule)* |
| `tools` | *(target's tool config)* | *(map tool names/format to target equivalents)* |
| `model` | *(target's model field)* | *(map model identifiers)* |
| Body (markdown) | *(target's prompt/body area)* | *(preserve verbatim)* |
| Provider-specific fields | *(comments/extra metadata)* | *(document preservation strategy)* |

**Note:** Different source providers may have different field names, types, and structures. For example, Claude Code uses an array-style `tools` field while OpenCode uses an object-style `tools` map. The field mapping must account for the source provider's specific conventions.

**CRITICAL: Lossless translation.** All source information must appear in the target output. Where the target format's structure differs from the source (e.g., body content moves into a YAML field like `roleDefinition`), the *information* is preserved even if the *structure* changes. Use these priority tiers:

- **Required (must appear):** `name`/identifier, `description`, body/prompt content.
- **Best-effort (map if target supports):** `tools` (mapped to target equivalents), `model` (mapped to target identifiers).
- **Document-only (preserve as comments or in report):** Fields with no target equivalent (e.g., `permissionMode`, `hooks`, `permission`, `temperature`). Add as comments in the target file or document in the conversion report.

### 6. Generate Target Files

For each source agent file:

1. **Backup first.** Before modifying any existing target files, copy each to a timestamped backup (e.g., `<filename>.bak.20260301T1423`).
2. **Double-buffer edits.** Write to a `.tmp` file first. Only after verification, atomically replace the original (`Move-Item -Force` on Windows).
3. **Verify after swap.** Read back the target file and run these checks:
   - YAML/JSON frontmatter parses without errors
   - All required fields (per target format from step 4) are present
   - Field values satisfy target naming rules (e.g., slug regex, allowed characters)
   - Body/prompt content is non-empty (unless target explicitly allows empty)
   - File extension matches target convention
   If verification fails, restore from backup immediately.

**Re-run behavior:** If target files already exist from a prior conversion, overwrite them unconditionally (the backup in substep 1 protects against data loss). Include a diff summary of what changed in the conversion report.

Place generated files at the target's **user-wide** location for the detected environment.

### 7. Generate Conversion Report

After completion, generate a conversion report directory at `docs/reports/<target>/` (e.g., `docs/reports/kilo-code/`, `docs/reports/factory-droid/`). This allows multiple report files per target if needed (e.g., separate field mapping docs, issue logs, or re-run reports).

The primary report file is always named `conversion-report.md` (e.g., `docs/reports/kilo-code/conversion-report.md`). It should contain:

- Source provider used and its format
- Target client name and date
- Source and target formats with field mapping table
- File list with target locations
- Tool mapping details (including any source→target tool name translations)
- Diff summary of changes (for re-runs over existing files)
- Skipped source files and errors (if any)
- Any other issues encountered and whether they were resolved

Reference existing reports for format: [docs/reports/](../../../docs/reports/)

## Known Indexes

The [generate-target-agents.md](../../../generate-target-agents.md) task guide maintains two caches:

- **Learned source provider index** — caches discovered source provider formats. Step 2 checks this cache first; if a matching entry exists, it is used directly without re-reading source files. If no entry exists, the skill discovers the format from the files and caches it.
- **Learned target type index** — caches discovered target client formats. Step 4 checks this cache first; if a matching entry exists, it is used directly without re-fetching docs. If no entry exists, the skill discovers the format via web fetch and caches it.

### 8. Quality Gate

Run these checks before considering the conversion complete. If any check fails, fix the issue before finishing.

- [ ] Source provider was resolved (explicitly specified or defaulted per instructions file)
- [ ] Every source agent has a corresponding target file (or is logged as skipped with reason)
- [ ] Required fields (`name`/identifier, `description`, body content) are present in every target file
- [ ] Best-effort fields (`tools`, `model`) are mapped where the target supports them
- [ ] Unmappable fields are preserved as comments in target files or documented in the report
- [ ] Target files are at the correct user-wide location for the detected environment
- [ ] Backups exist for any overwritten files
- [ ] Conversion report exists at `docs/reports/<target>/conversion-report.md`
- [ ] Report includes source provider, diff summary (for re-runs), and skipped file errors (if any)
