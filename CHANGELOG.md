# Changelog

All notable changes to this project are documented here.

## [Unreleased]

### Changed
- Consolidated the agent roster from 22 role files down to 7
  (`orchestrator`, `software-engineer`, `verifier`, `reviewer`,
  `security-reviewer`, `qa-acceptance`, `integrator`), matching the
  roster already shipped in the source product repo. The
  `software-engineer` role now owns spec, architecture, and implementation
  directly instead of splitting across `product-spec` / `architect` /
  `implementer`.
- `pack/MANIFEST.json` kernel now covers all of `skills/**` (not just
  `skills/closed-loop/`) and `bin/**`, so the reusable skill packs the new
  roster depends on (`accessibility`, `api-design`, `boy-scout`, `ci-cd`,
  `closed-loop-participant`, `create-skill`, `debugging`, `design-review`,
  `github`, `migration`, `monorepo`, `performance`, `regression`) and
  `bin/cli.mjs` travel with every future export instead of drifting.
- `scripts/sync.mjs` now clears `.claude/agents/`, `.cursor/agents/`,
  `.codex/agents/`, and the synced skills directories before regenerating
  them, so a role or skill dropped from the source roster no longer leaves
  a stale generated file behind.

### Fixed
- `CLAUDE.md`, `AGENTS.md`, and `skills/closed-loop/{host,pack}.md` no
  longer refer to "22 agent files" — the wording and pipeline diagrams now
  match the 7-agent roster.

## [1.0.0] - 2026-09-27

### Changed
- Distribution model: clone-and-copy template becomes an installable npm
  package (PR #3). Consuming repos add it as a dependency
  (`yarn add -D github:leeran7/closed-loop-agents#main`) and run
  `npx closed-loop-agents sync` / `hygiene` / `init` / `loop`.
- Root package now ships `bin/cli.mjs` and declares `bin`, `files`, `engines`.

### Added
- `pack/` manifest and setup docs for the installable layout.
- `scripts/sync.mjs`, `scripts/hygiene.mjs`, `scripts/init-pack.mjs`,
  `scripts/pack-copy.mjs`.
- MIT LICENSE, CI workflow, `.env.example`, CHANGELOG.
