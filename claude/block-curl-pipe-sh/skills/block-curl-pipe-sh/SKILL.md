---
name: block-curl-pipe-sh
description: "Best-effort guard: refuses a download (curl, wget, iwr; ssh host cmd, scp/rsync/aws s3 cp to stdout) wired into a shell or interpreter (sh..fish, python, perl, ruby, node, php, lua, pwsh, deno, bun, su, source, eval, iex) by pipe, substitution or here-string, also inside bash -c/ssh/docker exec bodies, or downloaded then run with no checksum or signature step. nc/socat/one-liner reads ask. Misses: aliases, functions, outside variables, encoded text, renamed downloads. Friction only."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Block Curl Piped to Shell

Best-effort guard: refuses a download (curl, wget, iwr; ssh host cmd, scp/rsync/aws s3 cp to stdout) wired into a shell or interpreter (sh..fish, python, perl, ruby, node, php, lua, pwsh, deno, bun, su, source, eval, iex) by pipe, substitution or here-string, also inside bash -c/ssh/docker exec bodies, or downloaded then run with no checksum or signature step. nc/socat/one-liner reads ask. Misses: aliases, functions, outside variables, encoded text, renamed downloads. Friction only.

```
block(remote_exec): fetch(curl|wget|iwr|irm|ssh_host_cmd|scp|rsync|aws_s3_cp to_stdout) piped|substituted|here-string|run-after-download into interpreter(sh|bash|python|php|pwsh|source|eval|iex), quoted bodies too; ask: nc|socat|net one-liner
allow: download_to_file, fetch|non_interpreter(jq|tar|gpg), download+verify(sha256sum -c|gpg --verify)+run; prefer: curl -o file; read; verify; run
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
