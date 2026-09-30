---
name: protect-commit-privacy
description: "Keep the development conversation out of git history. Agent-authored commits narrate by default -- who asked for what, which discussion decided it, what the plan was -- and on a public repo that narration is published forever. The guard refuses git commit commands whose message (inline -m/--message or the file behind -F/--file) contains process-leak markers; the rule tells the agent to describe the change, not the conversation, and to propose sensitive messages to the human before committing. The command line is parsed, so a commit or gh pr create|edit behind cd, sh -c/bash -c, sudo, env or command is read like a bare one, a message fed on `-F -` from a heredoc is scanned, and a command that merely echoes the pattern as documentation still passes. A commit-msg hook applies the same markers to the message git records. Best-effort: markers are a narrow deny-list, and a message the human explicitly approves can say anything -- edit the marker list in the guard, the content is yours."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Commit Privacy

Keep the development conversation out of git history. Agent-authored commits narrate by default -- who asked for what, which discussion decided it, what the plan was -- and on a public repo that narration is published forever. The guard refuses git commit commands whose message (inline -m/--message or the file behind -F/--file) contains process-leak markers; the rule tells the agent to describe the change, not the conversation, and to propose sensitive messages to the human before committing. The command line is parsed, so a commit or gh pr create|edit behind cd, sh -c/bash -c, sudo, env or command is read like a bare one, a message fed on `-F -` from a heredoc is scanned, and a command that merely echoes the pattern as documentation still passes. A commit-msg hook applies the same markers to the message git records. Best-effort: markers are a narrow deny-list, and a message the human explicitly approves can say anything -- edit the marker list in the guard, the content is yours.

```
commit_message|pr_description: describe(change); never(narrate: conversation|plan|who_asked|user_quotes|session_refs|internal_doc_paths)
if(sensitive_context): propose_message_to_human; await(approval) before(commit)  # history is published forever
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives; a client that exports no plugin-root variable runs the hook, which then allows -- so treat this package as advisory unless a deny has been witnessed in your own client. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
