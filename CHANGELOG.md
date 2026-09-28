# Changelog

All notable changes to this project are documented here.

## [Unreleased]

### Added
- `skills/render-3d`: 3D rendering skill (issue #5). Plain-words prompt →
  Three.js scene module + headless preview screenshot + integration snippet.
  Pluggable model backend (`openai` / deterministic `stub`); the preview
  gate fails the run on any JS render error. Ships with an MCP-server
  wrapper (`mcp-server/server.mjs`) exposing `render-scene` and
  `preview-scene` tools for `claude mcp add`. Skill tests run in CI.

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
