---
name: block-no-verify
description: "Friction, not a security boundary: refuses agent commands that skip git hooks. --no-verify on commit/push/merge/am/rebase/pull/cherry-pick/revert, -n on commit/am; core.hooksPath set by -c, git config or GIT_CONFIG_*; HUSKY=0, SKIP=, LEFTHOOK=0 and kin; hook manager uninstall; aliases and rebase --exec defined in the command. Asks on plumbing, unset hooksPath, another GIT_DIR and config files. Refuses person-only CHOCK_*/marker changes. Mandatory. Misses: older aliases, scripts, $VARs."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Block Skipping Git Hooks

Friction, not a security boundary: refuses agent commands that skip git hooks. --no-verify on commit/push/merge/am/rebase/pull/cherry-pick/revert, -n on commit/am; core.hooksPath set by -c, git config or GIT_CONFIG_*; HUSKY=0, SKIP=, LEFTHOOK=0 and kin; hook manager uninstall; aliases and rebase --exec defined in the command. Asks on plumbing, unset hooksPath, another GIT_DIR and config files. Refuses person-only CHOCK_*/marker changes. Mandatory. Misses: older aliases, scripts, $VARs.

```
never(commit|push|merge|am|rebase|pull|cherry-pick|revert): --no-verify|-n(commit|am); never(set): core.hooksPath|HUSKY=0|SKIP=|LEFTHOOK=0|kin; never: pre-commit|lefthook|husky uninstall; ask: commit-tree|update-ref|GIT_DIR|config files
never(agent_set|unset): CHOCK_ALLOW*|CHOCK_AGENT_COMMIT|CHOCK_DIFF_LIMIT|CLAUDECODE|AI_AGENT; if(hook_fails|override_needed): fix_issue|ask_person
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
