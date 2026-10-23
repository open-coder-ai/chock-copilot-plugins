<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/logo.svg" alt="chock logo" width="110">
</p>

<h1 align="center">chock-copilot-plugins</h1>

<p align="center"><strong>Blocks your Copilot agent's wrong moves before they land.</strong></p>
<p align="center">Chock policies for Copilot CLI and VS Code agent mode — a real <code>PreToolUse</code> deny hook where the client supports it.</p>

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

## Two ways to get these plugins

**This marketplace: no chock on the machine.** Each policy is its own plugin, republished with
every chock release. You need Python 3.11+ and git, and nothing else. Add this repository as a
plugin marketplace in VS Code / GitHub Copilot, then install a plugin by name. See the Copilot
plugins marketplace and VS Code's agent-plugins docs:

- <https://github.com/github/copilot-plugins>
- <https://code.visualstudio.com/docs/agent-customization/agent-plugins>

These packages use the Claude plugin format, which VS Code and GitHub Copilot CLI read
natively (VS Code auto-detects the format and sets `CLAUDE_PLUGIN_ROOT` for the hook).

**The builder: one plugin, needs chock.** Pick the policies you want at
**[chock.sh](https://chock.sh/coding/builder/)**; it writes one `chock install` command that
builds a single plugin on your machine from
[chock-catalog](https://github.com/open-coder-ai/chock-catalog). That command needs
`pip install chock`:

```bash
pip install chock
chock install --selection '<code>' --client copilot --apply
```

**Use one route for a given policy, never both.** Every copy of a plugin runs its own hook, so
a policy installed from both places runs twice. To switch a policy from one route to the other,
remove it from the first route before installing it from the second:

1. Remove each plugin you installed from the `chock` marketplace. In Copilot CLI:
   `copilot plugin uninstall <id>@chock`. In VS Code: `/plugin uninstall <id>@chock` in agent
   mode.
2. To drop the marketplace too: `copilot plugin marketplace remove chock` in Copilot CLI,
   `/plugin marketplace remove chock` in VS Code.

### Builder links, one policy at a time

Each link opens the builder with that one policy preselected for Copilot, pinned to chock-catalog
commit `e885f918a335e20dfddb4fad0331d4d5b4c5eb43` (the catalog's `main` on 2026-10-21). The pin is
the catalog's current version of each policy, which can be newer than the version listed in
[PLUGINS.md](PLUGINS.md).

| policy | in Copilot | builder |
| :--- | :--- | :--- |
| `agent-discipline` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJhZ2VudC1kaXNjaXBsaW5lIiwidmVyc2lvbiI6IjAuMC4zIiwic2hhMjU2IjoiMDM4YjE5ZDcyNmVmYzEyNWVlYWYxMzIxYWM0ZmViNzBjN2ZkMWEyMDdkYThlODk1M2JlZDY1ZDRhY2RkODQ3MiJ9XX0) |
| `block-curl-pipe-sh` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay1jdXJsLXBpcGUtc2giLCJ2ZXJzaW9uIjoiMC4xLjQiLCJzaGEyNTYiOiIzNGI5NjFjNDgyNTg4ZDBiNTFjOGRjNzY1NWU5MTVlYTk4YzVjNDcwMjdkY2ExMTRmOGE3ZDEyN2YzNzY2MmYyIn1dfQ) |
| `block-destructive-commands` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay1kZXN0cnVjdGl2ZS1jb21tYW5kcyIsInZlcnNpb24iOiIwLjMuMCIsInNoYTI1NiI6IjU0MWI0ZDMyNzc4ODQyNDhkMzIxNjAxZDA0Y2UwMTgyNTM3YzMwZTI1OTZkYzc4MGMwMDFhZTUzMmIxYjlkNTAifV19) |
| `block-invisible-unicode` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay1pbnZpc2libGUtdW5pY29kZSIsInZlcnNpb24iOiIwLjEuMSIsInNoYTI1NiI6ImIxMDg0MzE1NzA2ZGMyNmUzMDk3ZGQxYzFhODkzYzc4MDQxNWYxMTY4YWI1YTk5NWMxOWE4OTVmOTM2ODhjNWEifV19) |
| `block-no-verify` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay1uby12ZXJpZnkiLCJ2ZXJzaW9uIjoiMC41LjAiLCJzaGEyNTYiOiJhNDA2ZDUwZTRjZjMwZDU2NDMwODQwODBhZDQyNTlkMzQyZjNlZGQwNzAzNjg4OGM0N2E4MjE0ZjY2MDk5NGNjIn1dfQ) |
| `block-test-skips` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay10ZXN0LXNraXBzIiwidmVyc2lvbiI6IjAuMS4wIiwic2hhMjU2IjoiODUzNWM1MmVkMDU1YjA4NGIzZDg3MGIzMzk0NDg3NDFlMTgzNGY2ZmQ5MWFiMmQzMmVhZDFkMjYzZjQ3NTU3NCJ9XX0) |
| `block-unapproved-egress` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay11bmFwcHJvdmVkLWVncmVzcyIsInZlcnNpb24iOiIwLjIuNCIsInNoYTI1NiI6ImU0ODZmZTM3YjEyMWMyMDU0MWY3MWNlZjZjYjg1NjA1ZmUwYTA2ZWVlYjZlNTYzODNjOThmZmIwMGMwMDdlMTAifV19) |
| `block-unguarded-agent-spawn` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay11bmd1YXJkZWQtYWdlbnQtc3Bhd24iLCJ2ZXJzaW9uIjoiMC4yLjAiLCJzaGEyNTYiOiJlNDVjMjE1NTFmMWYxYTI0MjI3NTY4M2FmZTlhZTAwNDYyNzdkOGQ1N2Y1MzczNDRhYzcxM2ZmYmZiYTQ2MTM3In1dfQ) |
| `block-wildcard-agent-permissions` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJibG9jay13aWxkY2FyZC1hZ2VudC1wZXJtaXNzaW9ucyIsInZlcnNpb24iOiIwLjAuMTAiLCJzaGEyNTYiOiI2YjMxOWRmNjhmMDNjOTM2MjZjNjk1ZDI5ODcwYzE3OTQ1NmVmYmRmNGVmYjMzMDEyYmU5ZmQwZWQ4YTM4ZWVkIn1dfQ) |
| `chock-mise` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJjaG9jay1taXNlIiwidmVyc2lvbiI6IjAuMC4zIiwic2hhMjU2IjoiMWIwNzJiZDUxNzRhYzhhNzViZDQ0NGVkMTAwZDQxY2RiNzZjYzM5ZjFkNjZjODY2YTdjMmQ5YTczZWVjZjUxZiJ9XX0) |
| `code-safety` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJjb2RlLXNhZmV0eSIsInZlcnNpb24iOiIwLjAuNCIsInNoYTI1NiI6IjUxMDY3OTUxMjM1YTlhZGQzMGYyYWZhMjA3NjJjNTNmZDM5MmJhMmQ1NzM4NDU5NzBjMjliZDMwMjk1MWVhZTIifV19) |
| `context-hygiene` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJjb250ZXh0LWh5Z2llbmUiLCJ2ZXJzaW9uIjoiMC4wLjIiLCJzaGEyNTYiOiI0ODkyZDIxNGM2Mjg5N2JiMjgzZWY1MmZmNmVjYzgwNTZkNjcyMTM2MzBmNjE3YzIxMjA2ZGY1ZDViNWU3ZDc0In1dfQ) |
| `firecrawl-fallback-only` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJmaXJlY3Jhd2wtZmFsbGJhY2stb25seSIsInZlcnNpb24iOiIwLjEuMSIsInNoYTI1NiI6ImMxZmIzZjRjZDI0ZGZiMDcwYmMxZGNmYTBkNTdkN2U5MDE3NDgxOTA4YmI1NDg1MGM1NmQ1NmRmY2MwNzkwNjQifV19) |
| `git-safety` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJnaXQtc2FmZXR5IiwidmVyc2lvbiI6IjAuMC40Iiwic2hhMjU2IjoiNzg0YzNhMTg4YzVjNDJhODg2MDcwMzAxOTlkZTRiNWFjYTlmM2MyZjQ2Mjc5OGViOTY2OTFjNzEzMDdjYTg1OCJ9XX0) |
| `guard-memory-writes` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJndWFyZC1tZW1vcnktd3JpdGVzIiwidmVyc2lvbiI6IjAuMC45Iiwic2hhMjU2IjoiZTg4ZmQ0NmViYmI3YTVhNjZmNzdlNjRiN2FlNjVlNDA5NDVhNGQyYTU3ZGMyNWQ1M2MzMWY3M2U1NzgwY2Q3YiJ9XX0) |
| `injection-defense` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJpbmplY3Rpb24tZGVmZW5zZSIsInZlcnNpb24iOiIwLjAuNCIsInNoYTI1NiI6IjY0MzdjZTJhMzZhN2NiNGE2MGZiNzhjMzk3OGEzMzYyYWEzNWNjOWNkZmI5M2Y4NjdlNTg1ODUyN2U3NjA5YzAifV19) |
| `java-security` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJqYXZhLXNlY3VyaXR5IiwidmVyc2lvbiI6IjAuNy4wIiwic2hhMjU2IjoiMmIxYTg1ODgxOTQ2ZTFiYjY1MTU3MTgzMmM0YmVkNDdmM2YyMmU2MjU1NzU5ZWQyMWQzYTE3MGRlYzk3MDA1OCJ9XX0) |
| `limit-diff-size` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJsaW1pdC1kaWZmLXNpemUiLCJ2ZXJzaW9uIjoiMC4wLjMiLCJzaGEyNTYiOiJhN2U1YWRkNjZhNTcwNmEwODg5ODQ3NjY5NWZlMmMzMGMwNWUwNmU0YmY4MGMwYjRjNjcyMmNkMjg5ZTU4YWY0In1dfQ) |
| `memory-discipline` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJtZW1vcnktZGlzY2lwbGluZSIsInZlcnNpb24iOiIwLjAuNSIsInNoYTI1NiI6IjBmN2E2NjdhMDdlOGY1YjljOTZlYWQ2Zjk2ZGY3YTJlOWU5ODljM2MwZWM3NzZhYmY4M2EwN2ZkZGM2OTU1ZjgifV19) |
| `no-a11y-regression` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJuby1hMTF5LXJlZ3Jlc3Npb24iLCJ2ZXJzaW9uIjoiMS4xLjIiLCJzaGEyNTYiOiIyMWM0N2NmYWVhOGQ5ZjdkNjFlOWY0ZDlkOWIxMjdiNzM3MTUzNTYxM2E1ODhlMWM1ZTc0NDM2MDEzZDBiMWRiIn1dfQ) |
| `pin-github-actions` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJwaW4tZ2l0aHViLWFjdGlvbnMiLCJ2ZXJzaW9uIjoiMC4wLjciLCJzaGEyNTYiOiIxNjhkNmU5NDYzMjQ5MzA5YWJkNDAyMmFkYzE3NmI4ZDk3YWIxZmRiMTZmNGNhNTk2N2Y1YzRjYTg1ODU2OWVjIn1dfQ) |
| `protect-agent-config` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJwcm90ZWN0LWFnZW50LWNvbmZpZyIsInZlcnNpb24iOiIwLjcuMSIsInNoYTI1NiI6IjE5NjQ0NTc1MzY1ZjI1NWE2MTIzNDU0MmM2NzZiM2JkZjAyOGJmZDIwYTdjMjA1NzNhYzIzZDMwYjU5MDg3YTEifV19) |
| `protect-ci-workflows` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJwcm90ZWN0LWNpLXdvcmtmbG93cyIsInZlcnNpb24iOiIwLjIuNiIsInNoYTI1NiI6IjBlNTBjZTk3OGZiYTc5OWYzZGU2ZWJhNTI0ZmQyMGIxY2U4NmFkZDljOWMxZjRiZDYxOWNjOTRmZDhiM2YwMzcifV19) |
| `protect-commit-privacy` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJwcm90ZWN0LWNvbW1pdC1wcml2YWN5IiwidmVyc2lvbiI6IjAuMi41Iiwic2hhMjU2IjoiMzMxNDI5NWJhZTFhNGI3NWQ1MDNkOTc3Yzg0NDllODk4YTgxNTkxYzVjYTFlOTg5NjhjOWY3ODk5MDJmYzcwMSJ9XX0) |
| `protect-main-branch` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJwcm90ZWN0LW1haW4tYnJhbmNoIiwidmVyc2lvbiI6IjAuMC4zIiwic2hhMjU2IjoiZWJhMTY0ZDA5YzIyYjQ2ZjVjMmQyNjU2NjA3ZDczNTk2ZDNiMGNlNGM3NzgxODczN2VmYzUyNzJkNzg3MTllZSJ9XX0) |
| `protect-test-integrity` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJwcm90ZWN0LXRlc3QtaW50ZWdyaXR5IiwidmVyc2lvbiI6IjAuMC4zIiwic2hhMjU2IjoiZTUzNjBkNmU5MzIwNTRjYjZlMzdlNzdlZGEzYmU2ZTgxZWQ5MDAwZmY1NDFiMGJkZDNmMmVlOTI1NWQyZTkyZiJ9XX0) |
| `rtk-dangerous-actions-blocker` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJydGstZGFuZ2Vyb3VzLWFjdGlvbnMtYmxvY2tlciIsInZlcnNpb24iOiIwLjMuMCIsInNoYTI1NiI6IjE0OTA5Nzk2NmQyYzhiZGIyZTE1N2E0NGIzZjg3YmE5NGViOTViNGFjN2Y3MjQyNDBlNTI1MmRkNTZmNDhlYjEifV19) |
| `scan-secrets` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJzY2FuLXNlY3JldHMiLCJ2ZXJzaW9uIjoiMC4xLjIiLCJzaGEyNTYiOiI4MGMyN2I2Y2UyZjE0NTcxYjFjMmExZmEyM2U3ZjcxZTMwNWNhM2UyMWY3NjBiMzU1NzUwOWRmZDQzMDU2MGQwIn1dfQ) |
| `token-efficiency` | advisory | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJ0b2tlbi1lZmZpY2llbmN5IiwidmVyc2lvbiI6IjAuMS4xIiwic2hhMjU2IjoiMTkwMDNmYWZhYTJhNDQ4MzZkYTU0ODZmOWY1MjhhNjdhNzVjNDRiMmI0OTIyZTI2NDQ1MmU1OTI5YjVhMWM3NiJ9XX0) |
| `verify-dependency-exists` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJ2ZXJpZnktZGVwZW5kZW5jeS1leGlzdHMiLCJ2ZXJzaW9uIjoiMC4yLjAiLCJzaGEyNTYiOiJhOTg2M2ZmNDhlMjQ5ZjA3YTYwYTU2ZjJiNGNlYmFjZjI0YjdjNTQxMTczYzkwOWJlODRmNmY2OTkxNzg4OTU1In1dfQ) |
| `verify-mcp-allowlist` | enforces | [open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImNvcGlsb3QiLCJidW5kbGUiOnsidmVyc2lvbiI6IjEuMC4wIn0sImNhdGFsb2ciOnsic291cmNlIjoiaHR0cHM6Ly9naXRodWIuY29tL29wZW4tY29kZXItYWkvY2hvY2stY2F0YWxvZyIsInJlZiI6ImU4ODVmOTE4YTMzNWUyMGRmZGRiNGZhZDAzMzFkNGQ1YjRjNWViNDMifSwicG9saWNpZXMiOlt7ImZyb20iOiJjYXRhbG9nIiwiaWQiOiJ2ZXJpZnktbWNwLWFsbG93bGlzdCIsInZlcnNpb24iOiIwLjUuMyIsInNoYTI1NiI6IjA0MjE2ODg5YTYwMTJiMmU1ODIyZGFiODFkZjU4NjM2YTA3YjQ4N2MyMGQxZmZiODAyM2I3MGUxOGI0Y2UxYTQifV19) |

