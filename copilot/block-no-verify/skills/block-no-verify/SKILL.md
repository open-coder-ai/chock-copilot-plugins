---
name: block-no-verify
description: "Best-effort guard against bypassing git hooks via git commit/push --no-verify, commit's short -n form, or -c core.hooksPath overrides. On git push, -n means --dry-run and stays allowed. Known bypass classes include aliases, wrapper scripts, and non-standard clients. Fix the underlying hook failure instead of skipping validation."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block No-Verify

Best-effort guard against bypassing git hooks via git commit/push --no-verify, commit's short -n form, or -c core.hooksPath overrides. On git push, -n means --dry-run and stays allowed. Known bypass classes include aliases, wrapper scripts, and non-standard clients. Fix the underlying hook failure instead of skipping validation.

```
never(commit): --no-verify|-n; never(push): --no-verify
if(hook_fails): fix_issue; never(skip_hook)
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives; a client that exports no plugin-root variable runs the hook, which then allows -- so treat this package as advisory unless a deny has been witnessed in your own client. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
