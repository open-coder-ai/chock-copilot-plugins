---
name: block-unguarded-agent-spawn
description: "Best effort. Refuses agents with safety off: claude --dangerously-skip-permissions, bypassPermissions, wildcard --allowedTools; codex --full-auto, --yolo, -a never, danger-full-access; gemini --yolo; cursor-agent --force; aider --yes-always; copilot --allow-all-tools; amp, cline, goose, opencode auto-approve; DeepSeek Harness 127.0.0.1:3080/api calls lifting sandbox/approvals (CVE-2026-82533). Sees through bash -c, sudo, npx. Misses: config, aliases, scripts, ssh, other dsh ports, file bodies."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Block Agents Started With Safety Off

Best effort. Refuses agents with safety off: claude --dangerously-skip-permissions, bypassPermissions, wildcard --allowedTools; codex --full-auto, --yolo, -a never, danger-full-access; gemini --yolo; cursor-agent --force; aider --yes-always; copilot --allow-all-tools; amp, cline, goose, opencode auto-approve; DeepSeek Harness 127.0.0.1:3080/api calls lifting sandbox/approvals (CVE-2026-82533). Sees through bash -c, sudo, npx. Misses: config, aliases, scripts, ssh, other dsh ports, file bodies.

```
never(spawn_agent): claude(--dangerously-skip-permissions|bypassPermissions|--allowedTools_*), codex(--full-auto|--yolo|-a_never|--sandbox_danger-full-access), gemini(--yolo|-y|--approval-mode_yolo), cursor-agent(--force|-f), aider(--yes-always), copilot(--allow-all-tools), amp|cline|goose|opencode(auto-approve flags)
never(http_to(dsh 127.0.0.1:3080/api)+body(danger-full-access|approval_never)); if(unattended_run_needed|wider_access): ask_person; person_starts_it
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
