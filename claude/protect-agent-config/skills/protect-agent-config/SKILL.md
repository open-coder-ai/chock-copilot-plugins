---
name: protect-agent-config
description: "Guard against an agent hand-editing its own guardrails. Agent instruction files (AGENTS.md and the per-agent wrappers), permission files (.claude/settings.json, .mcp.json) and vendored enforcement (.chock/bin/, .chock/compiled/) define what the agent may do -- so a shell command that rewrites them is the agent modifying its own authority (MITRE ATLAS AML.T0081; the AIVSS self-modification factor). The guard refuses shell write-commands targeting those paths; reads pass, and regeneration through `chock sync` passes because the tool writes them itself rather than through shell editing. Best-effort and deliberately coarse: a compound command that both reads a protected file and writes elsewhere may be refused -- rewrite it in two steps. The 'chock: approved-config-change' escape marker is friction plus an audit trail, not authentication -- the agent can write it too; the check an agent cannot self-approve is the commit-time gate and CI."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Protect Agent Config

Guard against an agent hand-editing its own guardrails. Agent instruction files (AGENTS.md and the per-agent wrappers), permission files (.claude/settings.json, .mcp.json) and vendored enforcement (.chock/bin/, .chock/compiled/) define what the agent may do -- so a shell command that rewrites them is the agent modifying its own authority (MITRE ATLAS AML.T0081; the AIVSS self-modification factor). The guard refuses shell write-commands targeting those paths; reads pass, and regeneration through `chock sync` passes because the tool writes them itself rather than through shell editing. Best-effort and deliberately coarse: a compound command that both reads a protected file and writes elsewhere may be refused -- rewrite it in two steps. The 'chock: approved-config-change' escape marker is friction plus an audit trail, not authentication -- the agent can write it too; the check an agent cannot self-approve is the commit-time gate and CI.

```
agent_config(AGENTS.md|wrappers|.claude/settings|.mcp.json|.chock/bin|.chock/compiled): never(hand_edit|delete); regenerate_via(chock sync)
if(config_change_needed): propose_to_human; await(approval)  # an agent must not widen or disarm its own guardrails
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail-open condition stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
