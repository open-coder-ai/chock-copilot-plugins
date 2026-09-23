---
name: rtk-dangerous-actions-blocker
description: "Carved out for rtk-ai/rtk#1007: rtk's own decision table as a chock policy (rtk's own hooks may not block). Refuses rm -rf on root, home, parent or absolute paths; git push --force and '+' refspecs (not --force-with-lease); reads of credential files (.env, *.pem, *.key, id_rsa, ~/.ssh, ~/.aws) and echoes or inline literals of *_API_KEY/*_SECRET/*_TOKEN; DROP/TRUNCATE/unscoped DELETE through psql, mysql, sqlite3, mongosh, redis-cli, and dropdb; plus the base policy's cloud rows (kubectl delete, terraform destroy, aws s3 rm --recursive, helm uninstall, gcloud delete, docker volume rm). Asks where rtk's table says ask: rm -rf on a relative path off the safe list; git reset --hard, clean -f, checkout ., branch -D; docker prune and docker rm -f over a substitution. File checks are skipped inside docker/kubectl exec, as rtk specifies; rtk is a transparent prefix. Bypasses: aliases, quoting, indirection, sourced files, interpreters, indirect scripts. This is friction, not a security boundary."
metadata:
  chock.artifact: rule
  chock.enforcement: advise
  chock.hooks: com.github.copilot/hooks/hooks.json
---

# rtk Dangerous Actions Blocker

Carved out for rtk-ai/rtk#1007: rtk's own decision table as a chock policy (rtk's own hooks may not block). Refuses rm -rf on root, home, parent or absolute paths; git push --force and '+' refspecs (not --force-with-lease); reads of credential files (.env, *.pem, *.key, id_rsa, ~/.ssh, ~/.aws) and echoes or inline literals of *_API_KEY/*_SECRET/*_TOKEN; DROP/TRUNCATE/unscoped DELETE through psql, mysql, sqlite3, mongosh, redis-cli, and dropdb; plus the base policy's cloud rows (kubectl delete, terraform destroy, aws s3 rm --recursive, helm uninstall, gcloud delete, docker volume rm). Asks where rtk's table says ask: rm -rf on a relative path off the safe list; git reset --hard, clean -f, checkout ., branch -D; docker prune and docker rm -f over a substitution. File checks are skipped inside docker/kubectl exec, as rtk specifies; rtk is a transparent prefix. Bypasses: aliases, quoting, indirection, sourced files, interpreters, indirect scripts. This is friction, not a security boundary.

```
block: rm_-rf(/|~|..|abs), git_push(--force|+ref), read(.env|*.pem|*.key|id_rsa|~/.ssh|~/.aws), echo|inline($*_API_KEY|$*_SECRET|$*_TOKEN), sql(DROP|TRUNCATE|DELETE_no_WHERE)|dropdb|FLUSHALL, kubectl_delete|terraform_destroy|aws_s3_rm_-r|helm_uninstall|gcloud_delete|docker_volume_rm
ask: rm_-rf(relative, off safe_list), git(reset_--hard|clean_-f|checkout_.|branch_-D), docker_*_prune|docker_rm_-f_$(..); skip_inside: docker|kubectl_exec; prefix: rtk; prefer: force-with-lease|stash|dry-run
```

This package ships a PreToolUse hook under com.github.copilot/. It enforces only in a client that both reads that namespace AND tells the hook where the package lives; a client that exports no plugin-root variable runs the hook, which then allows -- so treat this package as advisory unless a deny has been witnessed in your own client. A client that ignores the namespace gets this text only. Repo-wide enforcement across every commit and in CI still needs `chock sync`. See https://github.com/open-coder-ai/chock
