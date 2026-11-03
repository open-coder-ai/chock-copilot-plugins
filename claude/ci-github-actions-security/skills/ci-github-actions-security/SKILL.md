---
name: ci-github-actions-security
description: "Friction, not a security boundary: refuses or asks (each rule's tier) on GitHub Actions weaknesses a change adds to workflows, composite actions and dependabot.yml: event text in run/script, PR-head checkout under pull_request_target/workflow_run/issue_comment, missing or write-all permissions, secrets inherit or inlined, self-hosted runners on PRs, GITHUB_ENV writes, artifact and cache poisoning, agent steps on untrusted text. Misses: step outputs, composite internals, custom runner labels."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# CI GitHub Actions Security

Friction, not a security boundary: refuses or asks (each rule's tier) on GitHub Actions weaknesses a change adds to workflows, composite actions and dependabot.yml: event text in run/script, PR-head checkout under pull_request_target/workflow_run/issue_comment, missing or write-all permissions, secrets inherit or inlined, self-hosted runners on PRs, GITHUB_ENV writes, artifact and cache poisoning, agent steps on untrusted text. Misses: step outputs, composite internals, custom runner labels.

```
on(commit|tool_use): block(script) script=ci-github-actions-security-gate.py
A GitHub Actions workflow, composite action or Dependabot file gained a weakness (each finding names its rule, CWE and fix). Fix it as the finding says. A person who has reviewed one may keep it with '# chock: allow <rule id>' on that line and commit from their own shell; in the agent only a line already committed in HEAD counts, so an agent asks the person rather than writing the comment.
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
