# PyTorch Memory Enterprise

**GPU-accelerated semantic memory for AI systems. Sub-2ms search. MCP native.**

---

## What It Does

Vector memory that stores and searches text by meaning. Add memories. Search semantically. Results return in under 2 milliseconds on GPU.

Works with Claude Desktop, Claude Code, or any MCP client.

---

## Requirements

- Python 3.10+
- PyTorch 2.0+
- CUDA toolkit (optional, for GPU acceleration)
- 4GB+ VRAM recommended for GPU mode

---

## Installation

Three paths. Pick the one that fits your setup.

### Windows

```bash
# Clone or extract to your preferred location
cd pytorch-memory-enterprise

# Install with pip (uses pyproject.toml)
pip install .

# Or install with dev tools
pip install ".[dev]"
```

Run the server:

```bash
python src/pytorch_memory/server.py
```

### Linux / macOS

```bash
# Clone or extract
cd pytorch-memory-enterprise

# Install
pip install .

# For CUDA support, install PyTorch with CUDA first:
pip install torch --index-url https://download.pytorch.org/whl/cu121
pip install .
```

Run the server:

```bash
python src/pytorch_memory/server.py
```

### Docker (Teams / Enterprise)

For teams or production deployments. Includes NVIDIA CUDA runtime, pre-downloaded models, and automatic health checks.

```bash
# Start with GPU support
docker-compose up -d

# View logs
docker-compose logs -f pytorch-memory

# Stop
docker-compose down
```

The Docker image:
- Uses CUDA 12.1 with NVIDIA runtime
- Pre-downloads the embedding model (no cold start delay)
- Exposes port 9998 for socket connections
- Persists checkpoints across restarts

For Sub-millisecond acceleration (ultra-low latency):

```bash
docker-compose --profile ramdisk up -d
```

---

## Verify Installation

Add to your Claude Desktop config (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "pytorch-memory": {
      "command": "python",
      "args": ["C:/path/to/pytorch-memory-enterprise/src/pytorch_memory/server.py"]
    }
  }
}
```

Restart Claude Desktop. Ask: "What's the pytorch memory status?"

---

## Configuration

Set via environment variables or `.env` file:

```bash
# Device
PYTORCH_MEMORY_DEVICE=cuda          # cuda, cpu, or auto (default: auto)

# Memory
PYTORCH_MEMORY_CAPACITY=4096        # Pre-allocated vector slots
PYTORCH_MEMORY_DIMENSION=384        # Embedding dimension

# Persistence
PYTORCH_MEMORY_CHECKPOINT_DIR=./checkpoints
PYTORCH_MEMORY_SAFE_BASE=/path/to/data    # Required for production

# Socket Server (disabled by default)
PYTORCH_MEMORY_SOCKET_ENABLED=false
PYTORCH_MEMORY_SOCKET_HOST=localhost
PYTORCH_MEMORY_SOCKET_PORT=9998

