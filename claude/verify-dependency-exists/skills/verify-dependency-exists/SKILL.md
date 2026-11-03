---
name: verify-dependency-exists
description: "Allowlist gate for new dependencies; no registry lookup. Reads Python, Node, Rust, Ruby, PHP, JVM, .NET, Go, Elixir, Dart and Swift manifests (read, never run); blocks an added name missing from .chock/dependency-allowlist.txt, asks on a lockfile-only one. Not read: computed lists, sources, versions, some forms (see docs). No allowlist file (plugin-only installs): a manifest change warns, never blocks. Repo form is opt-in: seed, observe, `chock enable`. Runs: commit, agent write, turn's end."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Dependency Allowlist

Allowlist gate for new dependencies; no registry lookup. Reads Python, Node, Rust, Ruby, PHP, JVM, .NET, Go, Elixir, Dart and Swift manifests (read, never run); blocks an added name missing from .chock/dependency-allowlist.txt, asks on a lockfile-only one. Not read: computed lists, sources, versions, some forms (see docs). No allowlist file (plugin-only installs): a manifest change warns, never blocks. Repo form is opt-in: seed, observe, `chock enable`. Runs: commit, agent write, turn's end.

```
on(commit|tool_use): block(script) script=dependency-manifests.py
Unknown dependency blocked: it is not in .chock/dependency-allowlist.txt. This gate checks the allowlist only and makes no registry lookup, so confirm the package exists in its official registry and is the intended one first. If you are an agent, ask a person to add the name; do not edit that file yourself (protect-agent-config). If you are a person, add the name to the file.
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
