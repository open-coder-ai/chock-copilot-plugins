---
name: compromised-package-ioc
description: "Blocks adding a known-malicious package version (npm, PyPI, crates, Go, RubyGems, Packagist manifests and lockfiles), a re-pointed action ref, or an IOC file name, from a dated, sourced list (data/ioc.json, CI fails 120 days after as_of). Runs: commit, agent write, turn's end; only additions judged. Exact versions only: a range is not resolved. A snapshot, not a feed; friction, not a boundary. Adopt under rollout observe."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Block Known-Malicious Packages

Blocks adding a known-malicious package version (npm, PyPI, crates, Go, RubyGems, Packagist manifests and lockfiles), a re-pointed action ref, or an IOC file name, from a dated, sourced list (data/ioc.json, CI fails 120 days after as_of). Runs: commit, agent write, turn's end; only additions judged. Exact versions only: a range is not resolved. A snapshot, not a feed; friction, not a boundary. Adopt under rollout observe.

```
on(commit|tool_use): block(script) script=compromised-package-ioc-gate.py
A known-malicious package version, compromised action ref or IOC file name was added. Use a release outside the list (the cited advisory names the fixed one) or drop the dependency, pin the action to a reviewed commit, or delete the file. The list changes only in a reviewed pull request to this policy's data/ioc.json; there is no in-line waiver.
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
