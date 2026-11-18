---
name: dockerfile-compose-security
description: "Blocks (some rules ask) when a change to a Dockerfile, Containerfile or compose file adds: a base image with no tag, a floating tag (latest, stable, edge, lts) or an unresolvable ARG; a final stage running as root; TLS or signature checks off; secret ENV/ARG/environment literals; COPY of key or .env files; remote ADD without checksum; fetch piped to a shell; RUN --security=insecure; chmod 777 or setuid; sudo, sshd, chpasswd; unpinned git clone; ONBUILD RUN; compose privileged, broad cap_add, unconfined profiles, devices, host namespaces, runtime socket or host-root mounts, DB ports on every interface. A one-line form goes to block-fetch-exec-in-files, block-unpinned-agent-components or agentic-code-security only where that gate is installed and reads the file; scan-secrets lines always. Misses: digest pinning (a version tag passes), build args overriding defaults, commands in variables, aliases or $'' quoting, bake and k8s files, extends or include, other file names, a moved file. Friction, not a boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Dockerfile and Compose Security

Blocks (some rules ask) when a change to a Dockerfile, Containerfile or compose file adds: a base image with no tag, a floating tag (latest, stable, edge, lts) or an unresolvable ARG; a final stage running as root; TLS or signature checks off; secret ENV/ARG/environment literals; COPY of key or .env files; remote ADD without checksum; fetch piped to a shell; RUN --security=insecure; chmod 777 or setuid; sudo, sshd, chpasswd; unpinned git clone; ONBUILD RUN; compose privileged, broad cap_add, unconfined profiles, devices, host namespaces, runtime socket or host-root mounts, DB ports on every interface. A one-line form goes to block-fetch-exec-in-files, block-unpinned-agent-components or agentic-code-security only where that gate is installed and reads the file; scan-secrets lines always. Misses: digest pinning (a version tag passes), build args overriding defaults, commands in variables, aliases or $'' quoting, bake and k8s files, extends or include, other file names, a moved file. Friction, not a boundary.

```
flag(container_change): Dockerfile|Containerfile|compose adds floating image (no tag, latest, stable, edge), root final stage, TLS|signature off, literal secret, key-file COPY, privileged|cap_add|host ns|docker.sock|host-root mount, db port on 0.0.0.0
prefer: pinned tag@sha256, USER <uid>, RUN --mount=type=secret, cap_add narrow, named volumes; waiver: '# chock: allow <rule-id>', person only
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
