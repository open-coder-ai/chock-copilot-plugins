---
name: rtk-dangerous-actions-blocker
description: "Deprecated: use block-destructive-commands, which shares this policy's destructive-command table. rtk#1007. Same table as block-destructive-commands: blocks root/home/.git deletes, force/delete/mirror pushes, reset --hard, clean -f, switch --discard, IaC destroy, cloud deletes, DB drops, lockouts; asks rm -rf off rtk's safe list, one named kubectl delete, bare lease, stash drop, prunes; dry-run flags pass. Own rows block credential-file reads (.env, keys, ~/.ssh) and echo or inline *_API_KEY/_SECRET/_TOKEN. Skips file rows in container exec."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Dangerous Commands (Deprecated)

Deprecated: use block-destructive-commands, which shares this policy's destructive-command table. rtk#1007. Same table as block-destructive-commands: blocks root/home/.git deletes, force/delete/mirror pushes, reset --hard, clean -f, switch --discard, IaC destroy, cloud deletes, DB drops, lockouts; asks rm -rf off rtk's safe list, one named kubectl delete, bare lease, stash drop, prunes; dry-run flags pass. Own rows block credential-file reads (.env, keys, ~/.ssh) and echo or inline *_API_KEY/_SECRET/_TOKEN. Skips file rows in container exec.

```
block: shared table (= block-destructive-commands: rm_-rf(/|~|.|abs|.git),push(-f|+ref|:ref|--mirror),reset_--hard|clean_-f|checkout_.|switch_--discard,destroy|cloud_delete,sql_drop|dropdb),read(.env|*.pem|*.key|id_rsa|~/.ssh|~/.aws),echo|inline($*_API_KEY|$*_SECRET|$*_TOKEN)
ask: rm_-rf(off safe_list),kubectl_delete(one_named),lease_bare,stash_drop,branch_-D,docker_prune,-auto-approve,pkill_-f,systemctl_disable;allow: dry_run(tool_flag);skip_inside: container_exec(file rows);prefix: rtk
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
