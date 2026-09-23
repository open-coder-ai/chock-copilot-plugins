---
name: block-unapproved-egress
description: "Best-effort guard against exfiltration through the tool channel: a network command (curl/wget/Invoke-WebRequest) that UPLOADS data -- POST/PUT, --data/--form, --upload-file, --post-file -- to a host outside the egress allowlist. The allowlist defaults to package registries and code hosting and is meant to be extended with your org's own domains; a host matches by exact name or \".<entry>\" suffix. Fetch-only traffic is left alone -- the target is upload to an unapproved host, not normal dependency traffic. A schemeless or protocol-relative target is checked too, from the last non-flag token, but only when no explicit http(s):// URL appears anywhere -- once one has, it alone decides. Tool-time FLOOR, not a network sandbox: it stops the obvious reflex, not a determined adversary. Known bypasses: ~/.curlrc, combined short flags, obfuscated payloads, non-standard clients, a language runtime. Escape: 'pragma: allowlist egress'."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Block Unapproved Egress

Best-effort guard against exfiltration through the tool channel: a network command (curl/wget/Invoke-WebRequest) that UPLOADS data -- POST/PUT, --data/--form, --upload-file, --post-file -- to a host outside the egress allowlist. The allowlist defaults to package registries and code hosting and is meant to be extended with your org's own domains; a host matches by exact name or ".<entry>" suffix. Fetch-only traffic is left alone -- the target is upload to an unapproved host, not normal dependency traffic. A schemeless or protocol-relative target is checked too, from the last non-flag token, but only when no explicit http(s):// URL appears anywhere -- once one has, it alone decides. Tool-time FLOOR, not a network sandbox: it stops the obvious reflex, not a determined adversary. Known bypasses: ~/.curlrc, combined short flags, obfuscated payloads, non-standard clients, a language runtime. Escape: 'pragma: allowlist egress'.

```
block(egress): fetch(curl|wget|iwr) + upload(-d|--data|-F|--upload-file|-X POST|PUT) to host NOT in allowlist
allow: fetch_only(GET), allowlisted_host(github|pypi|npm|...); floor_not_sandbox; escape: 'pragma: allowlist egress'
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
