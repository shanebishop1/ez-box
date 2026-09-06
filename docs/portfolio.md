# Portfolio And Social Copy

## Project summary

`ez-devbox` is a TypeScript CLI that turns E2B sandboxes into repeatable remote coding-agent environments. It bootstraps repositories, forwards only explicitly selected credentials, launches OpenCode, Codex, Claude Code, or a shell, and reconnects to persistent agent sessions.

## What it demonstrates

- Product thinking: a focused workflow layer rather than another hosted development platform.
- Systems engineering: typed SDK integration, SSH/WebSocket bridging, persistent `tmux` sessions, tunnel support, and explicit E2B lifecycle controls.
- Security: opt-in environment forwarding, credential-redacted logs, restricted sandbox-file permissions, and documented tunnel exposure risks.
- Quality: strict TypeScript, Biome checks, complexity limits, package-content verification, source coverage thresholds, Linux/macOS CI, and live E2B smoke tests.

## Demo flow

Run this in a directory containing `.env` with `E2B_API_KEY` and an `ez-devbox.config.toml`:

```bash
npx ez-devbox@latest create --mode ssh-opencode
npx ez-devbox@latest resume
npx ez-devbox@latest list --json
npx ez-devbox@latest wipe
```

For a browser demo, replace the first command with `npx ez-devbox@latest create --mode web`. The command prints a password-protected OpenCode URL.

## Social post draft

I built `ez-devbox`, a TypeScript CLI for launching disposable E2B sandboxes as persistent coding-agent environments.

It handles the glue work I kept rewriting: sandbox creation, repo bootstrap, explicit credential/config sync, SSH or web agent launch, reconnecting to the last session, and cleanup.

It supports OpenCode, Codex, Claude Code, and a persistent shell. The project has strict TypeScript, source coverage gates, Linux/macOS CI, package verification, and live tests that create, connect to, exercise, and delete real E2B sandboxes.

GitHub: https://github.com/shanebishop1/ez-devbox
npm: https://www.npmjs.com/package/ez-devbox

## Portfolio entry

**ez-devbox | TypeScript, Node.js, E2B, SSH, GitHub Actions**

Built and published a CLI that standardizes disposable remote environments for coding agents. Designed a config-driven bootstrap flow for repositories and selected credentials, implemented persistent SSH/web agent sessions with reconnect support, and added secure resource lifecycle controls. Established CI across Linux and macOS with strict typing, formatting, complexity, package integrity, source coverage, and real cloud sandbox end-to-end tests.
