---
name: pr-handling
description: Pull request conventions using multica. Use when opening, updating, or reviewing a pull request. For Forgejo-specific workflows, also load the forgejo skill.
---

# PR Handling

## Opening a PR

Push your branch, then create the PR with multica.

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

multica pr create --title "feat(api): add email invite endpoint" --body-file /tmp/pr-body.md
```

## Branch naming

```
feat/short-description
fix/short-description
refactor/short-description
chore/short-description
```

## Review comments via CLI

When posting review comments, use stdin or a body file. Never pass code snippets, multi-line text, or special characters as a double-quoted shell argument.

## Never push directly to main

All changes go through branches and pull requests. Main is protected.
