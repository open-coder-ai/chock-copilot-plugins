---
name: block-secret-store-reads
description: "Best-effort guard against an agent reading credentials from the shell: cat, cp, tar, source or < on ~/.ssh (not .pub), ~/.aws, ~/.npmrc, agent CLI dirs, browser DBs, wallets, .env, *.tfstate; interpreter one-liners naming them; token printers (gh auth token, aws sts); curl, wget or iwr to cloud metadata credential paths (169.254.169.254, 169.254.170.2, GCE). Asks on env dumps. Misses: scripts, unresolved variables, xargs lists, $(...) results, metadata reached by DNS name, proxy or SDK."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Reading Stored Credentials

Best-effort guard against an agent reading credentials from the shell: cat, cp, tar, source or < on ~/.ssh (not .pub), ~/.aws, ~/.npmrc, agent CLI dirs, browser DBs, wallets, .env, *.tfstate; interpreter one-liners naming them; token printers (gh auth token, aws sts); curl, wget or iwr to cloud metadata credential paths (169.254.169.254, 169.254.170.2, GCE). Asks on env dumps. Misses: scripts, unresolved variables, xargs lists, $(...) results, metadata reached by DNS name, proxy or SDK.

```
never(read|print): credential_stores(~/.ssh(not_*.pub)|~/.aws|~/.npmrc|~/.netrc|~/.kube|~/.gnupg|agent_cli_dirs|browser_dbs|wallets|.env(not_.env.example)|*.tfstate)|tokens(gh_auth_token|aws_sts|git_credential_fill)|metadata_creds(169.254.169.254|169.254.170.2|fd00:ec2::254|metadata.google.internal), via(cat|grep|sed|cp|tar|base64|source|<|interpreter|curl|wget)
ask: env|printenv|set|export_-p  # dumps every secret; if(value_needed): ask_person_for_that_value
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
