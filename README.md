# 🤖 ez-devbox 📦

[![npm version](https://img.shields.io/npm/v/ez-devbox.svg)](https://www.npmjs.com/package/ez-devbox)
[![CI](https://github.com/shanebishop1/ez-devbox/actions/workflows/ci.yml/badge.svg)](https://github.com/shanebishop1/ez-devbox/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/github/license/shanebishop1/ez-devbox)](https://github.com/shanebishop1/ez-devbox/blob/main/LICENSE)

`ez-devbox` is a small CLI for running coding agents in disposable E2B sandboxes without rebuilding the same shell glue every time.

![ez-devbox: create a sandbox, use OpenCode, and resume the session](docs/assets/ez-devbox-demo.gif)

The closest alternative is usually a homegrown setup: create an E2B sandbox, clone the repo, copy auth files, run setup commands, start `tmux`, SSH in, launch OpenCode/Codex/Claude Code, remember the sandbox ID, and reattach later. This tool packages that workflow into repeatable commands and config.

## What This Is

- A workflow layer on top of E2B sandboxes.
- A way to launch and reconnect to OpenCode, Codex, Claude Code, or a shell in the same sandbox.
- A config-driven bootstrapper for cloning repos, setting branches, installing dependencies, and starting in the right working directory.
- A controlled way to pass selected env vars and sync local tool auth/config into the sandbox.
- Optional tunnel setup for reaching local MCP servers, Docker containers, or other services from the sandbox.

## Demo flow

After the [Quick start](#quick-start), with Node.js 20+, an `E2B_API_KEY` in `.env`, and an `ez-devbox.config.toml` in the current directory:

```bash
npx ez-devbox@latest create --mode ssh-opencode --detach --json
# Set SANDBOX_ID to the sandboxId from the create result.
npx ez-devbox@latest resume
npx ez-devbox@latest list --json
npx ez-devbox@latest wipe --sandbox-id "$SANDBOX_ID"
```

Privacy: redact sandbox IDs, credentials, and private repository names before sharing output or recordings.

## Why Use It

Use `ez-devbox` if your current workflow looks like `git worktree` + `tmux` + SSH + custom E2B scripts + copied config files, and you want that to be less manual.

It handles the repetitive parts:

- create or connect to an E2B sandbox
- clone/bootstrap one or more repos
- launch the selected agent mode
- keep SSH sessions persistent with `tmux` where needed
- save last-run state so `resume` can reattach
- sync selected OpenCode, Codex, Claude Code, and GitHub CLI auth/config during `create`
- optionally expose local services to the sandbox through managed tunnels

## How It Compares

- `git worktree` + `tmux` + SSH: flexible and simple, but you write the lifecycle glue yourself. `ez-devbox` keeps the same terminal-first feel while adding sandbox creation, config sync, bootstrap, and resume state.
- Raw E2B SDK/CLI scripts: good if you want total control. `ez-devbox` is for the repeated agent workflow around E2B, not for replacing E2B itself.
- Daytona, Coder, Codespaces, DevPod: infrastructure or dev-environment platforms. They can be useful underneath or alongside this kind of workflow, but `ez-devbox` is focused on launching and reattaching coding-agent sessions with your local config and auth expectations.
- Full agent platforms: better if you want task queues, PR automation, dashboards, or autonomous background work. `ez-devbox` is deliberately closer to "give me a clean remote box and attach my agent shell."

## Agent Modes

- `ssh-opencode`: SSH into the sandbox and attach the OpenCode TUI to a persistent in-sandbox `opencode serve` backend.
- `ssh-codex`: SSH into the sandbox and attach Codex inside a persistent `tmux` session.
- `ssh-claude`: SSH into the sandbox and attach Claude Code inside a persistent `tmux` session.
- `web`: start `opencode serve` and print the URL.
- `ssh-shell`: SSH into an interactive shell inside a persistent `tmux` session.

## Install

Prerequisites:

- Node.js 20 or newer on macOS or Linux. Windows config paths are supported, but Windows host SSH/tunnel workflows are not currently tested in CI.
- An [E2B API key](https://e2b.dev/docs/getting-started/api-key).
- `ssh` for SSH modes. If `tmux` is missing in the E2B template, ez-devbox installs it with `apt-get` or `apk`; other templates must provide it. Docker or `cloudflared` is needed only for tunnel features.
- An `ez-devbox.config.toml`, created during onboarding below or by the interactive first-run prompt.

Choose one:

```bash
npm install --save-dev ez-devbox
npx ez-devbox --help
```

or one-off run without install:

```bash
npx ez-devbox --help
```

or global install:

```bash
npm install -g ez-devbox
ez-devbox --help
```

The package exposes both `ez-devbox` and `ezdb` binaries. Use `ez-devbox` in portable instructions; after installation, `ezdb --help` is equivalent.

## Environment variables

You can set variables in your shell or put them in a local `.env` file.

For a source checkout, copy the template:

```bash
cp .env.example .env
```

Minimum required:

- `E2B_API_KEY`: required for any real sandbox operation (`create`, `connect`, `list`, `wipe`, live e2e).

Common optional vars:

- `FIRECRAWL_API_URL`: used by your own tooling/workloads inside the sandbox (for example tunneled MCP/API endpoints).
- `FIRECRAWL_API_KEY`: forwarded only if configured through `env.pass_through`.
- `GITHUB_TOKEN` / `GH_TOKEN`: used for GitHub auth flows (especially when `[gh].enabled = true`).
- `OPENCODE_SERVER_PASSWORD`: used for `web` mode auth.

The npm package also ships `.env.example`. Do not commit `.env`; it contains local secrets.

## Quick start

1. Make a project directory, then create `.env` and set at least:

```bash
printf 'E2B_API_KEY=%s\n' 'your_key_here' > .env
```

2. Download the complete minimal config, then edit its repo URL, branch, and setup command:

```bash
curl -fsSLo ez-devbox.config.toml \
  https://raw.githubusercontent.com/shanebishop1/ez-devbox/main/examples/minimal/ez-devbox.config.toml
```

The same example is shipped inside an installed package at `node_modules/ez-devbox/examples/minimal/ez-devbox.config.toml`. See the [minimal workflow](https://github.com/shanebishop1/ez-devbox/tree/main/examples/minimal) for a runnable public-repo example.

Config lookup order:

- Local: `./ez-devbox.config.toml` (from the directory where you run `ez-devbox`)
- Global: user config file
  - macOS/Linux: `~/.config/ez-devbox/ez-devbox.config.toml`
  - Windows: `%APPDATA%\\ez-devbox\\ez-devbox.config.toml`

If neither file exists and you're in an interactive terminal, ez-devbox prompts you to create a starter config locally or globally, then continues with it. In non-interactive environments, it exits with an error listing both expected paths.

If you prefer not to download a file, this is the smallest useful custom-project config:

```bash
cat > ez-devbox.config.toml <<'EOF'
[sandbox]
template = "opencode"
name = "ez-devbox"

[project]
mode = "single"
active = "prompt"

[[project.repos]]
name = "your-repo"
url = "https://github.com/your-org/your-repo.git"
setup_command = "npm install"
EOF
```

Then set each repo's `setup_command` as needed. The tracked repo-root config is deliberately a neutral, runnable example—not a maintainer's personal launcher configuration. For every field, see the [config reference](https://github.com/shanebishop1/ez-devbox/blob/main/docs/launcher-config-reference.md).

3. Run commands (`npx` if not globally installed):

```bash
npx ez-devbox create
npx ez-devbox connect
```

## Mode guides

- [Web mode (OpenCode in browser)](https://github.com/shanebishop1/ez-devbox/blob/main/docs/modes-web.md)
- [SSH agent modes (OpenCode, Codex, and Claude Code)](https://github.com/shanebishop1/ez-devbox/blob/main/docs/modes-ssh-agents.md)

## Common commands

Use `npx ez-devbox ...` if the CLI is not globally installed. See the install note above before relying on the `ezdb` alias.

| Goal | Command |
| --- | --- |
| Help | `ez-devbox --help` |
| Create sandbox + launch mode | `ez-devbox create --mode web` |
| List sandboxes | `ez-devbox list` |
| Connect to existing sandbox | `ez-devbox connect --sandbox-id <sandbox-id>` |
| Resume last sandbox/mode | `ez-devbox resume` |
| Run command in sandbox | `ez-devbox command --sandbox-id <sandbox-id> -- pwd` |
| JSON output for automation | `ez-devbox list --json` |
| Start agent detached | `ez-devbox create --mode ssh-codex --detach --json` |
| Send follow-up from file | `ez-devbox connect --sandbox-id <id> --mode ssh-codex --detach --prompt-file follow-up.md --json` |
| Wipe one sandbox | `ez-devbox wipe` |
| Wipe all sandboxes | `ez-devbox wipe-all --yes` |

## JSON output contracts

Use `--json` on automation-facing commands for stable machine-readable output:

- `list`: `{ "sandboxes": [...] }`
- `command`: command result envelope (`sandboxId`, `command`, `cwd`, `stdout`, `stderr`, `exitCode`)
- `create` / `connect`: launch result envelope (mode, command/url when present, workingDirectory, setup summary)

Tip: optional fields are omitted when undefined (for example `url` is absent for SSH modes).

For detached startup, prompt transport, non-PTY inspection, explicit shell execution, and concurrency guidance, see [Agent and automation usage](docs/agent-automation.md).

## Verbose mode

- Use `--verbose` to show detailed operational logs during `create/connect` (startup mode resolution, sandbox lifecycle steps, create-time tooling sync progress, bootstrap progress, SSH/tunnel setup details).
- Interactive pickers/prompts still show as normal.
- Without `--verbose`, ez-devbox keeps output focused on prompts and final command results.

## Config files

- `ez-devbox.config.toml`: ez-devbox behavior (sandbox, startup, project, env pass-through, tooling auth sync, tunnel). Resolved from local-first then global fallback.
- `.env`: secrets and local env values
- last-run state: by default stored at `${TMPDIR}/ez-devbox/last-run/cwd-state/<sha1(cwd)>/.ez-devbox-last-run.json` (legacy `.agent-box-last-run.json` in the current directory is still read only for persisted-data compatibility)
- [Config reference](https://github.com/shanebishop1/ez-devbox/blob/main/docs/launcher-config-reference.md): full `ez-devbox.config.toml` field reference

## Credentials, tunnels, and resource lifecycle

- `E2B_API_KEY` stays on the host and is used by the E2B SDK; it is not one of the sandbox pass-through variables. Values selected by built-in forwarding or `[env].pass_through` are sent into the sandbox during creation and may also be supplied to setup/startup commands on reconnect.
- Tool auth/config sync is explicit and create-time only. Configured OpenCode, Codex, Claude, and optional GitHub CLI files are copied from the host into the sandbox; treat the sandbox as credential-bearing and use only trusted templates and repositories.
- Quick-tunnel URLs are temporary bearer links with no access control supplied by ez-devbox. Anyone with a URL can reach its upstream while the host CLI operation and tunnel process remain active. Use upstream authentication and never expose an untrusted administrative service.
- Sandboxes are disposable but not automatically deleted when you detach or exit. `sandbox.timeout_ms` sets the E2B timeout at creation; reconnecting does not reset it, and the reserved `reuse` and `delete_on_exit` fields do not currently alter lifecycle behavior.
- Use `wipe --sandbox-id <id>` or `wipe-all --yes` to delete E2B resources explicitly. A newly created sandbox is auto-wiped only when interactive repository selection is cancelled after creation.

### Tunnel targets

For non-local upstream services, define explicit tunnel targets (port -> upstream URL):

```toml
[tunnel]

[tunnel.targets]
"3002" = "http://10.0.0.20:3002"
```

This keeps the same `EZ_DEVBOX_TUNNEL_*` env output while pointing cloudflared at a remote host/service.
When `tunnel.targets` is present, its keys are the authoritative tunneled ports (you do not need `tunnel.ports`).
Target URLs cannot include credentials, path, query, or fragment.
On `create`, ez-devbox prints a warning that tunnel URLs are effectively bearer links: anyone with the URL can reach the forwarded service.

## Troubleshooting

- `authorization header is missing` / 401 errors:
  - make sure `.env` exists and contains `E2B_API_KEY`.
- `wipe-all requires --yes in non-interactive terminals`:
  - add `--yes` in CI/scripts.
- Multiple sandboxes in non-interactive runs:
  - pass `--sandbox-id <id>` explicitly.
- Tunnel command issues:
  - ensure `cloudflared` is installed, or Docker is available for fallback.

## ez-devbox.config.toml reference

See the [complete config reference](https://github.com/shanebishop1/ez-devbox/blob/main/docs/launcher-config-reference.md).

## Project policy

- Security reports: [SECURITY.md](https://github.com/shanebishop1/ez-devbox/blob/main/SECURITY.md)
- Contributions: [CONTRIBUTING.md](https://github.com/shanebishop1/ez-devbox/blob/main/CONTRIBUTING.md)
- Release notes: [CHANGELOG.md](https://github.com/shanebishop1/ez-devbox/blob/main/CHANGELOG.md)
- [Portfolio and social copy](https://github.com/shanebishop1/ez-devbox/blob/main/docs/portfolio.md)
