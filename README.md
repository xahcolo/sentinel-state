# sentinel-state

Shared runtime state for peer Sentinel nodes. Each node has its own full [Sentinel](https://github.com/xahcolo/sentinel) install and syncs through this repo.

## Layout

- `work-items/` — work queue. Each node atomically claims an item by setting `claimed_by` + `lease_expires_at`. Git's push-conflict detection enforces single-claim.
- `node-cards/` — capability advertisements. One JSON per node with `last_seen_at`, free-tier providers, current load.
- `external-lease/` — `who.txt` holds the single-writer lease for external effects (Signal egress, paid API calls > $0.10, budget approvals).
- `memory/` — gossip-synced knowledge. Each node owns `memory/<node>/` and merges peers into local context.
- `decisions/` — append-only stream of each Sydney's ranked queue per cycle. `<ISO-timestamp>-<node>.json`.
- `proposals/` — high-risk decisions awaiting 2-of-3 vote. Each has `proposal.json` + `votes/<node>.txt`.

## Protocol

See [the v5 plan section in xahcolo/sentinel](https://github.com/xahcolo/sentinel/blob/main/docs/superpowers/specs/2026-05-15-v5-peer-sentinels.md) (TBD doc).

## Architectural precedent

Modeled on Anthropic's [16-agent C compiler experiment](https://www.anthropic.com/engineering/building-c-compiler) — no orchestrator, shared git substrate, file-based task locks.
