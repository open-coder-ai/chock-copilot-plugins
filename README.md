# chock-copilot-plugins

[![Generated-only](https://github.com/open-coder-ai/chock-copilot-plugins/actions/workflows/generated-only.yml/badge.svg)](https://github.com/open-coder-ai/chock-copilot-plugins/actions/workflows/generated-only.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Contribute upstream](https://img.shields.io/badge/contribute-chock--catalog-8957e5)](https://github.com/open-coder-ai/chock-catalog)

Chock policies packaged as installable plugins for **GitHub Copilot** — Copilot CLI and
VS Code agent mode. Guard policies ship a real `PreToolUse` hook, so a matched destructive
command is **denied in the session**, not just discouraged.

<img src="docs/assets/hero.svg" alt="Animated replay: an agent runs rm -rf and the chock guard denies it before it runs (Copilot CLI / VS Code, exit 2)" width="720">

**This repository is generated.** Every file is compiled from policy sources in
[chock-catalog](https://github.com/open-coder-ai/chock-catalog) by
[chock](https://github.com/open-coder-ai/chock). Pull requests here are closed with a
pointer to the catalog — review belongs where the source is.

## Which clients this works with

These packages use the **Claude plugin format**, which VS Code and GitHub Copilot CLI read
natively (VS Code auto-detects the format and sets `CLAUDE_PLUGIN_ROOT` for the hook). The
same packages also work in Claude Code. This repository is the
**Copilot-branded** distribution of that content; the format-named distribution lives at
[chock-claude-plugins](https://github.com/open-coder-ai/chock-claude-plugins) and the two
are byte-identical where they overlap, because both are generated from the same catalog.
Cursor and Codex users are served by
[chock-cursor-plugins](https://github.com/open-coder-ai/chock-cursor-plugins) and
[chock-codex-plugins](https://github.com/open-coder-ai/chock-codex-plugins), which carry
those vendors' own formats and deny dialects.

```
# VS Code / GitHub Copilot: add this repository as a plugin marketplace, then install a
# plugin by name. See the Copilot plugins marketplace and VS Code's agent-plugins docs:
#   https://github.com/github/copilot-plugins
#   https://code.visualstudio.com/docs/agent-customization/agent-plugins
```

Clients that read the Agent Plugins 1.0 standard instead can use the `agent-plugins/` tree
(advisory: the standard carries skills, not hooks).

## What a plugin actually does — read this before installing

Chock's rule is that a claim must match a mechanism, and that rule applies to these
packages: they are not equally strong and they say so in each description.

- **Guard policies** (e.g. `block-destructive-commands`) ship a `PreToolUse` hook and are
  **session-enforced** where the host honours it — the hook exits non-zero and the client
  refuses the call. This needs `python3` and a usable shell on PATH. Without them,
  fail-open clients allow silently and fail-closed clients refuse matched commands; on
  Windows, disable the `python3` Microsoft Store alias or install Python. Every guard's
  description states this posture verbatim.
- **Advisory policies** are a skill the client reads. They shape behaviour; they cannot
  block anything on their own.

See **[PLUGINS.md](PLUGINS.md)** for the full list: every policy, its version, whether it
enforces or advises in this client, and a link to its page in the catalog. That file is
generated from the packages themselves, so it cannot drift from what is published.

**A plugin is not the same as adopting Chock.** A plugin governs one person's session on
one client. It cannot enforce anything at commit time, it does not travel with a clone, and
it does not run in CI. Repository-wide enforcement — git hooks and a CI gate that a
`--no-verify` cannot skip — comes from installing Chock in the repo:

```bash
pip install chock
chock init && chock sync --ci
```

## Layout

```
claude/<policy-id>/          Claude-layout packages (hooks where the policy has a guard) — Copilot reads these natively
copilot/<policy-id>/         Agent Plugins 1.0 layout with the same hook under com.github.copilot/ — for spec-validating marketplaces
agent-plugins/<policy-id>/   plain Agent Plugins 1.0 packages (advisory: the standard itself has no hooks)
.claude-plugin/marketplace.json    the index VS Code and Claude Code read
.github/plugin/marketplace.json    byte-identical copy, the path Copilot CLI reads
```

The trees are deliberately separate. The same policy is enforced in a package that ships a
hook and advisory in a package that cannot carry one — so a shared skill file would have to
make a claim that is false for one of them. `claude/` and `copilot/` run byte-identical
hooks; they differ only in where the manifest and hook file live, because marketplace
validators disagree about that.

## Trust

- **Generated only:** CI regenerates from the pinned catalog and fails on any difference,
  so content here cannot be hand-edited into something the catalog never published.
- **Byte-identical guards:** guard scripts and the hook adapter are verbatim copies of
  their sources in the framework — a plugin cannot quietly behave differently from a
  repository install.
- **Best-effort, not a boundary:** guards are pattern-based filters. Aliases, quoting, and
  unusual paths can evade them. See
  [SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md) and the
  [assurance case](https://github.com/open-coder-ai/chock/blob/main/docs/assurance-case.md).

## Contributing

Pull requests that change packages here are closed automatically, and not because the
change is unwelcome: every package is compiled from the catalog, so an edit here would be
overwritten at the next publish and would carry none of a policy's checks. What is welcome,
and where it goes:

| You want to | Go to |
| :--- | :--- |
| Fix or add a policy | [chock-catalog](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md) — it reaches every client from there, including this one |
| Report that a guard did or did not block on your Copilot CLI or VS Code version | an issue on [chock](https://github.com/open-coder-ai/chock/issues/new/choose), which records the witnessed-blocking claims these packages carry; "it fails open where you say it fails closed" is the most useful result you can send |
| Report a bug in how packages are generated | [chock](https://github.com/open-coder-ai/chock/issues/new/choose), where the emitter lives |
| Fix this README | here — it is the one hand-written file in the repository |

## Part of the open-coder-ai family

Everything under [open-coder-ai](https://github.com/open-coder-ai) is built on one rule: a claim must match a
mechanism. Where this repository sits among the others:

| Repository | What it is |
| :--- | :--- |
| [chock](https://github.com/open-coder-ai/chock) | The framework: write a policy once, enforce it on git hooks, CI, and every agent |
| [chock-catalog](https://github.com/open-coder-ai/chock-catalog) | The policies, each graded by what it actually enforces |
| [agentseam](https://github.com/open-coder-ai/agentseam) | The primitives layer under chock: one handler API over every agent's hooks, with a capability matrix that carries its provenance |
| [context-report](https://github.com/open-coder-ai/context-report) | A signed report format for whether a plugin, hook, skill or `AGENTS.md` actually works |
| [chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel) | A weekly, human-reviewed threat digest scored against the catalog |
| [chock-claude-plugins](https://github.com/open-coder-ai/chock-claude-plugins) · [cursor](https://github.com/open-coder-ai/chock-cursor-plugins) · [codex](https://github.com/open-coder-ai/chock-codex-plugins) | The same catalog compiled for the other clients; generated only, like this one |
| [chock-quickstart](https://github.com/open-coder-ai/chock-quickstart) · [chock-example](https://github.com/open-coder-ai/chock-example) | Template repositories: exactly what `chock init` leaves behind, and a working adoption with one policy per layer |

## License

Apache-2.0, same as the framework and the catalog.
