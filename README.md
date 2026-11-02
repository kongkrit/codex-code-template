# AI-Assisted Coding Template

A starter repository for AI-assisted development with Codex, Claude Code, or another coding assistant: shared project instructions, a session-handoff workflow, ignore rules, and LF line endings from the first commit.

## What's included

<details>
<summary>Shared instructions, session handoff, optional assistant settings</summary>
<br>

| File | Purpose |
|------|---------|
| `AGENTS.md` | Shared project instructions: code principles, explanation style, conventions, environment, and session workflow. Edit to fit your project. |
| `CLAUDE.md` | Imports `AGENTS.md` for Claude Code compatibility. Keep shared guidance in `AGENTS.md`. |
| `docs/markdown.md` | Shared formatting rules for README.md and docs/, referenced by `AGENTS.md`. |
| `PROGRESS.md` | Latest session handoff that any assistant can read and update; earlier ones: `git log -p -- PROGRESS.md`. |
| `.codex/config.toml` | Optional Codex settings: workspace writes, `/tmp` access, `xhigh` reasoning effort, and approvals on request. |
| `.codex/rules/default.rules` | Codex command allowlist matching Claude Code's shell permissions. |
| `.agents/skills/wrapup/SKILL.md` | Codex's `$wrapup` shortcut — rewrites `PROGRESS.md` as a handoff for the work since the previous one, then commits. |
| `.claude/settings.json` | Optional Claude Code settings (`model: opus`, `effortLevel: xhigh`), a permission allowlist, and a `SessionStart` hook that prints `PROGRESS.md` into context. |
| `.claude/commands/wrapup.md` | Claude Code's `/wrapup` shortcut — rewrites `PROGRESS.md` as a handoff for the work since the previous one, then commits. |
| `.claude/rules/markdown.md` | Symlink to `docs/markdown.md`; Claude Code loads it automatically when it reads a README.md or a Markdown file under docs/. |
| `.gitignore` | OS cruft, Python/Node build artifacts, editor files. |
| `.gitattributes` | LF line endings; binary file types. |

</details>

## Usage

<details>
<summary>Create a project, choose an assistant, hand off a session</summary>
<br>

1. Click **Use this template** on GitHub, or clone the repo.
2. Edit `AGENTS.md` for your project, including its environment and build, test, and run commands.
3. Delete what you don't need and start building with your preferred assistant.

**Codex.** Open and trust the repository in Codex. It reads `AGENTS.md` automatically ([official guidance](https://learn.chatgpt.com/docs/agent-configuration/agents-md)). The optional `.codex/config.toml` sets workspace writes, `/tmp` access, `xhigh` reasoning effort, and approvals on request ([configuration docs](https://learn.chatgpt.com/docs/config-file/config-reference)). Choose the model in your personal Codex settings.

`.codex/rules/default.rules` mirrors Claude Code's shell allowlist. Matching commands may run outside the sandbox without another prompt. Project config and rules load only for trusted projects and remain subject to managed policy. Restart Codex after changing rules ([rules docs](https://learn.chatgpt.com/docs/agent-configuration/rules), [configuration basics](https://learn.chatgpt.com/docs/config-file/config-basic)).

**Claude Code.** `CLAUDE.md` imports the same `AGENTS.md`, including for versions that do not read `AGENTS.md` directly ([shared instructions docs](https://code.claude.com/docs/en/memory#share-one-file-with-other-coding-tools)). Adjust the optional `.claude/settings.json` to taste ([settings docs](https://code.claude.com/docs/en/settings)). Personal overrides go in `.claude/settings.local.json`, which is gitignored. These settings and `/wrapup` apply to Claude Code.

**Other assistants.** Point them at `AGENTS.md` if they do not discover it automatically.

**Session handoff.** `AGENTS.md` tells assistants to read `PROGRESS.md` at session start. To save a handoff, ask your assistant to update `PROGRESS.md`; explicitly request a commit if you want one. Use `/wrapup` in Claude Code or `$wrapup` in Codex to update the handoff and commit. These shortcuts never push.

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

**More tools.** In the intended development environment, `sudo` is passwordless, and coding assistants may install tools a task needs, subject to their permissions:

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
