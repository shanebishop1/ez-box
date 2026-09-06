# ez-devbox Master Cleanup Plan

Status: Complete
Owner: Shane Bishop
Last updated: 2026-09-06

This is the canonical checklist for the cleanup and portfolio-readiness work discussed in sessions #1 and #2. Checked items are historical outcomes, not work to repeat. The plan deliberately separates shipped release work from the later uncommitted hardening pass.

## Current State

- [x] `main` contains the session #1 release hardening and has since received newer upstream commits.
- [x] `v0.6.0` is published on npm and has a matching GitHub Release.
- [x] The repository has protected `main`, CI, release automation, security/contributor docs, examples, and package verification.
- [x] Session #1's live E2B workflow passed for OpenCode, web auth, SSH, Codex, Claude, reconnection, and cleanup.
- [x] Session #2's staged changes are resolved with the current upstream TypeScript/Vitest tooling and E2B SDK v2.
- [x] Current HEAD includes the detached/prompt transport work, the focused source-maintenance pass, and the user-approved restored original terminal demo.
- [x] `v0.6.1` was published with a matching GitHub Release and npm provenance; installed-package smoke testing then found the npm bin-symlink entrypoint regression.
- [x] Commit `f9bd94a` fixes the npm bin-symlink entrypoint regression and adds permanent real pack/install smoke coverage.
- [x] Corrective release `v0.6.2` is published with matching package, package-lock, tag, GitHub Release, npm package, and npm provenance; the successful Release workflow and attestation are recorded below.
- [x] Current offline validation passes with 370 tests: 82.32% statements, 72.90% branches, 82.88% functions, and 82.57% lines; complexity, style, build, and package checks also pass.

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
- [x] Run `npm run validate:offline`: 370 tests passed with 82.32% statements, 72.90% branches, 82.88% functions, and 82.57% lines coverage; build, style, complexity, and package checks passed.
- [x] Run `npm run e2e:live` with credentials on 2026-09-06: 12/12 live E2E scenarios passed, cleanup passed 3/3, and the final sandbox listing confirmed zero remaining sandboxes.
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
- [x] The user-approved original terminal demo (roughly 20-40 seconds) showing create, reconnect/resume, JSON listing, and cleanup is restored.
- [x] Embed the restored demo in the README in its original lightweight GitHub-compatible placement without exposing credentials, sandbox IDs, or private repository names.
- [x] Review README claims against the current CLI, release guidance, CI platforms, supported Node versions, and current CLI output. The README reflects Node 20+, Linux/macOS CI, and the current command contracts.
- [x] Verify the public repository has useful discovery metadata and links to npm, GitHub, security guidance, and contribution guidance. Package metadata, badges, repository/homepage/issue links, and README policy links are present and consistent.
- [x] Draft the public launch/social post from the verified capabilities in `docs/portfolio.md`; do not claim unverified live behavior. The portfolio summary and social draft use the verified CLI, CI, package, and live-test capabilities.

## Phase 4: Focused Maintenance Review

Only do these if the result is clearly smaller and more maintainable. Avoid a broad redesign or speculative abstraction pass.

- [x] Inspect tracked files and scripts for genuinely unused, generated, personal, or legacy artifacts; remove only confirmed leftovers. The audit found no confirmed artifact or generated/personal leftover requiring removal.
- [x] Review the flat `scripts/` directory and group files only where the grouping improves discoverability without breaking package paths or npm scripts. No move was warranted; existing script paths and package commands are clear and externally referenced.
- [x] Check for newly oversized or mixed-responsibility source files; split only clear natural boundaries and preserve public behavior. Shared command argument/environment resolution, create execution, and host-sync operations now have explicit source boundaries.
- [x] Prefer the existing target of roughly 250 lines per file, but document exceptions where a file is cohesive or externally constrained. The remaining over-250-line files, `src/e2b/lifecycle.ts` and `src/setup/runner.ts`, are cohesive lifecycle/error-mapping and setup-pipeline units; the guardrail passes without speculative splitting.
- [x] Recheck duplicated parsing, prompt, environment, formatting, and orchestration helpers before introducing new abstractions. The maintenance pass extracted only the shared helpers needed by the current command boundaries.
- [x] Keep command modules aligned to parse, resolve, execute, and format responsibilities. The extracted `command-args.ts`, `command-env.ts`, `commands.create.execute.ts`, and `host-sandbox-sync.operations.ts` preserve those boundaries and public behavior.
- [x] Add regression tests and docs for every behavior change; do not add tests solely to inflate coverage. Compatibility tests cover legacy state/flag behavior and command-specific environment entry points, alongside detached/prompt transport documentation.
- [x] Run the full offline validation after any maintenance refactor. Senior review/current validation recorded 370 passing tests with 82.32% statements, 72.90% branches, 82.88% functions, and 82.57% lines coverage; style, complexity, build, and package checks passed.

## Release Gate

Before publishing any follow-up version:

- [x] Pre-release check passed from a clean, current `main` with no unresolved index entries.
- [x] Historical `v0.6.1` version-agreement precheck completed, followed by matching package/tag/GitHub Release/npm provenance evidence at publication; installed-package smoke later exposed the bin-symlink regression.
- [x] Confirm the corrective `0.6.2` version was unclaimed and complete version agreement: `package.json`, `package-lock.json`, tag, GitHub Release, and npm all report `0.6.2`.
- [x] Run `npm run validate:offline`: 370 tests passed with 82.32% statements, 72.90% branches, 82.88% functions, and 82.57% lines coverage.
- [x] Run `npm run e2e:live` from a trusted maintainer environment: 12/12 scenarios passed, cleanup was 3/3, and the final sandbox listing was zero.
- [x] Run `npm run pack:check` and inspect `npm pack --dry-run --json`: 220 files were packed, and the published `ez-devbox` and `ezdb` bins were verified.
- [x] Run the documented `v0.6.2` release command: tag `v0.6.2` points to commit `9ca18f4`.
- [x] Confirm the `v0.6.2` GitHub Actions Release and post-version CI succeeded: Release run [34066634470](https://github.com/shanebishop1/ez-devbox/actions/runs/34066634470) and CI run [34066633717](https://github.com/shanebishop1/ez-devbox/actions/runs/34066633717); npm publish includes SLSA provenance at https://registry.npmjs.org/-/npm/v1/attestations/ez-devbox@0.6.2.
- [x] Record the `v0.6.2` release evidence in [CHANGELOG.md](../../CHANGELOG.md) and the [GitHub release notes](https://github.com/shanebishop1/ez-devbox/releases/tag/v0.6.2).

## Related Documents

- `todo_dont_commit.md`: historical completed engineering backlog; retain as reference, not as the active checklist.
- `docs/plans/2026-09-04-reconcile-main-design.md`: narrower historical plan for canonicalizing `main`.
- `docs/release-checklist.md`: operational release procedure.
- `docs/portfolio.md`: verified public-facing project and social copy.
