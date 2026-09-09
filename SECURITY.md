# Security Policy

## What lives here, and what that means for a report

`chock-copilot-plugins` is **compiled output**. Every file under `copilot/`, `claude/` and `agent-plugins/`, along with the
marketplace index, `chock-market.lock` and `PLUGINS.md`, is generated from policy sources in
[chock-catalog](https://github.com/open-coder-ai/chock-catalog) by
[chock](https://github.com/open-coder-ai/chock), and the
[Generated-only](.github/workflows/generated-only.yml) check regenerates the tree on every
push and pull request and fails on any difference. There is no hand-written code in this
repository to hold a vulnerability of its own — a defect visible here was introduced either
in the emitter or in the policy it emitted, so a fix landed here would be overwritten by the
next publish. **Report it where it can actually be fixed:**

| What you found | Where it belongs |
|---|---|
| A defect in a guard script, hook wiring, plugin manifest, or anything about how policies are compiled into plugins | [open-coder-ai/chock](https://github.com/open-coder-ai/chock) — see its [SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md) |
| A defect in **policy content**: a guard that does not match what it claims to block, a pattern that can be trivially evaded, a policy whose description overstates its enforcement | [open-coder-ai/chock-catalog](https://github.com/open-coder-ai/chock-catalog) |
| This repository's tree does not match a rebuild from the catalog — i.e. something here was not published by the catalog | [chock](https://github.com/open-coder-ai/chock)'s private advisory route, as a supply-chain report against this repository |
| A defect in this repository's own workflows (`.github/workflows/`) | [chock](https://github.com/open-coder-ai/chock)'s private advisory route, naming this repository |

The last two are the only categories that are genuinely *this* repository's, and both are
about distribution integrity rather than about policy behaviour.

## Reporting a vulnerability

Use chock's private advisory route:
<https://github.com/open-coder-ai/chock/security/advisories/new>. Do **not** open a public
issue for an exploitable finding, here or upstream. Include the affected path, how to
reproduce it, and the impact. Acknowledgement and assessment follow the timelines stated in
[chock's SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md); this
repository does not set its own, and there is no PGP key — GitHub's advisory form is the
private channel.

Pull requests are closed here automatically with a pointer to the catalog. That applies to
security fixes too: a patch to a generated file cannot survive the next publish.

## Verifying what you installed

Two things are checkable without trusting this repository's README:

- **Every published plugin directory is hashed in `chock-market.lock`** (sha256 per
  directory), so a plugin's content can be compared against what the index claims.
- **The tree is reproducible.** Check out this repository, the catalog and chock as
  siblings, install chock from source, and run the same two build commands the
  [Generated-only](.github/workflows/generated-only.yml) workflow runs. `git diff` and
  `git status --porcelain` should both be silent. That workflow derives the framework
  version from the catalog's own `.framework-ref`, so a rebuild from the catalog ref you
  care about uses the emitter that catalog declares rather than whatever is on a branch.

## What these plugins do not promise

Stated here rather than left to the README, because a security file that omits it is
claiming more than the product does:

- A hook is enforcement **only where the host runs it**. Each plugin's description states
  its own fail posture, and several fail **open** — if the hook cannot run, the command is
  allowed. That is a property of the host agent, not a bug in the plugin.
- Skills and ambient rules are **advisory** in every client. They are text the model reads.
- Repository-level enforcement — git hooks and a CI gate, which apply with no agent running
  — is not part of an installed plugin. It comes from `chock sync` in the target
  repository.
