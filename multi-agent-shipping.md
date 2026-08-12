# Multi-agent shipping patterns

When dispatching >1 builder to a single repo, use git worktrees to avoid file-level conflicts. Default to serial dispatch unless files are disjoint.

## The pattern

```bash
# Per-builder worktree isolation
git worktree add ../repo-builder-1 main
git worktree add ../repo-builder-2 main

# Each builder works in its own worktree, pushes its own branch
# Merge order matters: satisfy cross-branch CI coupling before merging
```

## When to parallelize

- Files are disjoint (different directories, different file types)
- No cross-branch CI dependency
- Each builder has a stable task name and clear scope

## When to serialize

- Changes touch the same files or modules
- CI jobs depend on each other (e.g., Rust type generation → frontend null handling)
- Risk of merge conflicts is high

## The merge-order rule

If PR A introduces a Rust type change and PR B adds frontend null handling for that type, PR B must merge **after** PR A. If they become mutually dependent, merge one into the other instead of leaving them as separate PRs.

## Source

A Rust+TS desktop-app release (ts-rs generated types + svelte-check cross-PR coupling incident).