## Generated from chock-catalog

Every file here is compiled from policy sources in
[chock-catalog](https://github.com/open-coder-ai/chock-catalog) by
[chock](https://github.com/open-coder-ai/chock). Pull requests against this repository are
closed automatically — open them against the catalog instead.

- **Generated only:** CI regenerates from the pinned catalog and fails on any difference.
- **Byte-identical guards:** each guard script is a verbatim copy of its policy's source in the
  catalog, and the hook adapter a verbatim copy of its framework source.
- **Best-effort, not a boundary:** guards are pattern-based filters; see
  [SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md).
- **Tested upstream, and gated:** every policy ships an eval suite
  (`base/<policy>/evals/suite.yaml`) in the catalog, and the publish workflow runs
  `chock check` and `chock check --only evals` before packaging anything — a policy whose
  evals fail cannot reach this repository. The tests live in the catalog because the policy
  source does; this repository is compiled output.
- This README is hand-written, as are `SECURITY.md` and the workflows under `.github/`, so they
  sit outside the generated-only guarantee.

### Verify it yourself

Nothing above asks for trust that cannot be checked. This rebuilds the published tree from
source and compares it with what is committed here:

```bash
git clone https://github.com/open-coder-ai/chock-copilot-plugins dist
git clone https://github.com/open-coder-ai/chock-catalog catalog
git clone --branch "$(tr -d '[:space:]' < catalog/.framework-ref)" \
  https://github.com/open-coder-ai/chock framework
pip install ./framework
chock plugin build --repo catalog --policies-dir base --format agent-plugins --out-dir dist
chock plugin build --repo catalog --policies-dir base --format claude --out-dir dist
chock plugin build --repo catalog --policies-dir base --format copilot --out-dir dist
chock marketplace build --dist dist
git -C dist diff --exit-code && git -C dist status --porcelain
```

Silence from both `git` commands means this repository is byte-identical to a fresh build
from the catalog. The framework ref comes from the catalog's own `.framework-ref`, which is
what the publish and Generated-only workflows read, so this recipe cannot drift from the
release a tree was actually built with.
`chock-market.lock` records a sha256 per published plugin directory, so one package can be
checked without rebuilding the rest.

**If you are listing these plugins in a marketplace,** pin both a tag and the full commit
SHA. The tag names the release; the SHA is what holds the reviewed bytes still.

## Contributing

| You want to | Go to |
| :--- | :--- |
| Fix or add a policy | [chock-catalog](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md) — it reaches every client from there, including this one |
| Report that a guard did or did not block on your Copilot CLI or VS Code version | an issue on [chock](https://github.com/open-coder-ai/chock/issues/new/choose), which records what each package claims. No package here carries a witnessed block yet — the claims are read from vendor documentation — so a first-hand "it blocked" or "it fails open where you say it fails closed" is the most useful result you can send |
| Report a bug in how packages are generated | [chock](https://github.com/open-coder-ai/chock/issues/new/choose), where the emitter lives |
| Fix this README | here — it is hand-written, not generated |

## Part of open-coder-ai

| | |
|---|---|
| [agentseam](https://github.com/open-coder-ai/agentseam) | the primitives — one handler API and a verified capability matrix across 16 agents |
| [chock](https://github.com/open-coder-ai/chock) | the compiler — one policy into git hooks, CI gates and native pre-tool hooks |
| [chock-catalog](https://github.com/open-coder-ai/chock-catalog) | the policies — 42, each labelled enforced or advisory, with replayed evals |
| [context-report](https://github.com/open-coder-ai/context-report) | the evidence — a signed report of whether an agent artifact actually works |
| [chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel) | the threat ledger the catalog's policies answer to |
| chock-{claude,cursor,copilot,codex}-plugins | the catalog, packaged for each agent's plugin format (generated) |
| chock-quickstart · chock-example | template repos: what `chock init` leaves behind, and a full adoption |

## License

Apache-2.0, same as the framework and the catalog.
