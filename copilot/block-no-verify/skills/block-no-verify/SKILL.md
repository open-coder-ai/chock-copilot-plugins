---
name: block-no-verify
description: "Best-effort guard against bypassing git hooks via git commit/push --no-verify or -n. Known bypass classes include aliases, wrapper scripts, and non-standard clients. Fix the underlying hook failure instead of skipping validation."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block No-Verify

Best-effort guard against bypassing git hooks via git commit/push --no-verify or -n. Known bypass classes include aliases, wrapper scripts, and non-standard clients. Fix the underlying hook failure instead of skipping validation.

```
never(commit|push): --no-verify|-n
if(hook_fails): fix_issue; never(skip_hook)
```

This package ships a PreToolUse hook under com.github.copilot/ that enforces this policy in clients reading that namespace (documented for VS Code agent mode), subject to the fail posture stated in the plugin description. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
