---
name: block-curl-pipe-sh
description: "Best-effort guard: refuses a download (curl, wget, iwr; ssh host cmd, scp/rsync/aws s3 cp to stdout) wired into a shell or interpreter (sh..fish, python, perl, ruby, node, php, lua, pwsh, deno, bun, su, source, eval, iex) by pipe, substitution or here-string, also inside bash -c/ssh/docker exec bodies, or downloaded then run with no checksum or signature step. nc/socat/one-liner reads ask. Misses: aliases, functions, outside variables, encoded text, renamed downloads. Friction only."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Curl Piped to Shell

Best-effort guard: refuses a download (curl, wget, iwr; ssh host cmd, scp/rsync/aws s3 cp to stdout) wired into a shell or interpreter (sh..fish, python, perl, ruby, node, php, lua, pwsh, deno, bun, su, source, eval, iex) by pipe, substitution or here-string, also inside bash -c/ssh/docker exec bodies, or downloaded then run with no checksum or signature step. nc/socat/one-liner reads ask. Misses: aliases, functions, outside variables, encoded text, renamed downloads. Friction only.

```
block(remote_exec): fetch(curl|wget|iwr|irm|ssh_host_cmd|scp|rsync|aws_s3_cp to_stdout) piped|substituted|here-string|run-after-download into interpreter(sh|bash|python|php|pwsh|source|eval|iex), quoted bodies too; ask: nc|socat|net one-liner
allow: download_to_file, fetch|non_interpreter(jq|tar|gpg), download+verify(sha256sum -c|gpg --verify)+run; prefer: curl -o file; read; verify; run
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
