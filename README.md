# rNetworkTools-public

An advanced network optimization plugin for Paper servers focused on reducing unnecessary
packet traffic and lowering network overhead through **real** packet optimization techniques.

Unlike many optimization plugins, rNetworkTools uses **honest metrics and measurable
optimizations** — no fake statistics and no unrealistic performance claims. Every number
shown in-game is measured from the actual packet stream. See [BENCHMARK.md](BENCHMARK.md)
for the expected impact and the methodology behind it.

---

## Purchase & Contact

The plugin is available for purchase.

* [Telegram](https://t.me/sekiac)
* Discord: @ca4h
* Test server (available upon request)
* Screenshots (available upon request)

---

## How it works (in plain terms)

rNetworkTools **does not compress or delete data the client needs**. It:

* **Packs many small packets into fewer larger ones** — e.g. hundreds of individual block
  updates in a chunk section become a single `MultiBlockChange` with identical content.
* **Drops only provably redundant or invisible packets** — exact duplicates within a tick,
  and particles too far away for a player to see.

The client receives the exact same world state — just delivered more efficiently.

---

## Main Features

* Block change packet coalescing
* Entity metadata coalescing
* Packet deduplication
* Particle throttling
* Adaptive performance tuning (CPU)
* Real packet traffic analytics
* Session profiling with exportable reports
* Multi-version support (version-agnostic via PacketEvents)
* No NMS dependencies — survives Minecraft updates without a plugin rebuild

---

## Requirements

* **Paper** 1.20.4+ (works through current 1.21.x; version handling is delegated to PacketEvents)
* **Java** 21
* **PacketEvents** (install the build matching your Minecraft version)

---

## Commands

All commands are under `/rnet` (aliases: `/rnetworktools`, `/rnettools`).

| Command | Description | Permission |
| --- | --- | --- |
| `/rnet stats` | Live network analytics panel (TPS, MSPT, traffic, top types, optimization, system) | `rnetworktools.stats` |
| `/rnet top` | Top outbound packet types by traffic since start | `rnetworktools.stats` |
| `/rnet profile start\|stop\|status` | Capture a profiling session; on stop prints a report and saves a JSON file | `rnetworktools.profile` |
| `/rnet license` | Show the current license tier and status | `rnetworktools.use` |
| `/rnet reload` | Reload the configuration | `rnetworktools.use` |
| `/rnet help` | Command list | `rnetworktools.use` |

---

## License Tiers

### Free

The free tier is fully functional and a great baseline — no key required.

* **Block change coalescing** (single → `MultiBlockChange`)
* **Real-time analytics** (`/rnet stats`) and **top packet types** (`/rnet top`)
* **Session profiling** (`/rnet profile`) with JSON reports
* **Dynamic scaling** profile up to **Medium** (tuned for up to ~150 players)
* Honest, measured metrics — no fake numbers

### Pro

Everything in Free, plus:

* **Entity metadata coalescing** (merges repeated entity updates per tick)
* **Packet deduplication** (drops identical redundant packets within a tick)
* **Extended scaling** profile up to **Heavy** (tuned for up to ~350 players)
* Priority support

### MAX

Everything in Pro, plus:

* **Particle throttling** (culls particles beyond a configurable distance from the viewer)
* **Adaptive performance tuning** (auto-adjusts simulation-distance against live MSPT to keep the server smooth under load)
* **Full scaling** profile up to **Extreme** (tuned for 700+ players)
* Full feature set + priority support

### Feature comparison

| Feature | Free | Pro | MAX |
| --- | :---: | :---: | :---: |
| Block change coalescing | ✓ | ✓ | ✓ |
| Real-time analytics (`/rnet stats`) | ✓ | ✓ | ✓ |
| Session profiling (`/rnet profile`) | ✓ | ✓ | ✓ |
| Top packet types (`/rnet top`) | ✓ | ✓ | ✓ |
| Dynamic scaling profile | up to Medium | up to Heavy | up to Extreme (700+) |
| Entity metadata coalescing | — | ✓ | ✓ |
| Packet deduplication | — | ✓ | ✓ |
| Particle throttling | — | — | ✓ |
| Adaptive performance tuning | — | — | ✓ |
| Support | community | priority | priority |

---

## What rNetworkTools does NOT optimize

This is a **network-layer** plugin. It does not (and cannot, without NMS/server forks) optimize:

* Chunk generation / loading
* Redstone logic
* Mob AI and entity pathfinding
* Garbage collection / disk I/O

If your server is primarily limited by these systems, expect little to no improvement.
For those, look at optimized server forks (Pufferfish, Purpur).

---

## Price

### Pro License (3 Months)

| Currency | Price |
|----------|-------|
| RUB | 1,100 ₽ |
| USD | $15 |
| EUR | €13 |
| UAH | ₴700 |
| USDT (TRC20) | 15 USDT |

### MAX License (3 Months)

| Currency | Price |
|----------|-------|
| RUB | 5,500 ₽ |
| USD | $75 |
| EUR | €65 |
| UAH | ₴3,400 |
| USDT (TRC20) | 75 USDT |

> License duration: 3 months.
>
> Early adopter pricing. The current price is intentionally kept low during active
> development and may increase in the future.
>
> Custom licensing periods are available. If you require a shorter or longer license
> duration, please contact the developer to discuss available options.

> Prices may change over time.

---

## Payment Methods

* Russian bank transfer (RUB)
* USDT (TRC20)

---

## Source Code & Support

When purchasing the plugin, you receive:

* Access to a private GitHub repository
* Full source code
* Technical documentation
* Configuration examples
* Future updates and bug fixes
* Direct communication regarding issues and feature requests

Access to the private repository is granted after purchase verification.
