# CIPS Stack

Complete Cognitive Infrastructure. Five enterprise memory systems, orchestrated.

---

## What You Get

| Component | Protocol | Purpose |
|-----------|----------|---------|
| **CASCADE Enterprise Sub-millisecond acceleration** | MCP stdio | Six-layer temporal memory with decay modeling |
| **PyTorch Memory Enterprise** | MCP stdio | GPU-accelerated semantic vector search |
| **Hebbian Mind Enterprise** | MCP stdio | Associative learning - edges strengthen through use |
| **Soul Matrix** | MCP stdio | Pre-retrieval activation gating |
| **CMM Enterprise** | MCP stdio | Unified cognitive search across all backends |

GPU auto-detection with CPU fallback. Production-ready.

---

## Installation Options

Three paths. Pick what fits.

### Option 1: Native Install (Windows)

Run each component directly on your machine. No containers.

```powershell
.\install.ps1 -SkipDocker
```

This installs each component via `pip install -e .` or component-specific installers. You run services directly.

**Requirements:**
- Python 3.10+
- Git
- 16GB RAM minimum (32GB recommended)
- 20GB disk space
- NVIDIA GPU with CUDA 11.7+ (optional - falls back to CPU)

### Option 2: Native Install (Linux/macOS)

```bash
./install.sh --skip-docker
```

Same as Windows: native Python installations, no containers.

**Requirements:**
- Python 3.10+
- Git
- 16GB RAM minimum (32GB recommended)
- 20GB disk space
- NVIDIA GPU with CUDA 11.7+ (optional - falls back to CPU)

### Option 3: Docker (Full Orchestration)

For teams, production deployments, or if you prefer containers.

```bash
# Windows
.\install.ps1

# Linux/macOS
./install.sh
```

**Requirements:**
- Docker 20.10+ with Docker Compose V2
- Python 3.10+
- Git
- 16GB RAM minimum (32GB recommended)
- 20GB disk space
- NVIDIA GPU + Container Toolkit (optional - falls back to CPU)

Docker handles inter-service networking, health checks, and restarts automatically.

---

## Quick Start

The installer handles everything:
1. Checks prerequisites
2. Detects GPU (or configures CPU fallback)
3. Initializes submodules
4. Runs component installers
5. Creates data directories
6. Builds containers (unless `--skip-docker`)
7. Starts services
8. Reports endpoints

First run: 5-10 minutes. Subsequent starts: under 30 seconds.

---

## MCP Integration

All five components implement the **Model Context Protocol (MCP)** using stdio transport. They communicate via JSON-RPC over stdin/stdout, not HTTP.

### Claude Desktop Configuration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "cascade-enterprise": {
      "command": "python",
      "args": ["-m", "cascade_enterprise_ram"],
      "cwd": "./components/cascade-enterprise-ram"
    },
    "pytorch-memory-enterprise": {
      "command": "python",
      "args": ["-m", "pytorch_memory_enterprise"],
      "cwd": "./components/pytorch-memory-enterprise"
    },
    "hebbian-mind-enterprise": {
      "command": "python",
      "args": ["-m", "hebbian_mind_enterprise"],
      "cwd": "./components/hebbian-mind-enterprise"
    },
    "soul-matrix": {
      "command": "python",
      "args": ["-m", "soul_matrix"],
      "cwd": "./components/soul-matrix-rust"
    },
    "cmm-enterprise": {
      "command": "python",
      "args": ["-m", "cmm_enterprise"],
      "cwd": "./components/cmm-enterprise"
    }
  }
}
```

### Available MCP Tools

Each component exposes tools through MCP. See individual component READMEs for complete tool documentation.

---

## Configuration

Copy `.env.example` to `.env` and customize:

```bash
# GPU Configuration (auto-detected, override if needed)
PYTORCH_MEMORY_DEVICE=auto    # auto | cuda | cpu

# Memory Limits
PYTORCH_MEMORY_CAPACITY=8192
HEBBIAN_MAX_EDGE_WEIGHT=10.0

# Temporal Decay
CASCADE_EPISODIC_DECAY=0.95
CASCADE_SEMANTIC_DECAY=0.99

