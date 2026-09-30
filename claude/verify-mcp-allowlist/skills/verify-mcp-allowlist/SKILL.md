---
name: verify-mcp-allowlist
description: "Gate MCP server configuration as protected content. A shell write to .mcp.json is refused unless every mcpServers entry on the line, parsed as JSON, matches a name+source pair on the allowlist -- an unlisted name blocks, and an allowed name whose command/args/url changed blocks too. `claude mcp add|add-json` is checked the same way (add-from-claude-desktop cannot be verified and is refused). The allowlist ships inside this guard's own script, protected like every policy's implementations/ source -- edit only with 'chock: approved-config-change'. The shell guard reads Claude Code's .mcp.json, best-effort (PreToolUse fails open on a crash; a write with no visible content fails closed). A script gate at commit and tool use parses every written MCP config (.mcp.json, .cursor/mcp.json, .vscode/mcp.json, claude_desktop_config.json, .gemini/settings.json, .codex/config.toml) against the same allowlist; only servers the change adds or alters are refused, at commit and tool use. No pragma."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Verify MCP Allowlist

Gate MCP server configuration as protected content. A shell write to .mcp.json is refused unless every mcpServers entry on the line, parsed as JSON, matches a name+source pair on the allowlist -- an unlisted name blocks, and an allowed name whose command/args/url changed blocks too. `claude mcp add|add-json` is checked the same way (add-from-claude-desktop cannot be verified and is refused). The allowlist ships inside this guard's own script, protected like every policy's implementations/ source -- edit only with 'chock: approved-config-change'. The shell guard reads Claude Code's .mcp.json, best-effort (PreToolUse fails open on a crash; a write with no visible content fails closed). A script gate at commit and tool use parses every written MCP config (.mcp.json, .cursor/mcp.json, .vscode/mcp.json, claude_desktop_config.json, .gemini/settings.json, .codex/config.toml) against the same allowlist; only servers the change adds or alters are refused, at commit and tool use. No pragma.

```
mcp_config(.mcp.json): server(name,source=cmd+args|url) must(match: allowlist(this_guard_source)); block(unlisted|source_mismatch); allow(exact_match)
allowlist: lives in implementations/verify-mcp-allowlist.py; also gates `claude mcp add|add-json`; edit requires 'chock: approved-config-change'; also gates written configs: .mcp.json|.cursor|.vscode|claude_desktop|.gemini|.codex added-only at commit+tool_use
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
