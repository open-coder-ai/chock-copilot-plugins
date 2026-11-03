---
name: protect-agent-config
description: "Stops an agent editing its guardrails (MITRE ATLAS AML.T0081). Shell guard refuses writes to AGENTS.md, whole agent folders (.claude/, .cursor/, .codex/, .gemini/, .agents/, .chock/, ...), MCP/hook client configs (.mcp.json, ...), guardrails toggles, plugin install markers, chock.lock, hook files of Cline, Kiro, Augment, Windsurf and Devin (user and machine level), ~/.claude.json, Claude Code plugin roots, project config of Kilo Code, opencode, Factory, Amp, Continue and Goose, .git/{hooks,config}, policy implementations/: redirect, rm/mv/tee/sed -i, cp into, git checkout/restore, a write through a symlink; CLAUDE_CODE_PLUGIN_DIRS, claude --plugin-dir, chock resume (best effort). Any case. An instruction file under docs/ asks a person. Coarse: also eval or sh -c of computed text, shells fed by pipe/variable, interpreter one-liners naming such a path. Reads, `chock sync` pass. Edit/Write: tool gate, same set and verdicts. Misses: user config under ~/.config/, writes a script makes, an existing hard link."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Agent Config

Stops an agent editing its guardrails (MITRE ATLAS AML.T0081). Shell guard refuses writes to AGENTS.md, whole agent folders (.claude/, .cursor/, .codex/, .gemini/, .agents/, .chock/, ...), MCP/hook client configs (.mcp.json, ...), guardrails toggles, plugin install markers, chock.lock, hook files of Cline, Kiro, Augment, Windsurf and Devin (user and machine level), ~/.claude.json, Claude Code plugin roots, project config of Kilo Code, opencode, Factory, Amp, Continue and Goose, .git/{hooks,config}, policy implementations/: redirect, rm/mv/tee/sed -i, cp into, git checkout/restore, a write through a symlink; CLAUDE_CODE_PLUGIN_DIRS, claude --plugin-dir, chock resume (best effort). Any case. An instruction file under docs/ asks a person. Coarse: also eval or sh -c of computed text, shells fed by pipe/variable, interpreter one-liners naming such a path. Reads, `chock sync` pass. Edit/Write: tool gate, same set and verdicts. Misses: user config under ~/.config/, writes a script makes, an existing hard link.

```
agent_config(AGENTS.md+wrappers|.goosehints|.{claude,cursor,codex,gemini,windsurf,agents,chock,junie,devin,grok,tabnine,augment,claude-plugin,kilo{,code},opencode,factory,amp,continue}/|{opencode,kilo}.json*|.continuerc.json|.github/{copilot*,hooks/}|.{clinerules,kiro}/hooks/|.kiro/agents/|<plugin>/hooks/|{.,.vscode/}mcp.json|~/.claude.json|chock.{selection.json,lock}|.git/{hooks,config}): never(edit|delete|*PLUGIN_DIRS|--plugin-dir); any case; links same
docs/**/<instruction>: ask; no waiver
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Its write gate is enforced in this client by the Stop hook installed with the plugin: the write itself is not judged here, because the client records no write-tool vocabulary, and what the turn left on disk is judged at its end instead. Subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
