---
name: verify-mcp-allowlist
description: "Gates MCP servers against .chock/mcp-allowlist.json, empty by default (name, launcher with exact args, or url host). Shell guard refuses `<agent> mcp add` and shell writes of MCP configs or the allowlist unless listed. Script gate (commit, tool use, turn end) reads 13 client configs: a server off the list, or with other command, args or url host, refuses; unpinned or shell launchers, http, literal credentials, old versions only warn. Misses: aliases, scripts, gitignored files, other clients."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Verify MCP Allowlist

Gates MCP servers against .chock/mcp-allowlist.json, empty by default (name, launcher with exact args, or url host). Shell guard refuses `<agent> mcp add` and shell writes of MCP configs or the allowlist unless listed. Script gate (commit, tool use, turn end) reads 13 client configs: a server off the list, or with other command, args or url host, refuses; unpinned or shell launchers, http, literal credentials, old versions only warn. Misses: aliases, scripts, gitignored files, other clients.

```
mcp_server(any client config): must(name+launcher+exact_args+env+headers+cwd+type|url_host in .chock/mcp-allowlist.json); block(unlisted|command_args_url_differ); warn(unpinned|shell_launcher|http|literal_credential|below_floor|enable_all)
allowlist: file, empty by default; an agent is judged by HEAD's copy and cannot grow it, a person edits it; guard: `<agent> mcp add`, shell writes; gate: commit|tool_use|stop
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Its write gate is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
