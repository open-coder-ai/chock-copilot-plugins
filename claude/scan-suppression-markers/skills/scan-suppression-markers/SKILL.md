---
name: scan-suppression-markers
description: "Asks a person before a change adds a scanner suppression: inline ignore markers (bandit, gosec, ruff S codes, Sonar, Semgrep, ESLint security, Checkov, tfsec, Trivy, KICS, hadolint, cfn_nag, zizmor, gitleaks, detect-secrets, CodeQL, Java/C#/Rust security allows), scanner ignore files and skip keys, a CI scan set to pass on failure. Only added lines; prose skipped. Runs: commit, agent write, turn's end; CI annotates. Line-local, friction not a boundary."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Review Added Scanner Suppressions

Asks a person before a change adds a scanner suppression: inline ignore markers (bandit, gosec, ruff S codes, Sonar, Semgrep, ESLint security, Checkov, tfsec, Trivy, KICS, hadolint, cfn_nag, zizmor, gitleaks, detect-secrets, CodeQL, Java/C#/Rust security allows), scanner ignore files and skip keys, a CI scan set to pass on failure. Only added lines; prose skipped. Runs: commit, agent write, turn's end; CI annotates. Line-local, friction not a boundary.

```
on(commit|tool_use): ask(script) script=scan-suppression-markers-gate.py
This change adds a scanner suppression: an inline ignore marker, an ignore-file entry or skip key, or a CI security step told to pass when it fails. Fix what the scanner reports instead of silencing it. If the finding is a reviewed false positive, a person keeps the suppression by committing from their own shell with CHOCK_ALLOW=scan-suppression-markers for that one commit; an agent asks the person and never sets it.
```

This policy asks the person before the action proceeds, through the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
