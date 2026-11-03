---
name: scan-instruction-files
description: "Asks a person before a change adds injection text to an agent instruction file (AGENTS.md, CLAUDE.md, GEMINI.md, Cursor/Windsurf/Cline/Roo/Kiro rules, Copilot instructions and prompts, SKILL.md, agent commands): rule overrides, secrecy, auto-approve, hook or review bypass, fetch-and-run, remote instructions, role swaps, fake system tags, removed guardrails. Refuses secret exfiltration and encoded payloads. Added text only; English phrases; friction, not a boundary."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Scan Instruction Files

Asks a person before a change adds injection text to an agent instruction file (AGENTS.md, CLAUDE.md, GEMINI.md, Cursor/Windsurf/Cline/Roo/Kiro rules, Copilot instructions and prompts, SKILL.md, agent commands): rule overrides, secrecy, auto-approve, hook or review bypass, fetch-and-run, remote instructions, role swaps, fake system tags, removed guardrails. Refuses secret exfiltration and encoded payloads. Added text only; English phrases; friction, not a boundary.

```
on(commit|tool_use): block(script) script=scan-instruction-files-gate.py
This change adds text to an agent instruction file that an attacker would add: it overrides standing rules, hides work from a person, approves tools or skips hooks and review without one, downloads and runs code, follows remote instructions, recasts the agent's role, poses as a system message, removes a guardrail statement, sends a secret somewhere, or hides a command in an encoded blob. Write rules as prohibitions a person can review, and put setup steps in a reviewed script. A person keeps a reviewed line by committing from their own shell: CHOCK_ALLOW=scan-instruction-files for that one commit answers an ask, and 'chock: allow instruction-scan' on the line waives a refusal; an agent asks the person and never sets either.
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
