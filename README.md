# OpenClaw Patterns

Curated, battle-tested patterns for running OpenClaw in production. Not docs — these are the things you figure out after breaking something.

## Contents

- `cron-architecture.md` — when to use `main`+`systemEvent` vs `isolated`+`agentTurn`
- `agent-heartbeat.md` — heartbeat config, per-agent scoping, and the "only those agents run" gotcha
- `config-pitfalls.md` — config patch limitations and the files you must edit directly
- `multi-agent-shipping.md` — worktree isolation, serial vs parallel dispatch, and conflict avoidance

## Usage

Each file is a standalone pattern with a problem statement, the wrong way, the right way, and the source incident that taught it.
