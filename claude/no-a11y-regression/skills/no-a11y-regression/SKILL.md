---
name: no-a11y-regression
description: "trigger: remediating accessibility, editing markup, emptying or removing an alt, aria-label, label or lang that an element already had, deleting a flagged element. avoid: breaking a requirement the previous revision met; interrupting a correct fix. Misses: a name lost while an element moves to another file, and a new element or a supplied name that is weak, which warn and do not block. Not production-grade: caught 23 of 51 regressions and refused 17 of 58 correct changes in the 2026-11-15 hardening review; known gaps are framework attribute syntax (Angular, Vue, Thymeleaf, JSP, ERB, Pug, Lit), icon-only controls that lose an aria-label, and an alt removed in some forms."
metadata:
  chock.artifact: rule
  chock.enforcement: block
  chock.hooks: hooks/hooks.json
---

# Keep Accessibility From Regressing

trigger: remediating accessibility, editing markup, emptying or removing an alt, aria-label, label or lang that an element already had, deleting a flagged element. avoid: breaking a requirement the previous revision met; interrupting a correct fix. Misses: a name lost while an element moves to another file, and a new element or a supplied name that is weak, which warn and do not block. Not production-grade: caught 23 of 51 regressions and refused 17 of 58 correct changes in the 2026-11-15 hardening review; known gaps are framework attribute syntax (Angular, Vue, Thymeleaf, JSP, ERB, Pug, Lit), icon-only controls that lose an aria-label, and an alt removed in some forms.

```
never(break): name|lang an element already had -- remove, empty(alt=""), aria-hidden, role=presentation|none; never(resolve_violation_by): delete(element)
on(name_added): record, never_ask; alt="" asserts decorative and only its author may retract a description; present -> present (reworded label) is a copy decision, stay silent
```

This policy is enforced in this client by the PreToolUse and Stop hooks installed with the plugin, subject to the fail conditions stated in the plugin description. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
