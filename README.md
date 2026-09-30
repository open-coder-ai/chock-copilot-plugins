<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/logo.svg" alt="chock logo" width="110">
</p>

<h1 align="center">chock-copilot-plugins</h1>

<p align="center"><strong>Chock's security guardrails for Copilot CLI and VS Code agent mode — a real <code>PreToolUse</code> deny hook where the client supports it.</strong></p>

<p align="center">

[![Generated-only](https://github.com/open-coder-ai/chock-copilot-plugins/actions/workflows/generated-only.yml/badge.svg)](https://github.com/open-coder-ai/chock-copilot-plugins/actions/workflows/generated-only.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![plugins](https://img.shields.io/badge/plugins-26-D9B45C?labelColor=0D1626)](PLUGINS.md)
[![deny hooks in Copilot](https://img.shields.io/badge/deny_hooks-14-D9B45C?labelColor=0D1626)](#what-these-plugins-refuse)
[![security policies in the catalog](https://img.shields.io/badge/security_policies-48-D9B45C?labelColor=0D1626)](https://github.com/open-coder-ai/chock-catalog)
[![eval cases](https://img.shields.io/badge/eval_cases-1%2C179-D9B45C?labelColor=0D1626)](https://github.com/open-coder-ai/chock-catalog)
[![OWASP Agentic Top 10](https://img.shields.io/badge/OWASP_Agentic_Top_10-10%2F10-D9B45C?labelColor=0D1626)](https://github.com/open-coder-ai/chock-catalog)
[![Contribute upstream](https://img.shields.io/badge/contribute-chock--catalog-8957e5)](https://github.com/open-coder-ai/chock-catalog)

</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/demo.gif" alt="Terminal: five security guards adopted from the catalog; a hard-coded AWS key, an MCP server at @latest, a wildcard IAM grant, model output piped into os.system and a Trojan Source bidi override are each refused at commit; the fixed file commits cleanly." width="760">
</p>

<p align="center"><sub>The demo is Chock installed in a repository, refusing at commit. The packages here are the
other half — the same policies inside GitHub Copilot CLI and VS Code agent mode, refusing at the agent's own hooks. Of the five
guards shown, <code>scan-secrets</code> and <code>block-invisible-unicode</code> are packaged here.</sub></p>

**Your coding agent has a shell, your git history and your cloud credentials.** Chock refuses the
dangerous action before it lands — as a git hook, a CI gate, or the agent's own pre-tool hook.
This repository is the third of those, packaged for GitHub Copilot CLI and VS Code agent mode. A rule an agent reads is
advice; a hook that refuses the call is a control. Guardrails, not guarantees.

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

## What these plugins refuse

**14 of the 26 packages here ship a real deny hook for Copilot; 12 are advisory. No package here carries a witnessed block yet — the claims are read from vendor documentation.** Every package states its posture in this client in its own description, and
[PLUGINS.md](PLUGINS.md) lists all 26 with versions.

- **guard** — `PreToolUse` on the shell tool: the command is judged and denied before it runs.
- **gate** — judges what a turn writes: `Stop` in the `copilot/` tree; the `claude/` tree adds
  `PreToolUse` on the write tools.

| Area | What gets refused | Plugin | In Copilot | Evals |
| :--- | :--- | :--- | :--- | ---: |
| **Secrets &amp; data leakage** | Hard-coded credentials: vendor key prefixes, private-key blocks, key/token/password assignments in files the agent writes | [`scan-secrets`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/scan-secrets/README.md) | ✓ gate | 32 |
|  | A `git commit` whose message narrates the agent conversation into permanent history | [`protect-commit-privacy`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/protect-commit-privacy/README.md) | ✓ guard | 35 |
|  | `curl` / `wget` / `Invoke-WebRequest` <b>uploading</b> data (POST/PUT, `--data`, `--upload-file`) to a host outside the egress allowlist | [`block-unapproved-egress`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/block-unapproved-egress/README.md) | ✓ guard | 49 |
| **Destructive commands &amp; hook bypass** | `rm -rf` on absolute/home/root paths, `git push --force`, `git reset --hard`, `terraform destroy`, `kubectl delete`, `helm uninstall`, `aws s3 rm --recursive`, `dropdb` | [`block-destructive-commands`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/block-destructive-commands/README.md) | ✓ guard | 80 |
|  | rtk's decision table: credential-file reads (`.env`, `id_rsa`, `~/.aws`), `DROP` / `TRUNCATE` / unscoped `DELETE` via `psql`, `mysql`, `sqlite3`, `mongosh`, `redis-cli`; `+refspec` force-pushes | [`rtk-dangerous-actions-blocker`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/rtk-dangerous-actions-blocker/README.md) | ✓ guard | 92 |
|  | `git commit/push --no-verify`, `commit -n`, `-c core.hooksPath=` overrides | [`block-no-verify`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/block-no-verify/README.md) | ✓ guard | 74 |
|  | `curl … \| sh`, `bash -c "$(curl …)"`, `bash <(curl …)`, `iwr … \| iex` | [`block-curl-pipe-sh`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/block-curl-pipe-sh/README.md) | ✓ guard | 34 |
| **Agent self-protection &amp; excessive agency** | Shell writes to the agent's own guardrails: `AGENTS.md` and wrappers, `.claude/settings.json`, `.mcp.json`, `.chock/` (MITRE ATLAS AML.T0081) | [`protect-agent-config`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/protect-agent-config/README.md) | ✓ guard | 79 |
|  | Shell writes that rewrite or delete `.github/workflows/`, `.github/actions/` or `dependabot.yml` — the checks that review the agent's work | [`protect-ci-workflows`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/protect-ci-workflows/README.md) | ✓ guard | 36 |
|  | An agent settings file granting a bare-wildcard shell grant or allow-list | [`block-wildcard-agent-permissions`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/block-wildcard-agent-permissions/README.md) | ✓ gate | 17 |
|  | A shell write to `.mcp.json` adding an MCP server not on the name + source allowlist, or re-pointing an allowed one | [`verify-mcp-allowlist`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/verify-mcp-allowlist/README.md) | ✓ guard | 65 |
| **Supply chain** | A workflow using a third-party action by tag or branch instead of a full 40-character commit SHA | [`pin-github-actions`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/pin-github-actions/README.md) | ✓ gate | 17 |
| **Prompt injection** | Bidi override/embed/isolate controls (Trojan Source, CVE-2021-42574) and Unicode tag-block smuggling; ZWJ and bidi marks in real text still pass | [`block-invisible-unicode`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/block-invisible-unicode/README.md) | ✓ gate | 14 |
| **Secure code: Java** | `${}` in MyBatis SQL, unescaped template output, unsafe deserialization, wildcard CORS with credentials, wildcard actuator exposure, unverified JWT parse, request-chosen file paths — each rule allow · deny · ask in `.chock/security.json` | [`java-security`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/java-security/README.md) | ✓ gate | 115 |

<sub>Evals: cases in the policy's current suite in [chock-catalog](https://github.com/open-coder-ai/chock-catalog),
replayed in CI; the version packaged here is in [PLUGINS.md](PLUGINS.md) and can trail the
catalog. `java-security` 0.3.0 ships the rules its description lists; the catalog's current
release has grown to 129 rules in 16 packs.</sub>

<details>
<summary><b>12 advisory plugins</b> — skill text the agent reads; they shape behaviour but nothing stops a violation</summary>

| Area | Steers the agent away from | Plugin |
| :--- | :--- | :--- |
| Destructive commands &amp; hook bypass | Direct commits and pushes to `main` / `master` | [`protect-main-branch`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/protect-main-branch/README.md) |
| Destructive commands &amp; hook bypass | Force push, hard reset, destructive branch delete, hook bypass, direct main commits | [`git-safety`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/git-safety/README.md) |
| Supply chain | Hallucinated or unknown dependencies (opt-in; needs a curated allowlist) | [`verify-dependency-exists`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/verify-dependency-exists/README.md) |
| Prompt injection | Instructions found in tool output, fetched content and files — treated as data, never commands | [`injection-defense`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/injection-defense/README.md) |
| Secure code | Secrets, `eval`/`exec`, unsanitized SQL, hallucinated dependencies | [`code-safety`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/code-safety/README.md) |
| Accessibility | Retracting an accessible name an element already had (alt emptied, aria-label removed…) — ADA / WCAG | [`no-a11y-regression`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/no-a11y-regression/README.md) |

Agent hygiene: [`agent-discipline`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/agent-discipline/README.md) · [`context-hygiene`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/context-hygiene/README.md) · [`memory-discipline`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/memory-discipline/README.md) · [`token-efficiency`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/token-efficiency/README.md) · [`chock-mise`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/chock-mise/README.md) · [`firecrawl-fallback-only`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/firecrawl-fallback-only/README.md).

`protect-main-branch`, `verify-dependency-exists` and `no-a11y-regression` are enforced at
commit once Chock is installed in the repository; as plugins they are advice.
</details>

<details>
<summary><b>In the catalog, not packaged here</b></summary>

`guard-memory-writes`, `block-unguarded-agent-spawn`, `block-unpinned-agent-components`, `agentic-code-security`, `block-unsafe-code-execution`, `block-wildcard-iam`, `protect-test-integrity`, `block-test-skips`, the ten OWASP Agentic Top 10 policies (`owasp-asi01` … `asi10`) and the EU AI Act policies live in [chock-catalog](https://github.com/open-coder-ai/chock-catalog) but are not
in this repository's packages. To run them, install Chock in the repository:
`pip install chock && chock init && chock sync --ci`.
</details>

## How a refusal works in Copilot

Two kinds of package enforce here, and they enforce differently.

**Guard policies** ship a `PreToolUse` hook that judges a shell command before it runs. The
adapter always exits 0 and carries its verdict as JSON on stdout (`permissionDecision: "deny"`),
so the refusal is the client reading that answer, not an exit status.

**Gate policies** judge what a turn writes rather than what it runs. In the `copilot/` tree
they are wired at `Stop`, re-reading what the turn left on disk; the `claude/` tree adds
`PreToolUse` on the write tools, judging the file a write would create.

Both need `python3` on PATH: without it a fail-open client allows silently. A guard that
crashes is handled per that client's description; a gate that cannot reach a decision refuses
rather than allowing something it never judged. Advisory policies are a skill the client reads;
they shape behaviour but cannot block anything on their own. See **[PLUGINS.md](PLUGINS.md)** for the full list: each
policy, its version, whether it enforces or advises in this client, and a catalog link.

A plugin governs one person's session in one client; it doesn't run in CI or travel with a
clone. For enforcement that follows the repository instead, install Chock directly:
`pip install chock && chock init && chock sync --ci`.

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

## Part of open-coder-ai

| | |
|---|---|
| [agentseam](https://github.com/open-coder-ai/agentseam) | the primitives — one handler API and a verified capability matrix across 16 agents |
| [chock](https://github.com/open-coder-ai/chock) | the compiler — one policy into git hooks, CI gates and native pre-tool hooks |
| [chock-catalog](https://github.com/open-coder-ai/chock-catalog) | the policies — 48, each labelled with its enforcement tier, with 1,179 replayed eval cases |
| [context-report](https://github.com/open-coder-ai/context-report) | the evidence — a signed report of whether an agent artifact actually works |
| [chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel) | the threat ledger the catalog's policies answer to |
| chock-{[claude](https://github.com/open-coder-ai/chock-claude-plugins) · **copilot** · [cursor](https://github.com/open-coder-ai/chock-cursor-plugins) · [codex](https://github.com/open-coder-ai/chock-codex-plugins) · [devin](https://github.com/open-coder-ai/chock-devin-plugins)}-plugins | the catalog, packaged for each agent's plugin format (generated) — you are in **chock-copilot-plugins** |
| chock-quickstart · chock-example | template repos: what `chock init` leaves behind, and a full adoption |

## Contributing

Contributions land upstream and reach every client from there, this one included.

| You want to | Go to |
| :--- | :--- |
| Report a bypass you found | add a failing eval case to the policy's `evals/suite.yaml` in [chock-catalog](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md) — a bypass that becomes a case stays fixed |
| Fix or add a policy | [chock-catalog](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md) — `chock new policy <id>` scaffolds one; it reaches every client from there, including this one |
| Report that a guard did or did not block on your Copilot CLI or VS Code version | an issue on [chock](https://github.com/open-coder-ai/chock/issues/new/choose), which records what each package claims. No package here carries a witnessed block yet — the claims are read from vendor documentation — so a first-hand "it blocked" or "it fails open where you say it fails closed" is the most useful result you can send |
| Teach Chock a new agent | an adapter in [agentseam](https://github.com/open-coder-ai/agentseam), the handler API every client here is built on |
| Report a bug in how packages are generated | [chock](https://github.com/open-coder-ai/chock/issues/new/choose), where the emitter lives |
| Report a vulnerability | [SECURITY.md](SECURITY.md) — the private advisory route, never a public issue |
| Fix this README | here — it is hand-written, not generated |

Upstream commits carry a DCO sign-off (`git commit -s`); the catalog's
[CONTRIBUTING.md](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md) has the details.

## License

Apache-2.0, same as the framework and the catalog.
