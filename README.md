# OpenClaw Patterns

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

Curated, battle-tested patterns for running OpenClaw in production. Not docs — these are the things you figure out after breaking something.

## Contents

- `cron-architecture.md` — when to use `main`+`systemEvent` vs `isolated`+`agentTurn`
- `agent-heartbeat.md` — heartbeat config, per-agent scoping, and the "only those agents run" gotcha
- `heartbeat-outcome-monitoring.md` — cron green ≠ checks running; monitor the state file, not the scheduler
- `config-pitfalls.md` — config patch limitations and the files you must edit directly
- `config-backup-hygiene.md` — dated backup directories over `.bak` litter
- `destructive-op-gates.md` — confirmation gates, the `-y` bypass anti-pattern, and the checklist that makes a gate real
- `multi-agent-shipping.md` — worktree isolation, serial vs parallel dispatch, and conflict avoidance

## Usage

Each file is a standalone pattern with a problem statement, the wrong way, the right way, and the source incident that taught it.
