---
name: agent-permissions-scan
description: "Blocks: parses agent permission configs (.claude/settings*, .codex, .gemini, .vscode, .cursor/cli.json, opencode, .aider, .continue) and flags added bare or wildcard allows (Bash, curl/rm/sudo/git push, WebFetch, Write/Edit globs, mcp__*), removed deny entries, bypass/auto modes, codex never+danger, yolo, autoAccept, yes-always, regex-all VS Code approve, Claude project env API-endpoint overrides and enableAllProjectMcpServers. Misses: other MCP configs, user-level settings, scripts, Read."
metadata:
  chock.artifact: rule
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Scan Agent Permission Settings

Blocks: parses agent permission configs (.claude/settings*, .codex, .gemini, .vscode, .cursor/cli.json, opencode, .aider, .continue) and flags added bare or wildcard allows (Bash, curl/rm/sudo/git push, WebFetch, Write/Edit globs, mcp__*), removed deny entries, bypass/auto modes, codex never+danger, yolo, autoAccept, yes-always, regex-all VS Code approve, Claude project env API-endpoint overrides and enableAllProjectMcpServers. Misses: other MCP configs, user-level settings, scripts, Read.

```
agent_permissions(.claude/settings*|.codex/config.toml|.gemini/settings.json|.vscode/settings.json|.cursor/cli.json|opencode.json|.aider.conf.yml|.continue/**): grant named, scoped actions only
never(add): bare|wildcard allow, bypass|auto default mode, yolo|autoAccept|yes-always, regex-all auto-approve, claude env ANTHROPIC_*_URL|OPENAI_BASE_URL|*_API_BASE, enableAllProjectMcpServers; never(remove): deny entry; blocks at commit+tool_use
```

This policy is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
