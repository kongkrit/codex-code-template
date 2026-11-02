# Codex Coding Template

A starter repository for development with Codex: project instructions, a session-handoff workflow, ignore rules, and LF line endings from the first commit.

## What's included

<details>
<summary>Project instructions, session handoff, Codex settings</summary>
<br>

| File | Purpose |
|------|---------|
| `AGENTS.md` | Project instructions for Codex: code principles, explanation style, conventions, environment, and session workflow. Edit to fit your project. |
| `docs/markdown.md` | Formatting rules for README.md and docs/, referenced by `AGENTS.md`. |
| `PROGRESS.md` | Current version and handoff for changes since the latest tag; tags and git history hold earlier versions. |
| `.codex/config.toml` | Optional Codex settings: workspace writes, `/tmp` access, `xhigh` reasoning effort, approvals on request, and a startup handoff hook. |
| `.codex/rules/default.rules` | Command allowlist for Codex. |
| `.agents/skills/wrapup/SKILL.md` | Codex's `$wrapup` shortcut — rewrites `PROGRESS.md` with changes since the latest tag; never stages or commits. |
| `.agents/skills/bump-version/SKILL.md` | Codex's `$bump-version [version]` shortcut — bumps the version, commits all changes, and adds a lightweight tag. |
| `.gitignore` | OS cruft, Python/Node build artifacts, editor files. |
| `.gitattributes` | LF line endings; binary file types. |

</details>

## Usage

<details>
<summary>Create a project, configure Codex, hand off a session, release a version</summary>
<br>

1. Click **Use this template** on GitHub, or clone the repo.
2. Edit `AGENTS.md` for your project, including its environment and build, test, and run commands.
3. Open and trust the repository in Codex and start building.

**Configuration.** Codex reads `AGENTS.md` automatically ([official guidance](https://learn.chatgpt.com/docs/agent-configuration/agents-md)). The optional `.codex/config.toml` sets workspace writes, `/tmp` access, `xhigh` reasoning effort, and approvals on request ([configuration docs](https://learn.chatgpt.com/docs/config-file/config-reference)). Choose the model in your personal Codex settings.

`.codex/rules/default.rules` allows selected shell commands to run outside the sandbox without another prompt. Project config and rules load only for trusted projects and remain subject to managed policy. Restart Codex after changing rules ([rules docs](https://learn.chatgpt.com/docs/agent-configuration/rules), [configuration basics](https://learn.chatgpt.com/docs/config-file/config-basic)).

**Startup handoff.** The `SessionStart` hook in `.codex/config.toml` loads `PROGRESS.md` into context on startup, including when Codex starts in a subdirectory. It includes commands for Unix shells and Windows PowerShell. Use `/hooks` in the Codex CLI to review and trust the hook, then start a new session ([hooks docs](https://learn.chatgpt.com/docs/hooks)). `AGENTS.md` also instructs Codex to read the handoff if it has not been loaded.

**Markdown.** `AGENTS.md` tells Codex to read `docs/markdown.md` before creating or editing a README.md or a Markdown file under docs/.

**Session handoff.** Use `$wrapup` to rewrite `PROGRESS.md` with changes since the latest tag, including uncommitted changes. With no tag, it covers the whole history. It preserves the version and never stages or commits ([skills docs](https://learn.chatgpt.com/docs/build-skills)).

**Version bump.** The version starts at `v0.0.0`, stored as `Version: v0.0.0` in `PROGRESS.md`. After `$wrapup`, use `$bump-version` for the next patch version or `$bump-version 1.2.3` for an explicit version. It checks the current version against the latest tag, requires an uncommitted handoff, and asks before including untracked files. It then commits all changes and adds a lightweight version tag. It never pushes; it reports `git push origin main vX.Y.Z` with the new version.

</details>

## Development setup (Debian 13)

<details>
<summary>Base tools, Python with uv, optional Docker multi-arch setup</summary>
<br>

`docker` is assumed installed. Base toolchain:

```bash
sudo apt install -y git gh python3 python3-dev build-essential curl jq wget
```

`python3-dev` and `build-essential` are only needed for Python packages built from source.

`uv` is not packaged for Debian; install it with the standalone installer (needs `curl` from above; [official instructions](https://docs.astral.sh/uv/getting-started/installation/)). It puts `uv` and `uvx` in `~/.local/bin`, which Debian's default `~/.profile` adds to `PATH` on next login:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Verify: `git --version && gh --version && curl --version && uv --version`.

**More tools.** In the intended development environment, `sudo` is passwordless, and Codex may install tools a task needs, subject to its permissions:

```bash
sudo apt update && sudo apt install -y <package>
```

Use `apt` for system tools; keep Python packages in the project venv. Add any lasting dependency to the command above.

**Python.** Use `uv` for every venv and package install, not `pip` or `python3 -m venv` (Debian 13 also blocks system-wide `pip install`, PEP 668). One venv per project:

```bash
uv sync                                          # project with a pyproject.toml; add packages with: uv add <pkg>
uv venv && uv pip install -r requirements.txt    # project with only a requirements.txt
```

For a packaged CLI, `uv tool install .` avoids managing a venv by hand.

**Docker multi-arch (optional, one-time).** Cross-building `linux/amd64` + `linux/arm64` needs QEMU and a `docker-container` builder:

```bash
docker run --privileged --rm tonistiigi/binfmt --install all
docker buildx create --name multiarch --driver docker-container --bootstrap --use
```

On Apple Silicon with OrbStack, emulation is built in, so only the second line is needed.

**Not used.** `node`/`npm` (serve static content with `python3 -m http.server`) and the .NET SDK.

</details>
