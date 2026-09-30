---
name: block-destructive-commands
description: "Best-effort guard against destructive commands, read as parsed commands (bash -c and cd chains included, echo excluded): rm -rf on absolute, home ($HOME/~) or root-adjacent paths (and PowerShell Remove-Item -Recurse); git push --force (not --force-with-lease), reset --hard, clean -f; kubectl delete; terraform destroy; aws s3 rm --recursive / rb --force; dropdb; helm uninstall/delete; docker volume rm/prune and system prune; gcloud ... delete; find -delete / -exec rm; shred; truncate; wipefs -a. git branch -D asks first. Verbs are matched position-aware, so a bucket or object NAMED like a verb is allowed, and a relative path in the working tree stays allowed. sudo, doas and pkexec are transparent. A pre-push hook refuses any non-fast-forward push -- force, +refspec or lease alike; a human escapes with git push --no-verify. Known bypasses: aliases, an unusual value-flag, interpreters and scripts. Friction, not a security boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Destructive Commands

Best-effort guard against destructive commands, read as parsed commands (bash -c and cd chains included, echo excluded): rm -rf on absolute, home ($HOME/~) or root-adjacent paths (and PowerShell Remove-Item -Recurse); git push --force (not --force-with-lease), reset --hard, clean -f; kubectl delete; terraform destroy; aws s3 rm --recursive / rb --force; dropdb; helm uninstall/delete; docker volume rm/prune and system prune; gcloud ... delete; find -delete / -exec rm; shred; truncate; wipefs -a. git branch -D asks first. Verbs are matched position-aware, so a bucket or object NAMED like a verb is allowed, and a relative path in the working tree stays allowed. sudo, doas and pkexec are transparent. A pre-push hook refuses any non-fast-forward push -- force, +refspec or lease alike; a human escapes with git push --no-verify. Known bypasses: aliases, an unusual value-flag, interpreters and scripts. Friction, not a security boundary.

```
block(destructive_command @position-aware): rm_-rf(/|~|$HOME|.)|Remove-Item_-Recurse, git_push_--force, git_reset_--hard, git_checkout_., git_clean_-f, kubectl_delete, terraform_destroy, aws_s3(rm_--recursive|rb_--force), dropdb, helm(uninstall|delete), docker_volume(rm|prune)|system_prune, gcloud_delete, find(-delete|-exec_rm)|shred|truncate @dangerous_target, wipefs(-a|-o)
require_approval: reset_hard|rm_-rf|branch_-D; prefer: stash|soft_reset|force-with-lease|dry-run; push: refuse_non_ff
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives; a client that exports no plugin-root variable runs the hook, which then allows -- so treat this package as advisory unless a deny has been witnessed in your own client. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
