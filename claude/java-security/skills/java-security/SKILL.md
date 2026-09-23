---
name: java-security
description: "trigger: writing Java or Spring code, MyBatis mappers, JSP, Thymeleaf or FreeMarker templates, application.properties or .yml; \"customize java security\" opens this skill's guided page. avoid: string-interpolated SQL, unescaped template output, unsafe deserialization, wildcard CORS with credentials, wildcard actuator exposure, an unverified JWT parse, a request-chosen file path, an ObjectInputStream over request bytes. Eight rules, each allow|deny|ask in .chock/security.json; absent = deny."
metadata:
  chock.artifact: hook
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Java Security Rules

trigger: writing Java or Spring code, MyBatis mappers, JSP, Thymeleaf or FreeMarker templates, application.properties or .yml; "customize java security" opens this skill's guided page. avoid: string-interpolated SQL, unescaped template output, unsafe deserialization, wildcard CORS with credentials, wildcard actuator exposure, an unverified JWT parse, a request-chosen file path, an ObjectInputStream over request bytes. Eight rules, each allow|deny|ask in .chock/security.json; absent = deny.

```
on(commit|tool_use): block(script) script=java-security-gate.py
java-security: a Java construct a rule denies -- ${} in MyBatis SQL, unescaped template output, unsafe deserialization, a wildcard CORS origin with credentials, wildcard actuator exposure, an unverified JWT parse, a request-chosen file path, an ObjectInputStream over request bytes. Each rule's verdict is allow|deny|ask in .chock/security.json (absent = deny); waive one line with // chock: allow <rule-id>; choose per rule by asking to customize java security, which opens this skill's guided page.
```

## Guided setup

Asked to customize, configure, set up, review or change these rules -- "customize java
security" and anything meaning it -- open the guided page rather than asking the questions
as prose. Open it unasked, once, when Java is about to be written and no selection file
exists at either scope: every rule denies until someone chooses, and the page is a better
first meeting than the refusal. It is `setup.html`, in this skill's own directory beside this file and
`references/`. It is offline and writes nothing itself; deny is preselected for every rule,
and a verdict is chosen, never derived from a question about the stack.

Where this client can publish an Artifact, publish that file as one, declaring
`capabilities: {db: {}}`, and let the person walk it in the panel. Their Submit writes the
result to the artifact's own store at `selection/current`; read that document back.
Everywhere else, open the page in a browser and take the result from their clipboard.

`result.selection` is the whole file and `result.wiring.scope` says where it goes:

- `repo`: `.chock/security.json` at the repository root, committed; where chock is
  installed there, `chock sync --repo .` afterwards.
- `user`: `~/.chock/security.json`, the floor for work outside a repository that carries its
  own. A repository carrying `.chock/security.json` governs itself: refuse the user-scope
  write, say so, and offer to change the committed file in a pull request.

Show the person one row per rule before writing, and overwrite an existing selection only
after they have seen the diff. Never write a pattern, severity or path into the file: it
carries verdicts only, and the gate refuses anything else at the next write. Reach for the
text walk -- `references/setup-contract.json`, one rule at a time, deny unless told
otherwise -- only where the page cannot be shown at all.

Wiring is not this skill's. Installed as a plugin, the hooks beside this file judge each
write and the turn's end; in a repository, `chock sync` adds the commit hook. Both read the
same selection file.

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
