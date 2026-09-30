---
name: verify-dependency-exists
description: "Block hallucinated or unknown dependencies before they enter the repo. Watches requirements.txt, pyproject.toml, package.json, and go.mod, and blocks any newly added dependency not present in the allowlist file. Opt-in: disabled by default because it requires a curated allowlist. Enable with `chock enable verify-dependency-exists` after populating .chock/dependency-allowlist.txt. Runs at commit and at agent tool-use (a manifest edit is judged against the file on disk, and at the turn's end against HEAD)."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Verify Dependency Exists

Block hallucinated or unknown dependencies before they enter the repo. Watches requirements.txt, pyproject.toml, package.json, and go.mod, and blocks any newly added dependency not present in the allowlist file. Opt-in: disabled by default because it requires a curated allowlist. Enable with `chock enable verify-dependency-exists` after populating .chock/dependency-allowlist.txt. Runs at commit and at agent tool-use (a manifest edit is judged against the file on disk, and at the turn's end against HEAD).

```
on(commit|tool_use): block(dependency_allowlist) manifests=requirements.txt|pyproject.toml|package.json|... allowlist_file=.chock/dependency-allowlist.txt
Unknown dependency blocked. Verify the package exists in the official registry, then ask a person to add it to .chock/dependency-allowlist.txt -- an agent may not edit that file (protect-agent-config).
```

This policy is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
