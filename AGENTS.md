# AGENTS.md

Shared guidance for AI coding assistants working in this repository.

## What this is

An **AI-assisted coding template** — a starting point for new projects, not an application. It holds shared instructions and optional assistant-specific configuration; application code gets added alongside them.

## Code principles

Code is simple, minimal, easy to understand, and DRY. These come before cleverness, speculative flexibility, and personal style.

- **Simple:** use the most straightforward approach that works. No clever tricks, premature abstraction, or premature optimization.
- **Minimal:** write only what the task needs. No speculative features, unused options, or "just in case" code. Fewer lines, files, and dependencies win. Delete dead code.
- **Easy to understand:** a newcomer should follow it on first read. Clear names, small functions, shallow nesting, obvious control flow.
- **DRY:** one source of truth for each piece of logic, data, and config. Search for existing code to reuse before writing new code; never copy-paste logic. Extract it once instead.
- **When in doubt, choose the simpler option.** Before finishing, re-read your diff and cut anything that isn't needed.

## How to explain things

These rules apply to explanations to the user and to code comments, not to commit messages.

For code comments:

- Explain why and the mechanism; don't restate what the code plainly does.
- Define a term once, at its first use in the file.
- Add a worked numeric example only for non-obvious math (scaling, units, bit layouts).
- Keep comments as short as the point allows; "Simple", "Minimal", "Easy to understand", and "DRY" still apply.

Audience: someone with a BS in EE who remembers the fundamentals (circuits, signals, calculus, basic probability and statistics) but not every formula or every field's jargon. Write for a sharp engineer outside the specialty.

Goal: as simple as possible, without saying anything false.

1. Start with the plain-language answer in 1-2 sentences, then the reasoning.
2. Explain the mechanism (what causes what) in words before any math.
3. Define each technical term the first time it appears, in a short phrase. Introduce as few new terms as possible.
4. Use an equation only when it makes the point clearer than words. If you use one, define every symbol and show one worked example with real numbers.
5. Prefer a concrete numeric example over abstract notation.
6. Use an analogy (EE analogies are welcome) only if it maps accurately, and say where it breaks down.
7. When you simplify, say what you left out and when it would matter.
8. No handwaving: back each claim with a reason, a number, or a source. If the answer depends on something, say what. If unsure, say so.
9. Before sending, check: could I follow this on the first read without looking anything up? If not, rewrite it.

## Layout

- `AGENTS.md` — shared project instructions. Keep assistant-independent guidance here.
- `CLAUDE.md` — imports `AGENTS.md` for Claude Code compatibility.
- `docs/markdown.md` — how to write README.md and docs/: which `##` sections fold, and how. Before editing any README.md or Markdown file under docs/, read and follow it.
- `PROGRESS.md` — latest session handoff only; earlier ones: `git log -p -- PROGRESS.md`. See the session workflow below.
- `.codex/config.toml` — optional Codex workspace permissions, `/tmp` access, and reasoning effort.
- `.codex/rules/default.rules` — Codex command allowlist matching the shell permissions in `.claude/settings.json`. Project config and rules load only for trusted projects; restart Codex after changing rules. Managed policy still applies.
- `.agents/skills/wrapup/SKILL.md` — Codex's `$wrapup` shortcut for a session handoff and commit.
- `.claude/settings.json` — optional Claude Code settings, permissions, and a `SessionStart` hook that prints `PROGRESS.md` into context. Personal overrides go in the gitignored `.claude/settings.local.json`, never here.
- `.claude/commands/wrapup.md` — Claude Code's `/wrapup` shortcut for a session handoff and commit.
- `.claude/rules/markdown.md` — symlink to `docs/markdown.md`, so Claude Code loads it automatically when it reads a file matching the rule's `paths`.
- `.gitignore`, `.gitattributes` — ignore rules; LF line endings and binary file types.
- `README.md` — human-facing overview.

## Conventions

- **NEVER `git commit` or `git push` on your own.** Do either only when the user's current message asks for it. Exception: an explicitly invoked `/wrapup` (Claude Code) or `$wrapup` (Codex) requests a handoff and a commit, never a push.
- Line endings follow `.gitattributes`: LF by default, CRLF only for the Windows script types it lists.
- New languages or toolchains extend `.gitignore` and `.gitattributes` — no parallel ignore files. Keep new entries consistent with existing ones.
- **Read first, edit once, check once.** Before changing anything, list every file the change touches and read them in one pass. Then make all the edits, then run whatever verifies them once. Don't loop read, edit, rerun per file. If a check fails, fix it and rerun that check.
- As the template becomes a real project, add build, test, and run commands here.

## Session workflow

- At session start, read `PROGRESS.md` if it has not already been loaded. Check the current repository state before relying on the handoff.
- When asked for a session handoff, rewrite `PROGRESS.md` as a handoff for the work since the previous handoff, not a running history:
  1. Base: `git log -1 --format=%h -- PROGRESS.md`, the commit that wrote the previous handoff. Cover `git log <base>..HEAD`, uncommitted changes (`git status`, `git diff HEAD`), and decisions or findings from this session that left no commit.
  2. Keep the title; the intro line names the base ("Covers the work after `<base>`; earlier handoffs: `git log -p -- PROGRESS.md`.").
     - What we did: only the work from step 1. Replace the previous list, never append to it.
     - Current state: a snapshot as of now (tests, branch and push state, what is verified and what is not).
     - Next steps, Open questions: carry over what is still open, drop what is done or answered, add new items.
  3. Once the handoff is committed, that commit becomes the next handoff's base.
- Claude Code's `/wrapup` command and Codex's `$wrapup` skill request a handoff and a commit. With another assistant, ask to update `PROGRESS.md` and commit for the same workflow. A handoff alone does not authorize a commit.

## Development environment

- **Debian 13 (trixie)** with `docker` installed. Base tooling via `apt`: `git`, `gh`, `python3`, `python3-dev`, `build-essential`, `curl`, `jq`, `wget`; plus `uv` from its standalone installer into `~/.local/bin` (commands in the [README](README.md#development-setup-debian-13)).
- **Install missing tools yourself:** `sudo apt install -y <package>` (`sudo apt update` first if not found). Use `apt` for system tools only. If the project will keep relying on a new tool, add it to the README's install command.
- **Python: use `uv` only.** Never run `pip`, `pip3`, `python3 -m pip`, `python3 -m venv`, or `pipx`; `uv pip …` is uv's own subcommand and is fine. uv handles both venvs and installs, and Debian 13 blocks system-wide `pip install` anyway (PEP 668). One venv per project:

  ```bash
  uv sync                                          # pyproject.toml project; add packages with: uv add <pkg>
  uv venv && uv pip install -r requirements.txt    # requirements.txt-only project
  uv run <cmd>                                     # run a command in the project venv
  ```
- `node`/`npm` and .NET are not used; serve static content with `python3 -m http.server`. Multi-arch Docker builds need a one-time QEMU + buildx setup (see README).
