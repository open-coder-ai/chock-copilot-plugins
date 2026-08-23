---
name: block-no-verify
description: "Best-effort guard against bypassing git hooks via git commit/push --no-verify or -n. Known bypass classes include aliases, wrapper scripts, and non-standard clients. Fix the underlying hook failure instead of skipping validation."
metadata:
  chock:
    artifact: rule
    enforcement: advise
    coverage_without_chock: advisory
---

# Block No-Verify

Best-effort guard against bypassing git hooks via git commit/push --no-verify or -n. Known bypass classes include aliases, wrapper scripts, and non-standard clients. Fix the underlying hook failure instead of skipping validation.

```
never(commit|push): --no-verify|-n
if(hook_fails): fix_issue; never(skip_hook)
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail-open condition stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
