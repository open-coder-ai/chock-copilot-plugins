---
name: verify-mcp-allowlist
description: "Gates MCP servers against .chock/mcp-allowlist.json, empty by default (name, launcher with exact args, or url host). Shell guard refuses `<agent> mcp add` and shell writes of MCP configs or the allowlist unless listed. Script gate (commit, tool use, turn end) reads 13 client configs: a server off the list, or with other command, args or url host, refuses; unpinned or shell launchers, http, literal credentials, old versions only warn. Misses: aliases, scripts, gitignored files, other clients."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Verify MCP Allowlist

Gates MCP servers against .chock/mcp-allowlist.json, empty by default (name, launcher with exact args, or url host). Shell guard refuses `<agent> mcp add` and shell writes of MCP configs or the allowlist unless listed. Script gate (commit, tool use, turn end) reads 13 client configs: a server off the list, or with other command, args or url host, refuses; unpinned or shell launchers, http, literal credentials, old versions only warn. Misses: aliases, scripts, gitignored files, other clients.

```
mcp_server(any client config): must(name+launcher+exact_args+env+headers+cwd+type|url_host in .chock/mcp-allowlist.json); block(unlisted|command_args_url_differ); warn(unpinned|shell_launcher|http|literal_credential|below_floor|enable_all)
allowlist: file, empty by default; an agent is judged by HEAD's copy and cannot grow it, a person edits it; guard: `<agent> mcp add`, shell writes; gate: commit|tool_use|stop
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Its write gate is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
