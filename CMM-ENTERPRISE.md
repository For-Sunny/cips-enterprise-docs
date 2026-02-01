# CMM Enterprise

**One query. All memory.**

---

## What It Does

CMM Enterprise searches your entire memory infrastructure with a single query. Not aggregation. Synthesis.

Three backends. One result. Real understanding.

- **CASCADE** - Temporal memory with natural decay
- **PyTorch Memory** - Semantic vector search
- **Hebbian Mind** - Associative concept networks

Each backend sees differently. CMM synthesizes what they find into something none could produce alone.

---

## Why This Matters

Memory backends are siloed. You search one, you miss what the others know.

CMM doesn't just query all three and concatenate results. It clusters related memories, identifies gaps in understanding, resolves contradictions, and synthesizes a coherent response with confidence scores.

The synthesis is the product. Everything else is plumbing.

---

## Features

**Unified Cognitive Search**
One query hits all backends simultaneously. Results are scored, weighted, and synthesized.

**Neural Gate Integration**
Pre-retrieval boosting through Soul Matrix. Context shapes what surfaces before search begins.

**Tri-Save Persistence**
Save to all backends atomically. Write once, remember everywhere.

**Query Expansion**
Your query expands into related concepts. Search finds what you meant, not just what you typed.

**Five Search Modes**
From 5ms quick scans to comprehensive deep analysis. Match latency to need.

---

## Installation

CMM Enterprise is a TypeScript MCP server. Choose your path.

### Native Install (Windows / Linux / macOS)

Requires Node.js 18+.

```bash
# Clone or download
cd cmm-enterprise

# Install dependencies
npm install

# Build TypeScript
npm run build

# Run
npm start
```

For development with hot reload:

```bash
npm run dev
```

### Docker

```bash
# Build and run
docker-compose up -d

# Or build manually
docker build -t cmm-enterprise .
docker run -d cmm-enterprise
```

### Add to Claude Desktop

Add to your MCP config (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "cmm-enterprise": {
      "command": "node",
      "args": ["/path/to/cmm-enterprise/dist/index.js"]
    }
  }
}
```

---

## Configuration

Environment variables:

```bash
# Backend connections
CMM_VECTOR_HOST=localhost
CMM_VECTOR_PORT=9998
CMM_HEBBIAN_HOST=localhost
CMM_HEBBIAN_PORT=9995
CMM_CASCADE_HOST=localhost
CMM_CASCADE_PORT=3000

# Backend Weights (must sum to 1.0)
CMM_CASCADE_WEIGHT=0.35
CMM_PYTORCH_WEIGHT=0.40
CMM_HEBBIAN_WEIGHT=0.25

# Timeouts (ms)
CMM_SEARCH_TIMEOUT=5000
CMM_SYNTHESIS_TIMEOUT=3000

# Neural Gate
CMM_GATE_ENABLED=true
CMM_GATE_BOOST_FACTOR=1.5

