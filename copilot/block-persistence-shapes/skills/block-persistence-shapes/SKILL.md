---
name: block-persistence-shapes
description: "Best-effort guard against shell commands that publish or keep access after the session: npm/pnpm/yarn/twine/ poetry/uv/cargo/gem publish, docker/podman push and login, npm token and registry edits, repos made public; user services, launch agents, cron/at/schtasks, Run keys, authorized_keys, runner registration, sudoers, setuid bits, detached downloads. Asks on gh release create, git remote add, git push to a URL. Misses: scripts, aliases, system units, shell rc files, ssh-run commands."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Persistence and Publishing Commands

Best-effort guard against shell commands that publish or keep access after the session: npm/pnpm/yarn/twine/ poetry/uv/cargo/gem publish, docker/podman push and login, npm token and registry edits, repos made public; user services, launch agents, cron/at/schtasks, Run keys, authorized_keys, runner registration, sudoers, setuid bits, detached downloads. Asks on gh release create, git remote add, git push to a URL. Misses: scripts, aliases, system units, shell rc files, ssh-run commands.

```
never(run): publish(npm|pnpm|yarn|twine|poetry|uv|cargo|gem|docker|podman), registry_login_or_token, repo_public, persist(user_service|launch_agent|cron|at|schtasks|run_key|authorized_keys|runner|sudoers|setuid|detached_download)
ask(person): release_create, remote_add, push_to_url; allow: dry_run, pack, build, status, list  # human decisions
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
