---
name: package-lifecycle-scripts
description: "Blocks fetch-exec class hooks and asks about other new ones when a change adds or edits code that runs at install or build time: npm install/prepare/pack scripts, gypfile without native sources, bin shadowing, unpinned git/URL deps; setup.py cmdclass and import-time calls, .pth imports, conftest, pyproject build hooks; build.rs and build-deps; go:generate; MSBuild Exec; gemspec, extconf, Podfile, Composer, Maven, Gradle exec. Judges file text, not what a hook runs. Friction, not a security boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Review Scripts That Run on Install

Blocks fetch-exec class hooks and asks about other new ones when a change adds or edits code that runs at install or build time: npm install/prepare/pack scripts, gypfile without native sources, bin shadowing, unpinned git/URL deps; setup.py cmdclass and import-time calls, .pth imports, conftest, pyproject build hooks; build.rs and build-deps; go:generate; MSBuild Exec; gemspec, extconf, Podfile, Composer, Maven, Gradle exec. Judges file text, not what a hook runs. Friction, not a security boundary.

```
avoid(install_time_and_build_time_scripts); if_required: explain(why), keep_offline: true, pin(git_and_url_deps: commit)
prefer: download to a file, verify checksum, run as a reviewed step; never add hooks that fetch, decode or eval
```

This policy is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
