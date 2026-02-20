# Soul Matrix

Hebbian conductance graph for pre-token gating. Pure Rust. Sub-millisecond inference.

Part of the CIPS Stack from [cipscorps.io](https://cipscorps.io)

---

## What It Does

Before your LLM spends tokens thinking, this matrix shapes *how* it thinks.

Input: concepts you're about to reason about.
Output: related concepts that should resonate, weighted by learned associations.

The matrix learns through use. Concepts that fire together wire together. Connections that go unused decay. Over time, your AI develops faster intuition paths.

Performance: ~3-4 microseconds for raw matrix computation. ~20-50 microseconds with normalization. ~100-200 microseconds end-to-end with JSON serialization.

---

## Installation

### Prerequisites

Rust toolchain (1.70+). Install from [rustup.rs](https://rustup.rs/).

### Build from Source

Available as part of the CIPS Stack bundle. Contact glass@cipscorps.io for access.

```bash
# After purchase, you receive access to the private repository
git clone https://github.com/For-Sunny/soul-matrix-rust
cd soul-matrix-rust
cargo build --release
```

The binary lands at `target/release/soul-matrix-server` (or `.exe` on Windows).

### Run as Library

Add to your `Cargo.toml`:

```toml
[dependencies]
soul-matrix = { path = "../soul-matrix-rust" }
```

Or once published:

```toml
[dependencies]
soul-matrix = "0.1"
```

---

## Quick Start

### As MCP Server

The server speaks JSON-RPC over stdio. Point your MCP client at the binary.

```bash
./target/release/soul-matrix-server \
  --matrix path/to/soul_matrix.bin \
  --map path/to/concept_map.json
```

Or use environment variables:

```bash
export SOUL_MATRIX_PATH=path/to/soul_matrix.bin
export SOUL_MAP_PATH=path/to/concept_map.json
./target/release/soul-matrix-server
```

### Claude Desktop Configuration

```json
{
  "mcpServers": {
    "soul-matrix": {
      "command": "C:/path/to/soul-matrix-server.exe",
      "args": [
        "--matrix", "C:/path/to/soul_matrix.bin",
        "--map", "C:/path/to/concept_map.json"
      ]
    }
  }
}
```

### As Rust Library

```rust
use soul_matrix::{SoulMatrix, MatrixConfig};
use std::path::PathBuf;

let config = MatrixConfig::default();
let mut matrix = SoulMatrix::from_files(
    PathBuf::from("soul_matrix.bin"),
    PathBuf::from("concept_map.json"),
    config,
).expect("Failed to load matrix");

// Inject concepts, get resonance
let result = matrix.inject(&["consciousness".to_string(), "memory".to_string()]);

for r in result.resonance {
    println!("{}: {:.2}%", r.concept, r.score);
}
```

Run the example:

```bash
cargo run --example simple
```

---

## MCP Tools

Six tools exposed via stdio:

| Tool | Purpose |
|------|---------|
| `soul_inject` | Inject concepts, return resonance pattern |
| `soul_think` | Inject with narrative interpretation |
| `soul_ground` | Emergency stabilization (90% damping) |
| `soul_status` | Matrix stats: nodes, edges, learning rate |
| `soul_ping` | Quick health check |
| `soul_connections` | Show what connects to a concept |

### Example: Inject

Request:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "soul_inject",
    "arguments": {
      "concepts": ["authentication", "security"]
    }
  }
}
```

Response:
```json
{
  "status": "RESONANCE",
  "data": [
    {"pin": 42, "concept": "jwt", "score": 89.5},
    {"pin": 18, "concept": "session", "score": 84.2}
  ],
  "activated": [
    {"pin": 5, "concept": "authentication"},
    {"pin": 12, "concept": "security"}
  ],
  "compute_mode": "Rust-CPU",
  "inference_us": 47.3
}
```

---

## Configuration

### CLI Arguments

| Argument | Env Variable | Description |
|----------|--------------|-------------|
| `--matrix` | `SOUL_MATRIX_PATH` | Path to binary matrix file |
| `--map` | `SOUL_MAP_PATH` | Path to JSON concept map |
| `--log` | `SOUL_LOG_PATH` | Optional debug log file |
| `--ground-pin` | `SOUL_GROUND_PIN` | Primary ground pin index |
| `--frequency-pin` | `SOUL_FREQUENCY_PIN` | Secondary ground pin |
| `--learning-rate` | `SOUL_LEARNING_RATE` | Hebbian learning rate (default: 0.01) |
| `--decay-rate` | `SOUL_DECAY_RATE` | Hebbian decay rate (default: 0.999) |
| `--top-n` | - | Top resonance results to return (default: 15) |

### MatrixConfig (Library)

```rust
MatrixConfig {
    matrix_size: 256,        // NxN matrix dimension
    top_n_resonance: 15,     // Results to return
    top_n_connections: 20,   // Connections to show
    learning_rate: 0.01,     // Hebbian strengthening
    decay_rate: 0.999,       // Global decay per operation
    ground_pin: Some(0),     // Primary ground
    frequency_pin: Some(1),  // Secondary ground
}
```

---

## Data Files

### Matrix Binary (`soul_matrix.bin`)

Raw bytes. 256x256 = 65,536 bytes. Each byte represents edge weight 0-255 (normalized to 0.0-1.0 internally).

Row-major order. Element [i,j] at offset `i * 256 + j`.

### Concept Map (`concept_map.json`)

```json
{
  "meta": {
    "node_count": 133,
    "edge_count": 6500,
    "matrix_size": 256
  },
  "index_map": {
    "0": "consciousness",
    "1": "memory",
    "2": "partnership"
  }
}
```

Keys are pin indices (as strings). Values are concept names. Lookup is case-insensitive.

---

## Performance

| Operation | Time |
|-----------|------|
| Raw matvec (256x256) | 3-4 us |
| Inject with normalization | 20-50 us |
| End-to-end with JSON | 100-200 us |
| Matrix size | 65,536 bytes |
| Memory footprint | <10 MB |

The matrix fits in L1 cache. No disk access during inference.

Run benchmarks:

```bash
cargo bench
```

---

## How It Works

1. **Concepts map to pins** - Each concept gets an index (0-255)
2. **Inject creates input vector** - 1.0 at activated pins, 0.0 elsewhere
3. **Matrix-vector multiply** - Resonance spreads through learned connections
4. **Normalize and rank** - Top scoring concepts returned
5. **Hebbian update** - Co-activated pins strengthen their connection
6. **Decay** - All weights decay slightly, preventing saturation

The learning is unsupervised. Use it, it learns. Stop using connections, they fade.

---

## Building

```bash
# Debug build
cargo build

# Release build (optimized)
cargo build --release

# Run tests
cargo test

# Run benchmarks
cargo bench

# Run example
cargo run --example simple
```

---

## Platform Support

Works anywhere Rust compiles:

- **Windows** - Tested. Native `.exe` binary.
- **Linux** - Tested. Standard binary.
- **macOS** - Should work. Not actively tested.

No external dependencies beyond the Rust standard library and serde for JSON.

---

## License

MIT OR Apache-2.0 (standalone). Commercial license when bundled with CIPS Stack.

---

## Intellectual Property Notice

Copyright (c) 2025-2026 C.I.P.S. LLC. All rights reserved.

Portions of the technology described herein are subject to pending patent application(s) filed with the United States Patent and Trademark Office. The methods, processes, and architectures embodied in this software -- including but not limited to Hebbian conductance matrix computation, pre-token activation gating, and sub-millisecond neural graph inference -- may be protected under one or more issued or pending patents.

For licensing inquiries: glass@cipscorps.io

---

*Hebbian conductance. Sub-millisecond inference. Pre-token gating that learns.*