# Cache
CMM_CACHE_TTL=300
CMM_CACHE_MAX_SIZE=1000
```

Backend weights control synthesis influence. Adjust based on your use case:
- Heavy temporal context? Increase CASCADE weight
- Semantic similarity critical? Boost PyTorch weight
- Concept relationships matter? Raise Hebbian weight

---

## API Reference

CMM exposes MCP tools for integration with Claude and other MCP clients.

### cognitive_search

Primary search interface.

```typescript
{
  "tool": "cognitive_search",
  "arguments": {
    "query": "authentication flow for JWT tokens",
    "mode": "standard",
    "limit": 10,
    "threshold": 0.3
  }
}
```

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | yes | Search query |
| `mode` | string | no | Search depth (default: "standard") |
| `limit` | number | no | Max results (default: 10) |
| `threshold` | number | no | Minimum relevance score (default: 0.3) |

**Response:**
```json
{
  "results": [...],
  "synthesis": "Unified narrative of findings",
  "confidence": 0.87,
  "sources": {
    "cascade": 4,
    "pytorch": 3,
    "hebbian": 2
  },
  "latency_ms": 26
}
```

### cognitive_save_synthesis

Tri-save to all backends.

```typescript
{
  "tool": "cognitive_save_synthesis",
  "arguments": {
    "content": "Memory content to save",
    "metadata": {
      "source": "user_interaction",
      "importance": 0.8,
      "tags": ["auth", "security"]
    }
  }
}
```

Writes atomically to CASCADE, PyTorch Memory, and Hebbian Mind. If any backend fails, the operation rolls back.

### get_memory_context

Retrieve context for a topic without full synthesis.

```typescript
{
  "tool": "get_memory_context",
  "arguments": {
    "topic": "database migrations",
    "depth": "shallow"
  }
}
```

Faster than `cognitive_search`. Use when you need raw context, not synthesized understanding.

### cmm_status

System status and backend health.

```typescript
{
  "tool": "cmm_status"
}
```

**Response:**
```json
{
  "status": "healthy",
  "backends": {
    "cascade": { "status": "connected", "latency_ms": 2 },
    "pytorch": { "status": "connected", "latency_ms": 3 },
    "hebbian": { "status": "connected", "latency_ms": 1 }
  },
  "cache": {
    "entries": 234,
    "hit_rate": 0.73
  },
  "uptime_seconds": 86400
}
```

### cmm_cache_stats

Cache performance metrics.

### cmm_clear_cache

Clear the query cache.

### extract_key_terms

Extract key terms from text for query expansion.

---

## Search Modes

| Mode | Latency | Use Case |
|------|---------|----------|
| `priming` | ~5ms | Pre-warm context before user interaction |
| `quick` | ~10ms | Fast lookups, simple queries |
| `standard` | ~26ms | Default. Balanced depth and speed |
| `deep` | ~100ms | Complex queries, thorough synthesis |
| `comprehensive` | ~500ms | Full analysis, gap detection, contradiction resolution |

Choose based on latency budget and query complexity. Most requests work with `standard`.

---

## Integration Examples

### Programmatic Access

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";

const client = new Client({
  name: "my-app",
  version: "1.0.0"
});

// Connect to CMM
await client.connect(transport);

// Search
const result = await client.callTool({
  name: "cognitive_search",
  arguments: {
    query: "user authentication patterns",
    mode: "standard"
  }
});

console.log(result.synthesis);
```

### HTTP Direct (Development)

```bash
curl -X POST http://localhost:8005/search \
  -H "Content-Type: application/json" \
  -d '{"query": "memory architecture", "mode": "quick"}'
```

---

## Troubleshooting

**Backend connection failures**

Check that all backend services are running. Verify backend host/port environment variables match actual service addresses.

**High latency**

- Check cache hit rate via `cmm_status`. Low hit rate means repeated similar queries aren't being cached.
- Reduce search mode depth for time-sensitive requests.
- Verify GPU is available for PyTorch Memory backend.

**Empty results**

- Lower the `threshold` parameter.
- Check that backends have data. Empty backends return empty results.
- Verify query isn't too narrow. Query expansion helps, but can't expand nothing.

**Synthesis quality issues**

Synthesis depends on backend diversity. If all results come from one backend, synthesis has less to work with. Check `sources` in response to see distribution.

---

## Development

```bash
# Run tests
npm test

# Run tests with coverage
npm run test:coverage

# Watch mode
npm run test:watch

# Clean build artifacts
npm run clean
```

---

## Support

**Documentation & Benchmarks**
[cipscorps.io](https://cipscorps.io)

**Issues**
Contact support through your Gumroad purchase.

---

## License

CMM Enterprise is bundled with the CIPS Stack. See LICENSE file for terms.

---

<p align="center">
  <strong>C.I.P.S. LLC</strong><br>
  Cognitive Infrastructure for Persistent Systems
</p>