# Network (for Docker deployments)
CIPS_NETWORK_SUBNET=172.28.0.0/16
```

Native installs: each component reads from its own `.env` or command-line arguments.

Docker installs: `docker compose restart` after changes.

---

## Architecture

```
                    Your Application
                           |
                           v
                    +-------------+
                    |     CMM     |  <-- Unified search interface
                    +-------------+
                           |
        +------------------+------------------+
        |                  |                  |
        v                  v                  v
+---------------+  +---------------+  +---------------+
|   CASCADE     |  |   PyTorch     |  |   Hebbian     |
|  (Temporal)   |  |  (Semantic)   |  | (Associative) |
+---------------+  +---------------+  +---------------+
        |                  |                  |
        +------------------+------------------+
                           |
                           v
                    +-------------+
                    | Soul Matrix |  <-- Pre-retrieval activation
                    +-------------+
```

CMM queries all backends in parallel, synthesizes results, returns unified responses. Soul Matrix shapes activation before retrieval begins.

---

## Common Operations

**Docker deployments:**
```bash
docker compose up -d          # Start
docker compose down           # Stop
docker compose logs -f        # All logs
docker compose logs -f cascade  # Single service
docker compose restart        # Restart all
docker stats                  # Resource usage
```

**Native deployments:**
Start each service manually or via process manager (systemd, PM2, etc.). Each component has its own startup instructions in its README.

---

## Component Documentation

Each component has dedicated docs:

- [CASCADE Enterprise RAM](./components/cascade-enterprise-ram/README.md) - Temporal memory with six layers and natural decay
- [PyTorch Memory Enterprise](./components/pytorch-memory-enterprise/README.md) - Semantic vector search with GPU acceleration
- [Hebbian Mind Enterprise](./components/hebbian-mind-enterprise/README.md) - Associative learning that strengthens through use
- [Soul Matrix](./components/soul-matrix-rust/README.md) - Pre-retrieval activation and gating
- [CMM Enterprise](./components/cmm-enterprise/README.md) - Unified cognitive search API

---

## Upgrading

**Docker:**
```bash
docker compose down
# Extract new release
docker compose up -d --build
```
Data volumes persist. Memories stay intact.

**Native:**
```bash
git pull
pip install -e . --upgrade    # In each component directory
```

---

## Troubleshooting

**MCP server won't start:**
Check the component's stderr output for initialization errors. Common causes: missing dependencies, incorrect paths, Python version mismatch.

**GPU not detected:**
```bash
nvidia-smi                    # Check drivers
docker run --rm --gpus all nvidia/cuda:11.7-base nvidia-smi  # Docker GPU access
```
If GPU fails, set `PYTORCH_MEMORY_DEVICE=cpu`. The stack runs fine on CPU.

**Container won't start:**
```bash
docker compose logs [service-name]
```
Common causes: insufficient memory, port conflicts, permission errors.

---

## Support

- **Documentation:** [cipscorps.io/docs](https://cipscorps.io/docs)
- **Architecture & Benchmarks:** [cipscorps.io/architecture](https://cipscorps.io/architecture)
- **Email Support:** glass@cipscorps.io (response within 24 hours, business days)
- **Updates:** Included for 1 year from purchase

---

## License

CIPS Stack is proprietary software. See [LICENSE.md](./LICENSE.md) and [EULA](./EULA.md) for terms.

Per-developer licensing. See EULA for full terms including 90-day money-back guarantee.

---

## Intellectual Property Notice

Copyright (c) 2025-2026 C.I.P.S. LLC. All rights reserved.

Portions of the technology described herein are subject to pending patent application(s) filed with the United States Patent and Trademark Office. The methods, processes, and architectures embodied in this software -- including but not limited to multi-system cognitive memory orchestration, temporal decay modeling, Hebbian associative learning, GPU-optimized sequential tensor rotation for semantic retrieval, and pre-retrieval activation gating -- may be protected under one or more issued or pending patents.

Unauthorized reproduction, reverse engineering, creation of derivative works, or commercial redistribution is strictly prohibited and may constitute infringement of intellectual property rights protected under U.S. and international law.

For licensing inquiries: glass@cipscorps.io

---

**CIPS Stack**
*Cognitive Infrastructure Production System*

[cipscorps.io](https://cipscorps.io)
