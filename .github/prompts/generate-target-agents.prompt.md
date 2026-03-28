---
name: generate-target-agents
description: Generate target client agent files from source agents using the project plan.
argument-hint: sourceProvider=<provider> targetClient=<client name>
agent: agent
---

Run the plan in [generate-target-agents.md](../../generate-target-agents.md).

Source provider: ${input:sourceProvider:Source provider (e.g. .claude, .opencode) — leave blank for default}
Target client type: ${input:targetClient:Target client type}

Use the source provider and target client type as arguments when executing the plan.
If no source provider is specified, use the default defined in the plan.