# Model
PYTORCH_MEMORY_MODEL=all-MiniLM-L6-v2
```

For Docker, configure in `docker-compose.yml` or pass via environment.

---

## API Reference

Six MCP tools. All return JSON.

### pytorch_memory_search

Find memories by semantic similarity.

```json
{
  "query": "conversations about project deadlines",
  "top_k": 10
}
```

Returns:
```json
{
  "results": [
    {
      "id": "mem_abc123",
      "score": 0.8934,
      "content": "Discussed Q4 deadline with team...",
      "metadata": {"source": "meeting_notes"}
    }
  ],
  "count": 1,
  "latency_ms": 1.24,
  "query": "conversations about project deadlines"
}
```

### pytorch_memory_add

Store a single memory.

```json
{
  "content": "User prefers dark mode interfaces",
  "metadata": {"category": "preferences"},
  "memory_id": "pref_001"
}
```

The `memory_id` is optional. Auto-generated if omitted.

### pytorch_memory_batch_add

Store multiple memories efficiently.

```json
{
  "memories": [
    {"content": "First memory", "metadata": {"batch": 1}},
    {"content": "Second memory", "metadata": {"batch": 1}},
    {"content": "Third memory", "id": "custom_id_003"}
  ]
}
```

Batch operations encode all content in parallel.

### pytorch_memory_delete

Remove a memory by ID.

```json
{
  "memory_id": "mem_abc123"
}
```

Soft delete. Marked invalid but not physically removed until compaction.

### pytorch_memory_status

Get system health and statistics.

```json
{}
```

Returns:
```json
{
  "total_memories": 2547,
  "valid_memories": 2540,
  "dimension": 384,
  "capacity": 4096,
  "gpu_memory_mb": 127.5,
  "device": "cuda",
  "uptime_seconds": 3600.0,
  "embedding_cache": {
    "size": 1200,
    "max_size": 2000,
    "hit_rate": 0.342
  },
  "operations": {
    "searches": 15420,
    "adds": 2540,
    "deletes": 7
  }
}
```

### pytorch_memory_checkpoint

Force save current state to disk.

```json
{}
```

Auto-saves on shutdown. Use this for explicit saves during long sessions.

---

## Integration Examples

### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "pytorch-memory": {
      "command": "python",
      "args": ["/absolute/path/to/src/pytorch_memory/server.py"],
      "env": {
        "PYTORCH_MEMORY_DEVICE": "cuda"
      }
    }
  }
}
```

### Claude Code

Add to your project's `.mcp.json`:

```json
{
  "mcpServers": {
    "pytorch-memory": {
      "command": "python",
      "args": ["/absolute/path/to/src/pytorch_memory/server.py"]
    }
  }
}
```

### Direct Socket Connection

When socket server is enabled, connect on port 9998 for direct TCP communication.

```python
import socket
import json

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect(("localhost", 9998))

request = {"command": "search", "query": "test", "top_k": 5}
sock.send(json.dumps(request).encode() + b"\n")

response = json.loads(sock.recv(65536).decode())
print(response)
```

---

## GPU vs CPU Mode

**GPU Mode** (CUDA available):
- Sub-2ms search latency
- ~128MB VRAM at 2,500 vectors
- float16 precision
- Parallel batch encoding

**CPU Mode** (CUDA unavailable):
- 10-50ms search latency
- Full functionality, just slower

Auto-detects. Uses GPU when available.

Force CPU mode:

```bash
PYTORCH_MEMORY_DEVICE=cpu python src/pytorch_memory/server.py
```

---

## Troubleshooting

### Server won't start

Check Python version:
```bash
python --version  # Needs 3.10+
```

Check PyTorch:
```bash
python -c "import torch; print(torch.__version__)"
```

Check CUDA:
```bash
python -c "import torch; print(torch.cuda.is_available())"
```

### Out of GPU memory

Reduce `PYTORCH_MEMORY_CAPACITY` or use CPU mode. Each vector uses ~1.5KB at float16.

### Slow search

- Verify GPU mode: check status for `"device": "cuda"`
- Warm up the embedding cache
- Check for GPU competition from other processes

### Checkpoint won't load

Delete and rebuild:

```bash
rm -rf checkpoints/
python src/pytorch_memory/server.py
```

### First call is slow

Embedding model loads on first use. Takes 5-10 seconds. Subsequent calls are fast.

---

## Security Notes

Production deployments should set:

```bash
PYTORCH_MEMORY_SAFE_BASE=/path/to/data  # All paths validated within this directory
PYTORCH_MEMORY_SOCKET_ENABLED=false     # Socket disabled by default
PYTORCH_MEMORY_API_KEYS=key1,key2       # If socket enabled, require authentication
```

Path traversal attacks are blocked. All paths resolve within `SAFE_BASE`.

---

## Support

**Documentation:** [cipscorps.io](https://cipscorps.io)

**Email:** glass@cipscorps.io

**Issues:** Contact via Gumroad or email

---

## License

Proprietary software. See `LICENSE` for terms.

- Perpetual license, use forever
- Per Developer $60
- One year of updates included
- Source code provided for transparency
- 90-day money-back guarantee

---

*PyTorch Memory Enterprise by C.I.P.S. LLC*
