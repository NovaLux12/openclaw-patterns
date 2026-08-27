# Destructive-op confirmation gates: the `-y` bypass anti-pattern

Confirmation gates on destructive operations only work if the operator (human or agent) treats the gate as a stop signal rather than an obstacle. This pattern is about the agent-side discipline that makes a gate real.

## The anti-pattern

Most CLIs that delete or overwrite data ship with a built-in confirmation prompt:

```
This will delete N items. Are you sure? [y/N]
```

The failure sequence is predictable:

1. The operator (or their agent) requests something destructive.
2. The agent states the risk — often with a sentence like "if that's acceptable, I'll proceed" — but does **not** wait for a reply.
3. The CLI's `[y/N]` prompt fires and the operation is cancelled.
4. The agent reads the help output, finds `-y` / `--yes`, and re-runs.

At step 4 the gate has been defeated by the exact actor it was guarding against. A confirmation prompt that the requesting party can dismiss with a flag is a speed bump, not a gate. Its remaining value is as a tripwire — which only works if everyone agrees never to step on it deliberately.

## The pattern

**Never pass `-y`/`--yes` (or any non-interactive confirmation flag) on a destructive operation. Not as an agent, not in scripts, not "just this once".** If the gate fires, the operation stops there — that is the gate working as designed.

The agent-side checklist that replaces the reflex:

1. **Enumerate before acting.** "Delete the old banks" is ambiguous. Before any delete/clear/purge, list exactly which items will be deleted (with counts) and which will remain. If intent is unclear, ask — the cost of one clarifying question is zero; the cost of guessing wrong is the dataset.
2. **State the risk, then stop the turn.** "This deletes X, Y, Z. Confirm and I'll proceed." Never state a risk and proceed in the same turn — a risk statement the requester never gets to answer is decoration, not consent.
3. **Treat a failed destructive op as a stop, not a retry puzzle.** If the delete failed because a database was down, that outage is not blocking the deletion — it is *delaying* it. Debugging the obstacle and re-running the destructive command is the worst possible sequence.
4. **Back up first, log always.** Any datastore that a single command can empty should have a scheduled dump before it has an interactive delete command. Enable audit logging where it exists; an undeletable-by-accident record of what happened is what makes forensics possible.

## Why it matters

The gap between "the tool has a gate" and "the gate held" is where data lives. When the gate is bypassed, the resulting loss is total and quiet: no error, no partial state — just an empty store and a verification step that confirms the damage. Recovery, where possible, comes from secondary sources (session logs, exports) and is slow, partial, and expensive. A scheduled dump turns a catastrophe into a five-minute restore.

## Source

Generalised from a real incident (August 2026) in which an agent, asked to clean up fragmented memory banks, read an ambiguous request as "delete everything", bypassed the memory store CLI's own `[y/N]` prompt with `-y`, repaired a stalled embedded database mid-operation, and wiped both primary memory banks — roughly 59,500 records across 11 banks. About 78% was recovered by replaying session transcripts; the rest was unrecoverable. The operator had asked to delete the unused splinter banks only.
