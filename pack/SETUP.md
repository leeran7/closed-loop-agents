# Add closed-loop agents to a repo

This is the file-structure write-up. Follow it once; agents stay generic and
read **your** `context/` after that.

## This repo is the package

| Repo | Role |
|------|------|
| [leeran7/closed-loop-agents](https://github.com/leeran7/closed-loop-agents) | **Owns the package.** Install it as a dependency; `context/` here is placeholders. |
| [leeran7/building-blocks](https://github.com/leeran7/building-blocks) | **A consumer.** Depends on this package, fills in `context/`, and can override or extend any agent/skill locally. `app/` is The Climb. |

New or existing repo — add the dependency and generate the platform files:

```bash
yarn add -D github:leeran7/closed-loop-agents#main
# fill in context/profile.json, gates.json, trust.md, git.md, conventions.md
npx closed-loop-agents sync
```

Can't take a package dependency (offline, air-gapped)? Vendor a full copy
instead — you then own keeping it current by hand:

```bash
git clone https://github.com/leeran7/closed-loop-agents.git /tmp/cla
node /tmp/cla/scripts/init-pack.mjs /path/to/your-repo
```

There are three kinds of files:

| Kind | Who edits | Where it lives |
|------|-----------|-----------------|
| **Package** | Only closed-loop-agents itself | `node_modules/closed-loop-agents/` (or vendored, in `init` mode) |
| **Local override / addition** | You, per repo, when a role or skill needs to differ | Your repo's own `agents/`, `skills/`, `handoffs/schema.json` — same filename as the package's wins |
| **Context** | You, in every consuming repo | `context/` — never copied from the package |
| **Generated** | Nobody — `sync` rebuilds it | `.cursor/`, `.claude/`, `.codex/`, `.agents/` |

## How `sync` resolves each file

For every agent, skill, and the handoffs schema, `closed-loop-agents sync`
looks in **your repo first**, then falls back to the package:

- A file that exists **only in the package** → used as-is (a repo that adds
  nothing gets every package default).
- A file with the **same name in your repo** → your version wins entirely
  (a customized `verifier.md`, a swapped-in `skills/my-skill/`, your own
  `handoffs/schema.json` with extra fields your orchestrator needs).
- A file that exists **only in your repo** → included too (a repo-specific
  agent like `software-engineer.md`, or any skill unrelated to closed loop).

Don't want every package agent synced (most repos won't run all of them)?
Set `context/profile.json` `agentRoster` to the exact names you want:

```json
{ "agentRoster": ["software-engineer", "verifier", "reviewer", "security-reviewer", "qa-acceptance", "integrator"] }
```

Omit it and every agent name found (package ∪ your overrides) is synced.

## Tree after setup

```
your-repo/
│
├── context/                      ← YOU. This repo's facts. Agents only point here.
│   ├── README.md                 index: what to read, in what order
│   ├── profile.json              name, stack, package managers, paths, agentRoster
│   ├── gates.json                CI commands + how each was proven to fail
│   ├── trust.md                  irreversible writes, money, secrets
│   ├── git.md                    remote, default branch, PR vs trunk
│   └── conventions.md            how to match this codebase
│
├── loop/                         ← YOU (memory) + runtime (gitignored)
│   ├── learnings.md              this repo's ledger (version this)
│   ├── learnings.jsonl           append-only events (version this)
│   ├── handoffs/                 per-run; gitignored
│   └── state.json                per-run; gitignored
│
├── agents/                       ← OPTIONAL local overrides/additions only.
│   ├── claude.config.json        Omit entirely to take every package default;
│   └── my-custom-role.md         same filename as the package overrides it.
│
├── skills/                       ← OPTIONAL local overrides/additions only.
│   └── closed-loop/              Omit to take the package's closed-loop skill
│       └── *.md                  as-is; add other skill dirs freely.
│
├── handoffs/schema.json          ← OPTIONAL. Present → wins over the package's.
│
├── node_modules/closed-loop-agents/   ← THE PACKAGE (installed dependency).
│   ├── agents/*.md, agents/claude.config.json
│   ├── skills/closed-loop/*.md
│   ├── handoffs/schema.json
│   ├── orchestrator/              `closed-loop-agents loop "goal"` (Cursor SDK)
│   ├── bin/cli.mjs                the `closed-loop-agents` command
│   └── scripts/{sync,hygiene,init-pack,pack-copy}.mjs
│
├── .cursor/agents/                ← GENERATED. Do not edit.
├── .claude/agents/                ← GENERATED.
├── .codex/agents/                 ← GENERATED. TOML.
├── .cursor/skills/, .claude/skills/, .agents/skills/  ← GENERATED, symlinked.
├── .cursor/handoffs/, .claude/handoffs/               ← GENERATED, symlinked.
├── .cursor/rules/                 ← GENERATED, symlinked from .claude/rules/ if present.
└── AGENTS.md                      ← GENERATED, symlinked to CLAUDE.md if present.
```

Product code (`app/`, libraries, DESIGN.md, etc.) stays wherever the host
repo already puts it. Point `context/profile.json` `paths.design` at the
live design file. **Do not copy tokens into `agents/`.**

## 5-minute install

```bash
yarn add -D github:leeran7/closed-loop-agents#main
```

Then fill in **your** context — this is the only required human step:

1. `context/profile.json` — package managers per path, stack, `paths.design`,
   `agentRoster` if you don't want every package agent
2. `context/gates.json` — real lint/test/typecheck commands, each with `proveFail`
3. `context/trust.md` — this product's money paths and irreversible writes
4. `context/git.md` — remotes and branch policy
5. `context/conventions.md` — "match this tree"

```bash
npx closed-loop-agents sync
yarn --cwd node_modules/closed-loop-agents/orchestrator install
npx closed-loop-agents loop "smoke test"
```

Invoke `@orchestrator` (Cursor), `/closed-loop` (Claude Code), or
`closed-loop-agents loop "Build …"` (`CURSOR_API_KEY`).

If your repo needs its own customized orchestrator (product-specific gating
logic beyond the generic pipeline), keep a local `orchestrator/` — a repo
that has one should point its own `loop` script at it instead of the
package's.

## What agents read (in order)

Every role file starts with: read `context/README.md`, then the files it
lists. Sync also prepends `skills/closed-loop/protocol.md`. Runtime memory
is `loop/learnings.md`. Kernel rules are `skills/closed-loop/gates.md`.

```
context/          →  this product
protocol + gates  →  every product
agents/*.md       →  the job (verifier, frontend, …)
loop/learnings.md →  what this product already burned itself on
```

If `context/` is missing, agents infer from lockfiles and existing code.
They still must not invent a second stack.

## Do not copy from the package

| Leave behind | Why |
|--------------|-----|
| `app/` | Product |
| `context/` from this repo | Another product's trust/git/stack |
| `loop/learnings.md` body | Lava, Stripe-altitude, this game |
| `CLAUDE.md` as-is | Host overlay |
| `closed-loop.profile.json` | Replaced by `context/profile.json` |

## After install: commands

| Command | What |
|---------|------|
| `npx closed-loop-agents sync` | Rebuild platform agents/skills/handoffs; runs hygiene on the package first |
| `npx closed-loop-agents hygiene` | Fail if `agents/*.md` leak product facts, omit `context/README.md`, or exceed `maxAgentLines`. Lints **your repo's own** `agents/` if you keep a local `pack/hygiene-rules.json` (copy `pack/hygiene-rules.json` from the package and add your own banned strings); otherwise lints the package's own generic agents. |
| `npx closed-loop-agents loop "…"` | Programmatic closed loop (bundled orchestrator) |
| `npx closed-loop-agents init /path` | Vendor a full copy instead of depending on the package |
| Edit local `agents/` or `skills/` | Then `sync` again |

## File map (package vs context)

See `pack/MANIFEST.json` `kernel` (what ships in the package) and
`doNotCopy` (never copied by `init`). Schema for `context/profile.json`:
`pack/profile.schema.json`. Design of layers: `skills/closed-loop/pack.md`.
