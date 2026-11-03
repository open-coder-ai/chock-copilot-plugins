---
name: block-secret-store-reads
description: "Best-effort guard against an agent reading credentials from the shell: cat, cp, tar, source or < on ~/.ssh (not .pub), ~/.aws, ~/.npmrc, agent CLI dirs, browser DBs, wallets, .env, *.tfstate; interpreter one-liners naming them; token printers (gh auth token, aws sts); curl, wget or iwr to cloud metadata credential paths (169.254.169.254, 169.254.170.2, GCE). Asks on env dumps. Misses: scripts, unresolved variables, xargs lists, $(...) results, metadata reached by DNS name, proxy or SDK."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Block Reading Stored Credentials

Best-effort guard against an agent reading credentials from the shell: cat, cp, tar, source or < on ~/.ssh (not .pub), ~/.aws, ~/.npmrc, agent CLI dirs, browser DBs, wallets, .env, *.tfstate; interpreter one-liners naming them; token printers (gh auth token, aws sts); curl, wget or iwr to cloud metadata credential paths (169.254.169.254, 169.254.170.2, GCE). Asks on env dumps. Misses: scripts, unresolved variables, xargs lists, $(...) results, metadata reached by DNS name, proxy or SDK.

```
never(read|print): credential_stores(~/.ssh(not_*.pub)|~/.aws|~/.npmrc|~/.netrc|~/.kube|~/.gnupg|agent_cli_dirs|browser_dbs|wallets|.env(not_.env.example)|*.tfstate)|tokens(gh_auth_token|aws_sts|git_credential_fill)|metadata_creds(169.254.169.254|169.254.170.2|fd00:ec2::254|metadata.google.internal), via(cat|grep|sed|cp|tar|base64|source|<|interpreter|curl|wget)
ask: env|printenv|set|export_-p  # dumps every secret; if(value_needed): ask_person_for_that_value
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
