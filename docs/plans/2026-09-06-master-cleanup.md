# ez-devbox Master Cleanup Plan

Status: Active
Owner: Shane Bishop
Last updated: 2026-09-06

This is the canonical checklist for the cleanup and portfolio-readiness work discussed in sessions #1 and #2. Checked items are historical outcomes, not work to repeat. The plan deliberately separates shipped release work from the later uncommitted hardening pass.

## Current State

- [x] `main` contains the session #1 release hardening and has since received newer upstream commits.
- [x] `v0.6.0` is published on npm and has a matching GitHub Release.
- [x] The repository has protected `main`, CI, release automation, security/contributor docs, examples, and package verification.
- [x] Session #1's live E2B workflow passed for OpenCode, web auth, SSH, Codex, Claude, reconnection, and cleanup.
- [x] Session #2's staged changes are resolved with the current upstream TypeScript/Vitest tooling and E2B SDK v2.
- [x] The working tree is validated after resolving the dependency merge and before any `0.6.1` release.

## Phase 1: Finish Session #2 Safely

These are the immediate remaining items from the second readiness pass. Preserve upstream dependency updates while retaining the E2B v2 and coverage changes.

- [x] Resolve `package.json` by keeping the current upstream TypeScript/Vitest/tooling versions and the E2B SDK v2 upgrade.
- [x] Regenerate `package-lock.json` from the resolved manifest; do not hand-edit a broken lockfile.
- [x] Verify the E2B v2 adapter in `src/e2b/client.ts`, including paginator handling and the boolean deletion result.
- [x] Keep direct adapter coverage in `test/e2b.client.test.ts`.
- [x] Keep source coverage thresholds in `vitest.config.ts` after merging the dependency set.
- [x] Keep truthful deletion failure handling in `scripts/e2e.live.ts`.
- [x] Keep the macOS CI matrix and confirm its YAML remains valid.
- [x] Keep the release-checklist safeguards for npm/GitHub version mismatches.
- [x] Keep the package, README, changelog, test, and `.gitignore` changes directly related to this hardening pass.
- [x] Run `npm ci` after lockfile regeneration.
- [x] Run `npm run validate:offline`: 367 tests passed with 82.12% statements, 72.73% branches, 82.78% functions, and 82.39% lines coverage; build, style, complexity, audit, and package checks passed.
- [x] Run `npm run e2e:live` with credentials and verify every created sandbox is actually deleted.
- [x] Review the complete diff, create a focused commit, and push only after the worktree is cleanly resolved.

## Phase 2: Confirm Session #1 Release Outcomes

These items were the original launch blockers and are complete. Reopen one only if regression testing shows a problem.

- [x] Make JSON output machine-readable instead of mixing result payloads with prefixed logger output.
- [x] Make remote launch and bridge failures propagate instead of reporting success after failed commands.
- [x] Validate repository names and prevent workspace path traversal.
- [x] Redact credentials from verbose, warning, and error logs.
- [x] Replace the personal committed config with a neutral distributable example.
- [x] Reconcile the `ezdb` package alias and npm package contents.
- [x] Include documented runtime files and referenced docs/examples in the npm package.
- [x] Isolate tests from real `~/.codex` and `~/.claude` directories.
- [x] Complete the original correctness, security, reliability, CLI, and complexity cleanup described in `todo_dont_commit.md`.
- [x] Add release CI, Dependabot, package verification, security/contributor documentation, changelog updates, and protected-main requirements.
- [x] Publish and verify `v0.6.0` with npm provenance.

## Phase 3: Portfolio Presentation

These were identified in session #2 as the highest-value presentation improvements. They are optional code work and should not delay a technically verified release.

- [x] Add reusable project summary, portfolio copy, social copy, and a basic demo flow in `docs/portfolio.md`.
- [ ] Record a short, clean terminal demo (roughly 20-40 seconds) showing create, reconnect/resume, JSON listing, and cleanup.
- [ ] Add the demo to the README in a lightweight form that works on GitHub and does not expose credentials, sandbox IDs, or private repository names.
- [ ] Review README claims against the current published package, CI platforms, supported Node versions, and current CLI output.
- [ ] Verify the public repository has useful discovery metadata and links to npm, GitHub, security guidance, and contribution guidance.
- [ ] Draft the public launch/social post from the verified capabilities in `docs/portfolio.md`; do not claim unverified live behavior.

## Phase 4: Focused Maintenance Review

Only do these if the result is clearly smaller and more maintainable. Avoid a broad redesign or speculative abstraction pass.

- [ ] Inspect tracked files and scripts for genuinely unused, generated, personal, or legacy artifacts; remove only confirmed leftovers.
- [ ] Review the flat `scripts/` directory and group files only where the grouping improves discoverability without breaking package paths or npm scripts.
- [ ] Check for newly oversized or mixed-responsibility source files; split only clear natural boundaries and preserve public behavior.
- [ ] Prefer the existing target of roughly 250 lines per file, but document exceptions where a file is cohesive or externally constrained.
- [ ] Recheck duplicated parsing, prompt, environment, formatting, and orchestration helpers before introducing new abstractions.
- [ ] Keep command modules aligned to parse, resolve, execute, and format responsibilities.
- [ ] Add regression tests and docs for every behavior change; do not add tests solely to inflate coverage.
- [ ] Run the full offline validation after any maintenance refactor.

## Release Gate

Before publishing any follow-up version:

- [ ] Start from a clean, current `main` with no unresolved index entries.
- [ ] Confirm `package.json`, `package-lock.json`, changelog, Git tag, GitHub Release, and npm version agree.
- [ ] Run `npm run validate:offline`.
- [ ] Run `npm run e2e:live` from a trusted maintainer environment and verify cleanup.
- [ ] Run `npm run pack:check` and inspect `npm pack --dry-run --json` when package contents changed.
- [ ] Run the documented release command only after the version is confirmed unclaimed.
- [ ] Confirm GitHub Actions Release succeeds and npm provenance is present.
- [ ] Record the release evidence in the changelog or release notes.

## Related Documents

- `todo_dont_commit.md`: historical completed engineering backlog; retain as reference, not as the active checklist.
- `docs/plans/2026-09-04-reconcile-main-design.md`: narrower historical plan for canonicalizing `main`.
- `docs/release-checklist.md`: operational release procedure.
- `docs/portfolio.md`: verified public-facing project and social copy.
