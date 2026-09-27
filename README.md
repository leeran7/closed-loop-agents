# Closed-loop agents

Reusable multi-agent pack for Cursor, Claude Code, and Codex, published as
the `closed-loop-agents` npm package. **This repository owns the package.**
Product apps (for example
[building-blocks](https://github.com/leeran7/building-blocks)) install it
as a dependency, fill in their own `context/`, and can override or add
agents/skills locally.

## Add it to a repo

```bash
yarn add -D github:leeran7/closed-loop-agents#main
# edit context/ (profile, gates, trust, git, conventions)
npx closed-loop-agents sync
```

Offline / can't take a package dependency? Vendor a full copy instead:

```bash
git clone https://github.com/leeran7/closed-loop-agents.git /tmp/cla
node /tmp/cla/scripts/init-pack.mjs /path/to/your-repo
```

**File tree and what to edit:** [`pack/SETUP.md`](pack/SETUP.md).

Agents are generic. They only point at `context/`. Do not put product facts
in `agents/*.md`. A consuming repo overrides or extends any agent or skill
by adding a same-named (or new) file under its own `agents/` or `skills/` —
see `pack/SETUP.md` for the resolution order.

## Developing this package

```bash
yarn --cwd orchestrator install
node scripts/sync.mjs      # regenerates this repo's own .cursor/.claude/.codex
node scripts/hygiene.mjs   # fails on leaked product facts or oversized roles
```
