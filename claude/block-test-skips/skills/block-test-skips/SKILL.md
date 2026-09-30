---
name: block-test-skips
description: "Blocks newly added test skips and focus markers -- @pytest.mark.skip/skipif, @unittest.skip, it/describe/test.skip, xit/xdescribe, it/describe/test.only, JUnit @Disabled/@Ignore and Go t.Skip -- in test files, at commit and at agent tool-use. Only skips the change adds are judged (the engine compares findings with the baseline), so a skip already committed never blocks an unrelated edit. Companion of protect-test-integrity, which cannot see skips. Waivers: 'chock: allow test-skip' on the line is honoured at commit; in the agent, or for a commit with CHOCK_AGENT_COMMIT set, only a skip already committed in HEAD counts, so an agent that needs a new one must ask a person."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Block Test Skips

Blocks newly added test skips and focus markers -- @pytest.mark.skip/skipif, @unittest.skip, it/describe/test.skip, xit/xdescribe, it/describe/test.only, JUnit @Disabled/@Ignore and Go t.Skip -- in test files, at commit and at agent tool-use. Only skips the change adds are judged (the engine compares findings with the baseline), so a skip already committed never blocks an unrelated edit. Companion of protect-test-integrity, which cannot see skips. Waivers: 'chock: allow test-skip' on the line is honoured at commit; in the agent, or for a commit with CHOCK_AGENT_COMMIT set, only a skip already committed in HEAD counts, so an agent that needs a new one must ask a person.

```
on(commit|tool_use): block(script) script=block-test-skips-gate.py
Test skip added. Fix the test or the code instead of skipping it. A person may waive a reviewed skip with 'chock: allow test-skip' on the line and commit from their own shell; in the agent a waiver counts only for a line already committed in HEAD, so an agent asks the person rather than writing the pragma itself.
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
