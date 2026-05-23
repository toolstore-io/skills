---
name: forgejo
description: Use when interacting with a Forgejo remote — `fj` CLI commands, opening or commenting on PRs and issues, replying to review threads, SSH attribution for agent vs. human commits, or anything involving toolstore.dev. Triggers on "fj", "forgejo", "open a PR", "comment on the review". Skip for GitHub or GitLab remotes.
---

# Forgejo

Forgejo is managed with the `fj` CLI. Do not use `gh` (GitHub CLI) for Forgejo remotes.

## Identity preflight (mandatory before any write action)

Before posting any comment, review, or PR body:

```bash
fj -H <host> whoami
```

Expected result: the agent account (e.g. `rock_raider`). If the wrong identity is active, stop and report before continuing. Never derive auth from `git remote` URLs.

If required scopes are missing for comment or review writes, stop and report rather than attempting the action.

## SSH transport

Use SSH remotes only. Never use HTTPS with embedded credentials. Use SSH host aliases from `~/.ssh/config` for attribution-safe pushes:

- `forgejo-marcus` — primary key, for user-authored commits
- `forgejo-rock-raider` — agent key, for agent-authored commits and pushes

Agent commits and pushes must use `forgejo-rock-raider`.

## PR and issue body content

Always use `--body-file` for PR bodies and issue/PR comments. Never pass multi-line content as an inline shell argument.

```bash
cat > /tmp/pr-body.md << 'EOF'
## Summary
- Fixed X
- Addressed Y
EOF

fj pr create --body-file /tmp/pr-body.md
fj pr edit <pr> --body-file /tmp/pr-body.md
fj pr comment <pr> --body-file /tmp/pr-body.md
fj issue comment <issue> --body-file /tmp/pr-body.md
```

No literal `\n` escape sequences in message text — use real plaintext newlines via file or stdin only.

## Unfamiliar commands

Run help before using an unfamiliar command group to avoid syntax churn:

```bash
fj pr --help
fj pr review --help
fj issue comment --help
```

## PR review workflow

`fj` (v0.4.x) does not expose first-class commands for threaded review replies. Use the Forgejo REST API directly:

```bash
# List reviews on a PR
curl -sS -H "Authorization: token $FORGEJO_TOKEN" \
  "$FORGEJO_URL/api/v1/repos/$OWNER/$REPO/pulls/$PR/reviews"

# List comments within a review
curl -sS -H "Authorization: token $FORGEJO_TOKEN" \
  "$FORGEJO_URL/api/v1/repos/$OWNER/$REPO/pulls/$PR/reviews/$REVIEW_ID/comments"

# Reply in the same review thread
curl -sS -X POST -H "Authorization: token $FORGEJO_TOKEN" \
  -H "Content-Type: application/json" \
  "$FORGEJO_URL/api/v1/repos/$OWNER/$REPO/pulls/$PR/reviews/$REVIEW_ID/comments" \
  -d '{
    "body": "Re: comment #123 — fixed by validating nil input.",
    "path": "src/parser.go",
    "new_position": 88
  }'
```

Reply to every actionable review comment unless explicitly told otherwise. Track each by `comment_id` and disposition (`fixed`, `answered`, `needs-follow-up`) before finishing.

## Multica integration

- Always include the Multica issue link in Forgejo PR descriptions.
- Pass `--project <id>` when creating Multica issues — issues without a project are untracked.
- After resolving PR feedback, post a concise Multica summary: addressed comment IDs, commit SHAs, and any unresolved follow-ups.

For Multica comments with multi-line or special-character content, use stdin:

```bash
cat <<'COMMENT' | multica issue comment add <issue-id> --content-stdin
Summary line

- Item 1
- Item 2
COMMENT
```
