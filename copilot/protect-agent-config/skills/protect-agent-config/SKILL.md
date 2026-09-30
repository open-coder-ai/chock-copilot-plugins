---
name: protect-agent-config
description: "Guard against an agent hand-editing its own guardrails. Agent instruction files (AGENTS.md and the per-agent wrappers), permission files (.claude/settings.json, .mcp.json), the dependency allowlist (.chock/dependency-allowlist.txt) and vendored enforcement (.chock/bin/, .chock/compiled/) define what the agent may do -- so a shell command that rewrites them is the agent modifying its own authority (MITRE ATLAS AML.T0081). The guard refuses shell writes to those paths -- a redirect, rm/mv/tee/sed -i, cp into the path, git checkout/restore, PowerShell Set-Content/Add-Content/Out-File; reads and copies out pass, and `chock sync` passes. Best-effort and deliberately coarse. The 'chock: approved-config-change' marker is friction plus an audit trail, not authentication. A second, tool_use-only gate refuses Edit/Write to the same paths; it never runs at commit, so a person stays free to edit them."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Agent Config

Guard against an agent hand-editing its own guardrails. Agent instruction files (AGENTS.md and the per-agent wrappers), permission files (.claude/settings.json, .mcp.json), the dependency allowlist (.chock/dependency-allowlist.txt) and vendored enforcement (.chock/bin/, .chock/compiled/) define what the agent may do -- so a shell command that rewrites them is the agent modifying its own authority (MITRE ATLAS AML.T0081). The guard refuses shell writes to those paths -- a redirect, rm/mv/tee/sed -i, cp into the path, git checkout/restore, PowerShell Set-Content/Add-Content/Out-File; reads and copies out pass, and `chock sync` passes. Best-effort and deliberately coarse. The 'chock: approved-config-change' marker is friction plus an audit trail, not authentication. A second, tool_use-only gate refuses Edit/Write to the same paths; it never runs at commit, so a person stays free to edit them.

```
agent_config(AGENTS.md|wrappers|.claude/settings|.mcp.json|.chock/dependency-allowlist.txt|.chock/bin|.chock/compiled|.agents/policies/*/implementations): never(hand_edit|delete); regenerate_via(chock sync)
if(config_change_needed): propose_to_human; await(approval)  # an agent must not widen or disarm its own guardrails
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives; a client that exports no plugin-root variable runs the hook, which then allows -- so treat this package as advisory unless a deny has been witnessed in your own client. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
