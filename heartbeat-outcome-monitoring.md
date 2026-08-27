# Heartbeat monitoring: cron green ≠ checks running

A heartbeat scheduler firing on time is a liveness signal. The checks themselves are an outcome. Pinning the heartbeat session to the wrong model can break the outcome completely while the liveness signal stays perfect.

## The gotcha

The heartbeat cron fired every 30 minutes and looked healthy: "wake requested" in 7–14ms, scheduler green, job listed and enabled. But the actual heartbeat session was pinned to a model that emitted the wrong `exec` tool syntax — it wrote JavaScript (`require('child_process')`) where a shell command was expected. Every tick:

1. The scheduler woke the session on time.
2. The model produced malformed tool calls; the calls failed.
3. The turn fell back to posting a stale status report instead of running checks.
4. Nothing told anyone — because the health signal everyone could see (cron status) was measuring the scheduler, not the work.

The state file went untouched for roughly 16 hours while every visible signal said the system was fine. BG checks, email polling, and calendar checks silently went 15–25 hours overdue.

## The pattern

**Monitor the outcome artifact, not the scheduler.**

- The heartbeat's contract is "checks ran and their timestamps were recorded". Verify the state file's mtime and the per-check last-run timestamps — those are the outcome. `cron list` showing green is liveness at best.
- If a check hasn't run within its cadence window, that is an incident regardless of what the scheduler says.
- **Pin the heartbeat model explicitly** to one proven against the runtime's actual tool schema (shell-command exec, not code execution). Test after every model or provider change: wait one tick, then confirm the state file advanced.
- Session transcript review is the forensic tool: the failed ticks showed the model attempting the wrong syntax and giving up.

## Why it matters

Every layered automation system has this shape: a dispatcher (green), a session that does the work (silent), and an artifact that proves the work happened (the only truth). Health dashboards naturally attach to the dispatcher because that's where the metrics live. The blind spot is structural — the dispatcher can be 100% healthy while the worker produces garbage, and nothing in the dispatcher's view will say so.

## Source

Heartbeat outage diagnosed 2026-08-26: heartbeat state file stale ~16h while the 30-minute cron fired green throughout; root cause was a pinned heartbeat model generating wrong exec tool syntax. Fix: explicit `heartbeat.model` override + state-file-mtime health checks.
