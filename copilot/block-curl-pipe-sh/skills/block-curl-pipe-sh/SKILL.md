---
name: block-curl-pipe-sh
description: "Best-effort guard against piping a network download straight into a shell or script interpreter: curl|wget|iwr ... | sh/bash/zsh/python/perl/ruby/node (bare or path-qualified, including subshell groups and sudo/exec/command/env wrappers), bash -c \"$(curl ...)\", bash <(curl ...), and the PowerShell iwr ... | iex form. Downloading to a file, or piping a fetch into a non-interpreter tool (jq, tar, grep), stays allowed. Known bypass classes include aliases, variable indirection, base64/obfuscated payloads, env-var-prefixed interpreters, and non-standard fetch clients. This is friction, not a security boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Curl-Pipe-Shell

Best-effort guard against piping a network download straight into a shell or script interpreter: curl|wget|iwr ... | sh/bash/zsh/python/perl/ruby/node (bare or path-qualified, including subshell groups and sudo/exec/command/env wrappers), bash -c "$(curl ...)", bash <(curl ...), and the PowerShell iwr ... | iex form. Downloading to a file, or piping a fetch into a non-interpreter tool (jq, tar, grep), stays allowed. Known bypass classes include aliases, variable indirection, base64/obfuscated payloads, env-var-prefixed interpreters, and non-standard fetch clients. This is friction, not a security boundary.

```
block(remote_exec): fetch(curl|wget|iwr|irm) piped/substituted into interpreter(sh|bash|python|perl|node|iex)
allow: download_to_file, fetch|non_interpreter(jq|tar); prefer: curl -o file; read; run
```

This package ships a PreToolUse hook under com.github.copilot/ that enforces this policy in clients reading that namespace (documented for VS Code agent mode), subject to the fail posture stated in the plugin description. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
