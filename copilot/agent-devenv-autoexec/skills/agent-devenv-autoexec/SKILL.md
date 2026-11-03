---
name: agent-devenv-autoexec
description: "Warns only (observe): flags files that make a dev tool run code on open, clone or shell entry -- agent hooks, helpers, env/BASE_URL overrides, auto-approve; VS Code folderOpen tasks, trust off, repo executables; devcontainer initializeCommand; .envrc, mise, husky, lefthook, pre-commit; gitconfig exec keys, gitattributes drivers, unsafe .gitmodules. Commit, agent write, turn's end; additions only; unreadable configs refused. Friction: misses interpreted code and unlisted files."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Flag Dev Tools That Run Code on Open

Warns only (observe): flags files that make a dev tool run code on open, clone or shell entry -- agent hooks, helpers, env/BASE_URL overrides, auto-approve; VS Code folderOpen tasks, trust off, repo executables; devcontainer initializeCommand; .envrc, mise, husky, lefthook, pre-commit; gitconfig exec keys, gitattributes drivers, unsafe .gitmodules. Commit, agent write, turn's end; additions only; unreadable configs refused. Friction: misses interpreted code and unlisted files.

```
devenv_autoexec(agent hooks|helpers|env overrides|auto-approve|folderOpen tasks|trust off|devcontainer lifecycle|.envrc|mise|git hooks|gitconfig exec|gitattributes drivers|.gitmodules): changed by people only
never(add): hook|task|helper|env override|auto-approval; observe: warns at commit+tool_use, enforce later
```

This policy warns, and does not block, through the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
