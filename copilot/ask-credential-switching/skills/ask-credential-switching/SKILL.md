---
name: ask-credential-switching
description: "Best effort, ask only. Asks a person before a shell command switches the identity later commands act as: aws --profile, AWS_PROFILE= or AWS keys (also $env:), sts assume-role, configure set of a key; gcloud account, configuration, login or impersonation; az account set, az login as a principal or identity; kubectl use-context or set-credentials; writes to ~/.aws, ~/.config/gcloud, ~/.azure. Names in .chock/credential-allowlist.json pass. Misses: scripts, SDKs, env files, credential_process."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Review Cloud Identity Switches

Best effort, ask only. Asks a person before a shell command switches the identity later commands act as: aws --profile, AWS_PROFILE= or AWS keys (also $env:), sts assume-role, configure set of a key; gcloud account, configuration, login or impersonation; az account set, az login as a principal or identity; kubectl use-context or set-credentials; writes to ~/.aws, ~/.config/gcloud, ~/.azure. Names in .chock/credential-allowlist.json pass. Misses: scripts, SDKs, env files, credential_process.

```
ask_person: switch_identity(aws(--profile|AWS_PROFILE=|AWS_*KEY*=|$env:|sts_assume-role|configure_set_cred)|gcloud(set_account|configurations_activate|auth_login|impersonate)|az(account_set|login_principal|login_identity)|kubectl(use-context|set-credentials)|write(~/.aws|~/.config/gcloud|~/.azure)); say(identity), ask(intended?)
pass: reads(configure_list|config_list|account_show), names_in(.chock/credential-allowlist.json@HEAD)  # person-only, own commit
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
