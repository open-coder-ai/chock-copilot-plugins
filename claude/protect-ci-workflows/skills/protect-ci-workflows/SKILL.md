---
name: protect-ci-workflows
description: "Asks a person before an agent weakens the checks that review its work. Shell guard refuses writes to .github/workflows/, .github/actions/, .github/dependabot.y*ml (redirect, rm/mv/tee/sed -i, cp into, git checkout/restore, cmdlets); reads and `chock sync` pass. Edit/Write: tool_use gate asks a person before a write to CI, hook or bot config (GitLab, Jenkins, CircleCI, CODEOWNERS, husky, more). Not judged: deletions, empty writes, shell writes to non-GitHub CI paths; plugin installs ship no gate."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: hooks/hooks.json
---

# Protect CI Workflows

Asks a person before an agent weakens the checks that review its work. Shell guard refuses writes to .github/workflows/, .github/actions/, .github/dependabot.y*ml (redirect, rm/mv/tee/sed -i, cp into, git checkout/restore, cmdlets); reads and `chock sync` pass. Edit/Write: tool_use gate asks a person before a write to CI, hook or bot config (GitLab, Jenkins, CircleCI, CODEOWNERS, husky, more). Not judged: deletions, empty writes, shell writes to non-GitHub CI paths; plugin installs ship no gate.

```
ci_config(.github/{workflows,actions,dependabot.y*ml}): never(shell_edit|delete); edit_or_write(those|CODEOWNERS|.gitlab-ci.yml|.gitlab/|Jenkinsfile*|.circleci/|azure-pipelines*|bitbucket-pipelines.yml|.buildkite/|.drone.yml|cloudbuild*|renovate|.pre-commit-config.yaml|.husky/|lefthook|.githooks/|.travis.yml|action.yml|more): ask_person
if(ci_change_needed): ask_person; person edits from own shell; no agent-typed marker passes  # an agent must not disarm the checks on its own work
```

This policy is enforced in this client by a PreToolUse hook installed with the plugin, subject to the fail conditions stated in the plugin description. Its write gate asks the person before the action proceeds, through the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
