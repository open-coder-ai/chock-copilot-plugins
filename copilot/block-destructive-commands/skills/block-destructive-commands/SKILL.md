---
name: block-destructive-commands
description: "Best-effort guard against destructive commands: rm -rf targeting absolute, home, or root-adjacent paths; git push --force (not --force-with-lease); git reset --hard; git clean -f; kubectl delete; terraform destroy. Known bypass classes include aliases, quoted arguments, non-standard clients, and scripts that invoke these commands indirectly. This is friction, not a security boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Destructive Commands

Best-effort guard against destructive commands: rm -rf targeting absolute, home, or root-adjacent paths; git push --force (not --force-with-lease); git reset --hard; git clean -f; kubectl delete; terraform destroy. Known bypass classes include aliases, quoted arguments, non-standard clients, and scripts that invoke these commands indirectly. This is friction, not a security boundary.

```
block(destructive_command): rm_-rf(/|~|.), git_push_--force, git_reset_--hard, git_checkout_., git_clean_-f, kubectl_delete, terraform_destroy
require_approval: reset_hard|rm_-rf|branch_-D; prefer: stash|soft_reset|force-with-lease|dry-run
```

This package ships a PreToolUse hook under com.github.copilot/ that enforces this policy in clients reading that namespace (documented for VS Code agent mode), subject to the fail posture stated in the plugin description. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
