---
name: opaque-blob-guard
description: "Warns (observe rollout; never refuses yet) when a change adds or edits, in tests/, fixtures/, testdata/, spec/, m4/, vendor/, gradle/wrapper/: a file whose first bytes are an archive, executable, wasm or database signature (any extension), a random-looking non-media file over 100 KB, or a symlink out of the repo; m4/configure/Makefile text that decodes and evaluates; a gradle wrapper jar changed with no new distributionSha256Sum. Misses text-encoded or prefixed payloads, other folders."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Flag Hidden Binary Files in Tests

Warns (observe rollout; never refuses yet) when a change adds or edits, in tests/, fixtures/, testdata/, spec/, m4/, vendor/, gradle/wrapper/: a file whose first bytes are an archive, executable, wasm or database signature (any extension), a random-looking non-media file over 100 KB, or a symlink out of the repo; m4/configure/Makefile text that decodes and evaluates; a gradle wrapper jar changed with no new distributionSha256Sum. Misses text-encoded or prefixed payloads, other folders.

```
binary_or_archive_in(tests|fixtures|testdata|spec|m4|vendor|gradle/wrapper): avoid; if_required: state(purpose), list(sha256+path in .chock/blob-allowlist.txt), person_commits
prefer: generate_in_test_setup | pinned_checksummed_download; never decode_and_eval(test_file)
```

This policy warns, and does not block, through the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
