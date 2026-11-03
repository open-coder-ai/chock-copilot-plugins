---
name: refname-filename-metachar
description: "Refuses names a shell, CI step or git can misread. Paths a change adds or renames into (commit, agent writes), and branches and tags pushed (pre-push), with command substitution, an IFS expansion, a backtick, ; & | < >, a double quote, a backslash, a control or bidi character, a base64-decode shape, a leading dash or trailing space or dot in a segment, or a '..' segment; a ref also with a full commit id's shape. A guard refuses git and file commands creating such names. No waiver. Friction, not a boundary."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Risky Characters in File and Branch Names

Refuses names a shell, CI step or git can misread. Paths a change adds or renames into (commit, agent writes), and branches and tags pushed (pre-push), with command substitution, an IFS expansion, a backtick, ; & | < >, a double quote, a backslash, a control or bidi character, a base64-decode shape, a leading dash or trailing space or dot in a segment, or a '..' segment; a ref also with a full commit id's shape. A guard refuses git and file commands creating such names. No waiver. Friction, not a boundary.

```
on(commit|tool_use): block(script) script=refname-filename-metachar-gate.py
A path this change adds or renames into has a name a shell, a CI step or git can misread: command substitution, an IFS expansion, a backtick, a shell operator, a double quote, a backslash, a control or bidi character, a base64-decode shape, a segment starting with '-' or ending in a space or '.', or a '..' segment. Rename it with letters, digits, spaces inside, '.', '_', '-' and '/'. No waiver: if the name is truly required, a person creates it from their own shell.
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Its write gate is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
