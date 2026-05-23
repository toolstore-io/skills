---
name: pr-handling
description: Use when opening, updating, or reviewing a pull request — drafting the PR body, posting review comments, picking a branch name, or deciding what goes in the description vs. the commit messages. Triggers on "open a PR", "PR description", "review comment", "branch name". Pair with the forgejo skill for Forgejo-specific CLI.
---

# PR Handling

These rules apply regardless of git host. For the actual CLI calls (`fj` vs `gh`), pair with the `forgejo` skill or your host's equivalent.

## Opening a PR

Push your branch, then create the PR through the host CLI.

```sh
git push origin HEAD
```

## PR body content

Always write the PR body to a file and submit from the file. Never pass multi-line content as an inline shell argument — shell expansion corrupts symbols, backticks, and special characters.

```sh
cat > /tmp/pr-body.md << 'EOF'
## Summary
- Added email invite endpoint
- Validates token before accepting

## Test plan
- [ ] Manual: send invite, accept via UI
- [ ] Unit tests pass
EOF

# Forgejo
fj pr create --title "feat(api): add email invite endpoint" --body-file /tmp/pr-body.md
```

The Multica issue link belongs in the PR body, not the title. See the `multica` skill for the canonical delivery comment that closes the loop on a Multica issue.

## Branch naming

```
feat/short-description
fix/short-description
refactor/short-description
chore/short-description
```

## Review comments via CLI

When posting review comments, use stdin or a body file. Never pass code snippets, multi-line text, or special characters as a double-quoted shell argument. For Forgejo specifically, threaded review replies need the REST API directly — see the `forgejo` skill.

## Never push directly to main

All changes go through branches and pull requests. Main is protected.
