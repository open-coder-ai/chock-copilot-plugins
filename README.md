<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/logo.svg" alt="chock logo" width="110">
</p>

<h1 align="center">chock-copilot-plugins</h1>

<p align="center"><strong>Chock policies for Copilot CLI and VS Code agent mode — a real <code>PreToolUse</code> deny hook where the client supports it.</strong></p>

<p align="center">

[![Generated-only](https://github.com/open-coder-ai/chock-copilot-plugins/actions/workflows/generated-only.yml/badge.svg)](https://github.com/open-coder-ai/chock-copilot-plugins/actions/workflows/generated-only.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Contribute upstream](https://img.shields.io/badge/contribute-chock--catalog-8957e5)](https://github.com/open-coder-ai/chock-catalog)

</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/demo.gif" alt="Chock's demo: an agent runs a destructive command and a guard plugin denies it before it executes" width="760">
</p>

An agent you're running can already touch your shell, your git history, and your CI config.
You want it to move fast without being the reason a stray `rm -rf` actually happens. Telling
it to be careful in a prompt is not a guarantee; a plugin that can refuse the command is
closer to one — and it should be honest about which of those two it is.

## Install

```
# VS Code / GitHub Copilot: add this repository as a plugin marketplace, then install a
# plugin by name. See the Copilot plugins marketplace and VS Code's agent-plugins docs:
#   https://github.com/github/copilot-plugins
#   https://code.visualstudio.com/docs/agent-customization/agent-plugins
```

These packages use the Claude plugin format, which VS Code and GitHub Copilot CLI read
natively (VS Code auto-detects the format and sets `CLAUDE_PLUGIN_ROOT` for the hook).

## What you get

Guard policies ship a `PreToolUse` hook and are session-enforced where the host honours it —
the hook exits non-zero and the client refuses the call. This needs `python3` and a usable
shell on PATH: fail-open clients allow silently without them, fail-closed clients refuse
matched commands. Advisory policies are a skill the client reads; they shape behaviour but
cannot block anything on their own. See **[PLUGINS.md](PLUGINS.md)** for the full list: each
policy, its version, whether it enforces or advises in this client, and a catalog link.

## Generated from chock-catalog

Every file here is compiled from policy sources in
[chock-catalog](https://github.com/open-coder-ai/chock-catalog) by
[chock](https://github.com/open-coder-ai/chock). Pull requests against this repository are
closed automatically — open them against the catalog instead.

- **Generated only:** CI regenerates from the pinned catalog and fails on any difference.
- **Byte-identical guards:** guard scripts and the hook adapter are verbatim copies of their
  framework sources.
- **Best-effort, not a boundary:** guards are pattern-based filters; see
  [SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md).
- This README is the exception: the one hand-written file in this repository, so it alone
  sits outside the generated-only guarantee.

## Part of open-coder-ai

| | |
|---|---|
| [agentseam](https://github.com/open-coder-ai/agentseam) | the primitives — one handler API and a verified capability matrix across 16 agents |
| [chock](https://github.com/open-coder-ai/chock) | the compiler — one policy into git hooks, CI gates and native pre-tool hooks |
| [chock-catalog](https://github.com/open-coder-ai/chock-catalog) | the policies — 39, each labelled enforced or advisory, with replayed evals |
| [context-report](https://github.com/open-coder-ai/context-report) | the evidence — a signed report of whether an agent artifact actually works |
| [chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel) | the threat ledger the catalog's policies answer to |
| chock-{claude,cursor,copilot,codex}-plugins | the catalog, packaged for each agent's plugin format (generated) |
| chock-quickstart · chock-example | template repos: what `chock init` leaves behind, and a full adoption |

## License

Apache-2.0, same as the framework and the catalog.
