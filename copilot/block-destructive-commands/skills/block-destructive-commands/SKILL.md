---
name: block-destructive-commands
description: "Best-effort, on parsed commands (echo ignored); one verdict table shared with rtk-dangerous-actions-blocker. Blocks recursive deletes of root, home, abs paths or .git; mv/chmod/chown -R of system dirs; dd to devices, mkfs; lockouts; force, delete, mirror pushes; history rewrites; switch --discard; IaC destroy; cloud deletes; DB drops. Asks: rm -rf off a safe list, one named kubectl delete, bare lease, stash drop, -auto-approve, drain, prunes, API deletes. Dry runs pass. Pre-push: no non-ff."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Destructive Commands

Best-effort, on parsed commands (echo ignored); one verdict table shared with rtk-dangerous-actions-blocker. Blocks recursive deletes of root, home, abs paths or .git; mv/chmod/chown -R of system dirs; dd to devices, mkfs; lockouts; force, delete, mirror pushes; history rewrites; switch --discard; IaC destroy; cloud deletes; DB drops. Asks: rm -rf off a safe list, one named kubectl delete, bare lease, stash drop, -auto-approve, drain, prunes, API deletes. Dry runs pass. Pre-push: no non-ff.

```
block: rm|mv|chmod|chown_-R(root|home|sys),rm_-rf(abs|.|$PWD|.git),dd_of=/dev|mkfs|wipefs|shred,lockout(authkeys|usermod|passwd|chattr_+i|kill_-1|crontab),git(push_-f|+ref|-d|:ref|--mirror,reset_--hard,clean_-f,checkout|restore_.|switch_--discard,reflog|gc|filter),iac_destroy,cloud|k8s|helm_delete,sql_drop
ask: rm_-rf(off safe_list),kubectl_delete(1_named),lease_bare,stash_drop,branch_-D,-auto-approve,drain,prune,pkill_-f,systemctl_disable,api_DELETE;allow: tool_dry_run;push: refuse_non_ff
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
