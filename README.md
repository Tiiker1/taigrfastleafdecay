# FastLeafDecay

A Paper plugin that instantly decays leaves when the **last** supporting log of a tree is broken. Leaves from nearby intact trees are not affected.<br>Planned to upload sourcecode later.

## Features

- Decays leaves instantly (or after a configurable delay) when the last log of a tree is removed
- Only triggers on the **last** log — breaking a single log from a multi-log tree does nothing
- Adjacent trees with touching canopies are safe: their leaves are not decayed
- Player-placed (persistent) leaves are never touched
- Shears are respected when `respect-shears: true`
- Full vanilla drops (saplings, sticks, apples) via `breakNaturally()`
- Folia-compatible

## Requirements

- **Paper** 1.21.4+ (API 26.1.2+)
- **Java** 21+

## Installation

1. Download the jar from [Releases](https://github.com/Taigr/FastLeafDecay/releases)
2. Place it in your server's `plugins/` folder
3. Restart or run `/reload confirm`

## Configuration

```yaml
# plugins/FastLeafDecay/config.yml

# Delay in ticks before leaves decay (1 tick = 50ms)
# 0 = instant, 1 = near-instant with physics update
decay-delay: 1

# Whether to drop saplings, sticks, apples, etc.
drop-items: true

# Apply shear-specific drops when the player holds shears
respect-shears: true

# List of worlds where fast decay is enabled
# Empty list = all worlds
enabled-worlds: []
```

## How It Works

When a player breaks a log, the plugin runs a two-phase check:

1. **otherLogsExist** — BFS through log-to-log connections from the broken log's neighbors. If any connected log is found, the tree is not fully cut yet and nothing happens.

2. **findOrphanedLeaves** — BFS through leaf blocks (depth-limited to 6) starting from the broken log's neighbors. Each discovered leaf is checked against all nearby logs (scanned within a 12-block radius):
   - If the leaf is **directly adjacent** to any foreign log → skipped (supported by another tree)
   - If the leaf is within **Manhattan distance 3** of any foreign log → skipped
   - Otherwise → decayed

This three-layer defense (direct adjacency, distance-3 proximity, depth limit) ensures leaves from adjacent trees are never decayed while fully covering the cut tree's canopy.

## Building from Source

```bash
mvn clean package
```

The jar is output to `target/FastLeafDecay-<version>.jar`.

## License

MIT
