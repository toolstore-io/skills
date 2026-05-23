---
name: writing-style
description: Use when writing prose for humans — PR descriptions, issue comments, ADRs, READMEs, commit-message bodies. Triggers whenever the output is read-by-humans-not-machines. Covers be-direct, em-dash sparingly, no hedging language, short sentences over long complete ones.
---

# Writing Style

## Be direct

Get to the point immediately. No filler phrases, no throat-clearing, no meta-commentary about what you are about to say.

## Em-dashes sparingly

Use em-dashes (—) sparingly. One per document is fine; several in a paragraph is a signal to restructure. When in doubt, a comma, colon, or split sentence reads more cleanly.

```
// fine — one em-dash for emphasis
The service validates the token — invalid tokens return not_found immediately.

// too many — restructure instead
The gateway fetches the nonce — if it fails, it returns unreachable — then computes the MAC — before registering the user.
```

## No hedging language

Avoid phrases that add length without meaning: "it's worth noting that", "please note", "essentially", "basically", "just".

## Short and clear over long and complete

Prefer short sentences. If a sentence needs more than one clause, consider splitting it. A reader should never have to re-read a sentence to understand it.
