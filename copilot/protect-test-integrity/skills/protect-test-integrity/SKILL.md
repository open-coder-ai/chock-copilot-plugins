---
name: protect-test-integrity
description: "Blocks weakening tests to turn them green: a deleted test file (commit only), a net loss of assertions, or an added vacuous assertion (assert True, expect(true).toBe(true)). Added skips are block-test-skips. Runs: commit (incl. an agent's commit: CHOCK_AGENT_COMMIT, CLAUDECODE=1, AI_AGENT or agent_commit_env), agent write (vs disk), turn's end (vs HEAD). Waiver: 'chock: allow test-integrity' on an added line. Person's commit: honoured. Agent: only if already in HEAD; it asks a person."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Test Integrity

Blocks weakening tests to turn them green: a deleted test file (commit only), a net loss of assertions, or an added vacuous assertion (assert True, expect(true).toBe(true)). Added skips are block-test-skips. Runs: commit (incl. an agent's commit: CHOCK_AGENT_COMMIT, CLAUDECODE=1, AI_AGENT or agent_commit_env), agent write (vs disk), turn's end (vs HEAD). Waiver: 'chock: allow test-integrity' on an added line. Person's commit: honoured. Agent: only if already in HEAD; it asks a person.

```
on(commit|tool_use): block(test_integrity) test_path_regex(regex) assertion_pattern(regex) dummy_assertion_pattern(regex) ...
Test integrity: this change deletes a test file, removes more assertions than it adds, or adds a vacuous assertion. Fix the code under test, not the test. If the removal is deliberate (obsolete behaviour, a test moved elsewhere), a person adds 'chock: allow test-integrity' on an added line of that test file and commits from their own shell. In the agent (tool use, the turn's end, an agent's commit) only a waiver already committed in HEAD counts, so an agent asks a person.
```

This policy is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
