---
name: wrapup
description: Update PROGRESS.md and commit a session handoff when the user invokes $wrapup or requests a handoff and commit.
---

# Wrap up a session

Follow the session handoff instructions in the repository's root AGENTS.md to update PROGRESS.md.

An explicit `$wrapup` invocation authorizes a commit. Otherwise, commit only when the user's current request asks for it; a handoff alone does not authorize a commit.

Review the current changes, stage the work from this session, and commit with a conventional-commit message. Do not push.
