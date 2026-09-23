---
name: block-wildcard-agent-permissions
description: "The mechanizable slice of excessive agency, enforced at two points: at commit (the git hook, over staged changes) and at agent tool-use (over a tool call's arguments, as the agent writes) -- agent permission grants that allow everything. A settings file whose shell grant or allow-list is a bare wildcard hands the agent unlimited tool authority for every future session, in a file reviewers rarely read as code. The agent-world twin of block-wildcard-iam: scope grants to what the task needs (e.g. Bash(git status:*)). The allow/alwaysAllow/tools/defaultMode keys match whether or not YAML-style config quotes them, word-bounded so \"disallow\"/\"allowlist\" are not mistaken for \"allow\". Escape: 'pragma: allowlist broad-agency' on the same line."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Wildcard Agent Permissions

The mechanizable slice of excessive agency, enforced at two points: at commit (the git hook, over staged changes) and at agent tool-use (over a tool call's arguments, as the agent writes) -- agent permission grants that allow everything. A settings file whose shell grant or allow-list is a bare wildcard hands the agent unlimited tool authority for every future session, in a file reviewers rarely read as code. The agent-world twin of block-wildcard-iam: scope grants to what the task needs (e.g. Bash(git status:*)). The allow/alwaysAllow/tools/defaultMode keys match whether or not YAML-style config quotes them, word-bounded so "disallow"/"allowlist" are not mistaken for "allow". Escape: 'pragma: allowlist broad-agency' on the same line.

```
on(commit|tool_use): block(content_regex) scan=added_lines allowlist_pragma=pragma:\s*allowlist\s+broad-agency ...
Wildcard agent permission grant detected. Scope the grant to specific tools or commands (e.g. Bash(git status:*), a named tool list). At commit, 'pragma: allowlist broad-agency' on the same line marks a reviewed exception; the pragma is NOT honored at tool-use, where the scanned text is a live tool argument an appended token could neutralize.
```

This policy is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
