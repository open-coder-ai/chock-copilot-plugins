---
name: block-unapproved-egress
description: "Best-effort guard on the tool channel: blocks sending data to a host outside the allowlist (.chock/egress-allowlist.txt, else a built-in registry list): curl/wget/iwr uploads, nc/socat/telnet, scp/rsync/sftp, ssh commands, aws/gsutil/az uploads, git push|remote add URLs, $(..)/$VAR in a URL or host. Asks on interpreter HTTP one-liners, gh gist/--body-file, curl --proxy/--resolve/Host:. Blocks ~/.curlrc writes. A floor, not a sandbox: other clients, runtimes, obfuscation."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Block Unapproved Egress

Best-effort guard on the tool channel: blocks sending data to a host outside the allowlist (.chock/egress-allowlist.txt, else a built-in registry list): curl/wget/iwr uploads, nc/socat/telnet, scp/rsync/sftp, ssh commands, aws/gsutil/az uploads, git push|remote add URLs, $(..)/$VAR in a URL or host. Asks on interpreter HTTP one-liners, gh gist/--body-file, curl --proxy/--resolve/Host:. Blocks ~/.curlrc writes. A floor, not a sandbox: other clients, runtimes, obfuscation.

```
block(egress): curl|wget|iwr upload, nc|socat|telnet, scp|rsync|sftp, ssh host cmd, aws s3|gsutil|az blob, git push|remote add <url> to host NOT in .chock/egress-allowlist.txt (default: registries); $(..)|$VAR in URL or hostname; write ~/.curlrc|~/.wgetrc; unusable allowlist file = refuse; curl -K refused
ask: interpreter HTTP one-liner, gh gist|--body-file, curl --proxy|--resolve|Host:. allow: GET, allowlisted host, git push origin; floor_not_sandbox; no pragma passes: ask_person
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
