---
name: multica
description: Use when creating Multica issues, posting comments via `multica issue comment add`, attaching files, replying to a trigger comment, or reporting work delivery. Triggers on "multica", "create an issue", "post a comment on MUL-", "delivery comment", `--project`. Skip for projects not using Multica.
---

# Multica

Multica handles issue tracking and agent delivery reporting. Keep Multica and Forgejo workflows separate — they solve different problems and have different CLI patterns.

## Issue creation

Always pass `--project <id>` when creating issues. Issues without a project are untracked and hard to triage.

```bash
multica issue create --title "..." --project <project-id> --content-stdin << 'EOF'
Description of the issue.
EOF
```

## Comment formatting

Use `--content-stdin` for any multi-line or special-character content. Never pass rich text, code, or multi-line strings as a double-quoted shell argument.

```bash
cat <<'COMMENT' | multica issue comment add <issue-id> --content-stdin
Summary line

- Item 1
- Item 2
COMMENT
```

Short single-line comments can use `--content` inline:

```bash
multica issue comment add <issue-id> --content "Done — see PR #42."
```

## Delivery comments

After completing implementation work tied to a Multica issue, post a delivery comment summarizing:
- What was done
- Relevant PR or commit reference
- Any unresolved follow-ups or blockers

This is the canonical signal that work on an issue is complete. Do not treat a local diff or a Forgejo PR alone as the deliverable — the Multica comment closes the loop.

## Trigger-comment discipline

When acting on a trigger comment, reply in the same issue thread. Do not open a new issue or post to a different issue. Keep the thread coherent so the full history of the task is readable in one place.

## Mentions

Use mentions sparingly. Mention a person only when their attention is specifically needed (decision required, blocker, review request). Do not mention people as a courtesy or summary CC.

## No literal escape sequences

No `\n` in comment text. Use real plaintext newlines via stdin or a file. See the formatting example above.
