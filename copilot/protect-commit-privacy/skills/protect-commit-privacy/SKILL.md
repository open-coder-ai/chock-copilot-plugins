---
name: protect-commit-privacy
description: "Keeps the development conversation out of git history. Guard refuses `git commit` and `gh pr create|edit` whose message or body (inline -m/-b, -F/--file/--body-file, heredoc on -F -) holds a process-leak marker (session link, 'user asked', ...); commands behind cd, sh -c, sudo, env are read. A commit-msg hook applies the same markers to the recorded message. Narrow deny-list. No waiver: a legitimate phrase needs a person to remove it from MARKERS in the guard; an agent asks the person."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# Protect Commit Privacy

Keeps the development conversation out of git history. Guard refuses `git commit` and `gh pr create|edit` whose message or body (inline -m/-b, -F/--file/--body-file, heredoc on -F -) holds a process-leak marker (session link, 'user asked', ...); commands behind cd, sh -c, sudo, env are read. A commit-msg hook applies the same markers to the recorded message. Narrow deny-list. No waiver: a legitimate phrase needs a person to remove it from MARKERS in the guard; an agent asks the person.

```
commit_message|pr_description: describe(change); never(narrate: conversation|plan|who_asked|user_quotes|session_refs|internal_doc_paths)
if(marker_hit|sensitive_context): ask_person before(commit); no_waiver(person removes phrase from MARKERS); never(edit MARKERS)  # history is published forever
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives. A client that exports no plugin-root variable (VS Code's agent-plugin format exports none) runs the hook, which then exits 0 and checks nothing; a root that is set but lacks the bundled hook exits 2 and refuses the call. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
