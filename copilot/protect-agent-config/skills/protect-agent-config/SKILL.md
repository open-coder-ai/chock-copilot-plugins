---
name: protect-agent-config
description: "Guard against an agent hand-editing its own guardrails. Agent instruction files (AGENTS.md and the per-agent wrappers), permission files (.claude/settings.json, .mcp.json) and vendored enforcement (.chock/bin/, .chock/compiled/) define what the agent may do -- so a shell command that rewrites them is the agent modifying its own authority (MITRE ATLAS AML.T0081). The guard refuses shell write-commands targeting those paths; reads pass, and regeneration through `chock sync` passes because the tool writes them itself. Best-effort and deliberately coarse: a write signal (a `>`/`>>` redirect, or a writer verb like rm/cp/sed -i) is scoped to its own clause of the command line, so it must actually target the protected path, not merely appear alongside it; a glued-on separator or a non-redirect writer's own operand are not resolved that finely. The 'chock: approved-config-change' marker is friction plus an audit trail, not authentication; the check an agent cannot self-approve is the commit-time gate and CI."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Agent Config

Guard against an agent hand-editing its own guardrails. Agent instruction files (AGENTS.md and the per-agent wrappers), permission files (.claude/settings.json, .mcp.json) and vendored enforcement (.chock/bin/, .chock/compiled/) define what the agent may do -- so a shell command that rewrites them is the agent modifying its own authority (MITRE ATLAS AML.T0081). The guard refuses shell write-commands targeting those paths; reads pass, and regeneration through `chock sync` passes because the tool writes them itself. Best-effort and deliberately coarse: a write signal (a `>`/`>>` redirect, or a writer verb like rm/cp/sed -i) is scoped to its own clause of the command line, so it must actually target the protected path, not merely appear alongside it; a glued-on separator or a non-redirect writer's own operand are not resolved that finely. The 'chock: approved-config-change' marker is friction plus an audit trail, not authentication; the check an agent cannot self-approve is the commit-time gate and CI.

```
agent_config(AGENTS.md|wrappers|.claude/settings|.mcp.json|.chock/bin|.chock/compiled|.agents/policies/*/implementations): never(hand_edit|delete); regenerate_via(chock sync)
if(config_change_needed): propose_to_human; await(approval)  # an agent must not widen or disarm its own guardrails
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives; a client that exports no plugin-root variable runs the hook, which then allows -- so treat this package as advisory unless a deny has been witnessed in your own client. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
