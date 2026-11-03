---
name: block-wildcard-agent-permissions
description: "Blocks agent grants that allow everything: a bare Bash wildcard, a `*` in an allow/alwaysAllow/tools array on the same line, or quoted defaultMode bypassPermissions. Any file is judged. Not caught here: a multi-line array or YAML `- \"*\"` list, an unquoted defaultMode (agent-permissions-scan warns). Runs: commit, agent write, turn's end. Waiver: 'pragma: allowlist broad-agency' same line. Person's commit: honoured. Agent: only if already in HEAD; MCP gateway: never. Agent asks a person."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Block Wildcard Agent Permissions

Blocks agent grants that allow everything: a bare Bash wildcard, a `*` in an allow/alwaysAllow/tools array on the same line, or quoted defaultMode bypassPermissions. Any file is judged. Not caught here: a multi-line array or YAML `- "*"` list, an unquoted defaultMode (agent-permissions-scan warns). Runs: commit, agent write, turn's end. Waiver: 'pragma: allowlist broad-agency' same line. Person's commit: honoured. Agent: only if already in HEAD; MCP gateway: never. Agent asks a person.

```
on(commit|tool_use): block(content_regex) scan=added_lines allowlist_pragma=pragma:\s*allowlist\s+broad-agency content_pattern(regex)
Wildcard agent permission grant detected. Scope the grant to specific tools or commands (e.g. Bash(git status:*), a named tool list); replace defaultMode bypassPermissions with a mode that asks (default or acceptEdits). Waiver: 'pragma: allowlist broad-agency' on the same line. A person's commit honours it; in the agent (write, the turn's end, an agent's commit) only a line already in HEAD counts, and the MCP gateway never does. An agent asks a person; it never writes the pragma.
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
