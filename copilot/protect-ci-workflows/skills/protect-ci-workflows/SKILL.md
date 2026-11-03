---
name: protect-ci-workflows
description: "Asks a person before an agent weakens the checks that review its work. Shell guard refuses writes to .github/workflows/, .github/actions/, .github/dependabot.y*ml (redirect, rm/mv/tee/sed -i, cp into, git checkout/restore, cmdlets); reads and `chock sync` pass. Edit/Write: tool_use gate asks a person before a write to CI, hook or bot config (GitLab, Jenkins, CircleCI, CODEOWNERS, husky, more). Not judged: deletions, empty writes, shell writes to non-GitHub CI paths; plugin installs ship no gate."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect CI Workflows

Asks a person before an agent weakens the checks that review its work. Shell guard refuses writes to .github/workflows/, .github/actions/, .github/dependabot.y*ml (redirect, rm/mv/tee/sed -i, cp into, git checkout/restore, cmdlets); reads and `chock sync` pass. Edit/Write: tool_use gate asks a person before a write to CI, hook or bot config (GitLab, Jenkins, CircleCI, CODEOWNERS, husky, more). Not judged: deletions, empty writes, shell writes to non-GitHub CI paths; plugin installs ship no gate.

```
ci_config(.github/{workflows,actions,dependabot.y*ml}): never(shell_edit|delete); edit_or_write(those|CODEOWNERS|.gitlab-ci.yml|.gitlab/|Jenkinsfile*|.circleci/|azure-pipelines*|bitbucket-pipelines.yml|.buildkite/|.drone.yml|cloudbuild*|renovate|.pre-commit-config.yaml|.husky/|lefthook|.githooks/|.travis.yml|action.yml|more): ask_person
if(ci_change_needed): ask_person; person edits from own shell; no agent-typed marker passes  # an agent must not disarm the checks on its own work
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Its write gate asks the person before the action proceeds, through the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
