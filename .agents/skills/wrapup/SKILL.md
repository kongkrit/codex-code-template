---
name: wrapup
description: Rewrite PROGRESS.md with changes since the latest tag when the user invokes $wrapup or requests a session handoff. Never stage or commit.
---

# Wrap up a session

Rewrite `PROGRESS.md` from scratch. Keep the `# PROGRESS` title and preserve the current `Version: vX.Y.Z` line; version changes belong to `$bump-version`.

Find the latest reachable tag with `git describe --tags --abbrev=0`. Review `git log <tag>..HEAD`, `git status`, and staged and unstaged diffs. If no tag exists, review the whole history. Include relevant session findings that left no commit.

Use these sections:

- `## Since <tag>` — summarize commits after the tag and uncommitted changes. With no tag, use `## Since project start`.
- `## Current state` — branch and push state, verification results, and what remains unverified.
- `## Next steps` — remaining work.
- `## Open questions` — unresolved decisions.

Replace the whole file. Do not keep earlier sessions or describe older versions; tags and git history hold those.

Never stage or commit. Do not tag or push.
