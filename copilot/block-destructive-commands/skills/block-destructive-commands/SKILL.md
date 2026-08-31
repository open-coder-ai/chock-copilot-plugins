---
name: block-destructive-commands
description: "Best-effort guard against destructive commands: rm -rf targeting absolute, home ($HOME/~) or root-adjacent paths (and the PowerShell Remove-Item -Recurse equivalent); git push --force (not --force-with-lease); git reset --hard; git clean -f; kubectl delete; terraform destroy; aws s3 rm --recursive / rb --force; dropdb; helm uninstall/delete; docker volume rm/prune and system prune; gcloud ... delete. Destructive verbs are matched position-aware, so a bucket, path or object NAMED like a verb (aws s3 cp ... rm, docker volume inspect rm, helm list delete) is allowed. Known bypass classes include aliases, quoted arguments, non-standard clients, and scripts that invoke these commands indirectly. This is friction, not a security boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Destructive Commands

Best-effort guard against destructive commands: rm -rf targeting absolute, home ($HOME/~) or root-adjacent paths (and the PowerShell Remove-Item -Recurse equivalent); git push --force (not --force-with-lease); git reset --hard; git clean -f; kubectl delete; terraform destroy; aws s3 rm --recursive / rb --force; dropdb; helm uninstall/delete; docker volume rm/prune and system prune; gcloud ... delete. Destructive verbs are matched position-aware, so a bucket, path or object NAMED like a verb (aws s3 cp ... rm, docker volume inspect rm, helm list delete) is allowed. Known bypass classes include aliases, quoted arguments, non-standard clients, and scripts that invoke these commands indirectly. This is friction, not a security boundary.

```
block(destructive_command @position-aware): rm_-rf(/|~|$HOME|.)|Remove-Item_-Recurse, git_push_--force, git_reset_--hard, git_checkout_., git_clean_-f, kubectl_delete, terraform_destroy, aws_s3(rm_--recursive|rb_--force), dropdb, helm(uninstall|delete), docker_volume(rm|prune)|system_prune, gcloud_delete
require_approval: reset_hard|rm_-rf|branch_-D; prefer: stash|soft_reset|force-with-lease|dry-run
```

This package ships a PreToolUse hook under com.github.copilot/ that enforces this policy in clients reading that namespace (documented for VS Code agent mode), subject to the fail posture stated in the plugin description. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
