# Benchmarks

This document describes the expected performance characteristics of rNetworkTools.

> ### ⚠️ These are estimates, not measured results
>
> The numbers below are **theoretical projections derived mathematically from the
> plugin's packet-handling logic** — packet sizes from the Minecraft protocol, the
> coalescing/dedup/throttle algorithms in the source, and typical workload assumptions.
>
> They were **calculated with the help of AI (Claude Code · Claude Opus 4.8)** by
> analysing the implementation, **not** collected from real hardware load tests yet.
>
> Treat them as *reference expectations*. Once live load testing is done, this file will
> be replaced with real measurements captured by the plugin's own profiler (`/rnet profile`).

---

## How rNetworkTools changes traffic (in one sentence)

It **does not compress or delete needed data**. It **packs many small packets into fewer
larger ones** (coalescing) and **drops only provably redundant or invisible packets**
(duplicates, far-away particles). The client receives identical world state.

---

## Methodology

All estimates are built from approximate on-wire packet sizes (pre-compression) defined by
the Minecraft protocol:

| Packet | Approx. size | Notes |
| --- | --- | --- |
| `BLOCK_CHANGE` (single) | ~10–12 B | packed position (8 B) + block id varint + frame |
| `MULTI_BLOCK_CHANGE` | ~9 B header + ~3–4 B/block | section position + count + per-block varlong |
| `ENTITY_METADATA` | ~3–4 B header + fields | entity id varint + changed fields |
| `PARTICLE` | ~16–40 B | particle id + position + offsets + data |

The savings come from two effects:

1. **Header amortisation** — sending one packet header instead of *N* removes (*N − 1*)
   per-packet overheads.
2. **Local position encoding** — inside a `MULTI_BLOCK_CHANGE`, each block costs ~3–4 B
   instead of ~10–12 B as a standalone packet.

---

## Scenario 1 — Bulk block updates (single block-change spam)

Workload: an operation/plugin emitting ~1,240,000 individual `BLOCK_CHANGE` packets
(scattered edits, average ~25 blocks per chunk section).

> Applies to plugins and operations that send **individual** block changes. FAWE/WorldEdit
> already emit `MULTI_BLOCK_CHANGE` themselves and will see little additional gain — see the
> note at the end of this scenario.

### Without rNetworkTools (estimated)

| Metric | Value |
| --- | --- |
| Block Change packets | ~1,240,000 |
| Multi Block Change packets | 0 |
| Block-change traffic | ~13.6 MB |

### With rNetworkTools (estimated)

| Metric | Value |
| --- | --- |
| Block Change packets | ~0 |
| Multi Block Change packets | ~49,000 |
| Block-change traffic | ~4.8 MB |

### Result (estimated)

* **~96% fewer packets** on the block-change path (1.24M → ~49K)
* **~60–65% less traffic** for the block-change portion
* Lower per-packet CPU on the network threads (fewer encode/flush operations)
* **Overall** server traffic reduction depends on how much of your traffic is block
  changes — often small in mixed play, large during edit/build storms

> **FAWE note:** because FAWE already batches block changes, the additional gain there is
> minimal. rNetworkTools mainly helps the many plugins that still send single block updates.

---

## Scenario 2 — Entity metadata spam

Workload: plugins/mobs re-sending `ENTITY_METADATA` for the same entities multiple times
per tick (animations, holograms, equipment refreshes).

### Estimated effect

| Metric | Without | With | Reduction |
| --- | --- | --- | --- |
| Metadata packets | 100% | ~65–75% | ~25–35% |
| Metadata traffic | 100% | ~70–80% | ~20–30% |

Savings scale with how much redundant metadata your plugins produce — merging *K*
consecutive updates for one entity into one packet removes (*K − 1*) headers and collapses
repeated fields to their latest value.

---

## Feature impact

| Feature | Typical benefit | Where it helps |
| --- | --- | --- |
| Block Coalescing | High | Block-change spam, builds, non-FAWE editing plugins |
| Entity Metadata Coalescing | Medium | Mob-dense areas, holograms, animated entities |
| Packet Deduplication | Medium | Plugins resending identical packets each tick |
| Particle Throttling | Low–Medium | Particle-heavy areas with many viewers |
| Adaptive Tuning | Situation-dependent | CPU-bound servers (trades view range for MSPT) |

---

## What rNetworkTools does NOT optimize

This is a **network-layer** plugin. It cannot and does not optimize:

* Chunk generation / loading
* Redstone logic
* Mob AI and entity pathfinding
* Garbage collection
* Disk I/O

Those are server-internal (game logic) systems and are the domain of optimized server forks
(Pufferfish, Purpur), not a PacketEvents plugin. If your server is primarily limited by
these, expect little to no improvement from rNetworkTools.

---

## Reproducing / validating

The plugin ships with its own profiler so you can measure the real effect on **your**
server instead of trusting these estimates:

1. Use a clean Paper server (1.20+) with PacketEvents installed.
2. Run `/rnet profile start`, perform your workload, then `/rnet profile stop`.
3. Disable rNetworkTools (or its features in `config.conf`) and repeat the same workload.
4. Compare the two profile reports — packet counts and bytes per type are recorded exactly.
5. Watch real MSPT with `/rnet stats` (sourced from the server's actual tick time).

Real measured numbers from this process will replace the estimates above.

## Honesty Policy

rNetworkTools does not advertise synthetic performance gains, fabricated percentages or impossible optimizations.

Whenever benchmark data is published, the methodology used to obtain it will also be published.

All measurements are intended to be reproducible by server owners using the plugin's built-in profiling tools.

