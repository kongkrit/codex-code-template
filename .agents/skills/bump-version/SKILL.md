---
name: bump-version
description: Bump the version in PROGRESS.md, commit all changes, and tag the commit when the user invokes $bump-version or requests a version bump with a commit and tag.
---

# Bump the version

Usage: `$bump-version [version]`. Run `$wrapup` first so the release includes the updated handoff. An explicit invocation authorizes the commit and tag; otherwise, create them only when the user's current request asks for them.

1. Review `git status`, staged and unstaged diffs, and the `Version: vX.Y.Z` line in `PROGRESS.md`. Stop and tell the user if that line is missing, ambiguous, or malformed. Run `git describe --tags --abbrev=0`; if it names a different tag from the current version, stop and report the mismatch. With no reachable tag, allow the initial `v0.0.0` baseline. If `PROGRESS.md` has no uncommitted changes, stop and tell the user to run `$wrapup` first.
2. Use the supplied version, or increment only the patch number: `vX.Y.Z` becomes `vX.Y.(Z+1)`, starting with `v0.0.0` to `v0.0.1`. Add a leading `v` if absent. Require a full match of `v[0-9]+\.[0-9]+\.[0-9]+`; otherwise, stop and report the invalid version. If `git tag --list` already includes that exact tag, stop; never replace an existing tag.
3. Use `git status --short --untracked-files=all` to list every untracked file. If any exist, show their paths and ask whether to include them. Wait for explicit approval before adding any; if approval is declined, stop.
4. Replace only the version line in `PROGRESS.md` with `Version: vX.Y.Z`, using the new version. Stage all changes with `git add --all` and review the staged diff. Commit with a short summary of the actual changes, followed by a blank line and `Bump version to vX.Y.Z` as the last line. Only after the commit succeeds, create a lightweight tag on that commit with `git tag vX.Y.Z`, substituting the new version.
5. Do not push. Report the new version, commit, and tag, and give the command `git push origin main vX.Y.Z` with the actual version.
