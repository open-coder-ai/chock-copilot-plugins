---
name: scan-secrets
description: "Blocks known credential patterns -- vendor key prefixes, private-key blocks, and key/token/password assignments -- at two enforcement points: at commit (the git hook, over staged changes) and at agent tool-use (the mcp-gateway / agent write guard, over a tool call's arguments), so a secret is caught as the agent writes it, before it ever reaches a commit. Matched by pattern, not by entropy analysis. Best-effort guard; not a replacement for a dedicated secret scanner."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Scan Secrets

Blocks known credential patterns -- vendor key prefixes, private-key blocks, and key/token/password assignments -- at two enforcement points: at commit (the git hook, over staged changes) and at agent tool-use (the mcp-gateway / agent write guard, over a tool call's arguments), so a secret is caught as the agent writes it, before it ever reaches a commit. Matched by pattern, not by entropy analysis. Best-effort guard; not a replacement for a dedicated secret scanner.

```
on(commit|tool_use): block(content_regex) scan=added_lines forbidden_path_regex=(\.env(\.(?!(sample|example|template|dist|def... ...
Potential secret detected in this change. Remove credentials and rotate any exposed keys. At commit, add '# pragma: allowlist secret' on the same line only for documented test fixtures; the pragma is NOT honored at tool-use, where the scanned text is a live tool argument an appended token could neutralize.
```

This policy is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
