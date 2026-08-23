---
name: protect-commit-privacy
description: "Keep the development conversation out of git history. Agent-authored commits narrate by default -- who asked for what, which discussion decided it, what the plan was -- and on a public repo that narration is published forever. The guard refuses git commit commands whose message (inline -m/--message or the file behind -F/--file) contains process-leak markers; the rule tells the agent to describe the change, not the conversation, and to propose sensitive messages to the human before committing. Best-effort: markers are a narrow deny-list, and a message the human explicitly approves can say anything -- edit the marker list in the guard, the content is yours."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Commit Privacy

Keep the development conversation out of git history. Agent-authored commits narrate by default -- who asked for what, which discussion decided it, what the plan was -- and on a public repo that narration is published forever. The guard refuses git commit commands whose message (inline -m/--message or the file behind -F/--file) contains process-leak markers; the rule tells the agent to describe the change, not the conversation, and to propose sensitive messages to the human before committing. Best-effort: markers are a narrow deny-list, and a message the human explicitly approves can say anything -- edit the marker list in the guard, the content is yours.

```
commit_message|pr_description: describe(change); never(narrate: conversation|plan|who_asked|user_quotes|session_refs|internal_doc_paths)
if(sensitive_context): propose_message_to_human; await(approval) before(commit)  # history is published forever
```

This package ships a PreToolUse hook under com.github.copilot/ that enforces this policy in clients reading that namespace (documented for VS Code agent mode), subject to the fail posture stated in the plugin description. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
