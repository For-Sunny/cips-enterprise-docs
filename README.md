# CIPS Enterprise Documentation

**Complete technical documentation for CIPS memory infrastructure products.**

This repository contains the full documentation for all CIPS enterprise products. Review the architecture, APIs, and integration guides before purchasing.

---

## Products

| Product | Price | Documentation |
|---------|-------|---------------|
| [CASCADE Enterprise](CASCADE-ENTERPRISE.md) | $400 | 6-layer temporal memory, sub-millisecond access |
| [PyTorch Memory](PYTORCH-MEMORY.md) | $600 | GPU vector search, <2ms semantic retrieval |
| [Hebbian Mind](HEBBIAN-MIND.md) | $800 | Associative neural graph, relationship learning |
| [Soul Matrix](SOUL-MATRIX.md) | Bundle | Identity gating, 270μs Rust inference |
| [CMM Enterprise](CMM-ENTERPRISE.md) | Bundle | Unified cognitive search across all backends |
| [CIPS Stack](CIPS-STACK.md) | $1,500 | Complete integrated stack (all 5 components) |

---

## Quick Links

- **Website:** [cipscorps.io](https://cipscorps.io)
- **Free Tier:** [CASCADE Memory Lite](https://github.com/For-Sunny/cascade-memory-lite) (MIT License)
- **Contact:** glass@cipscorps.io
- **Support:** glass@cipscorps.io

---

## What You Get

Each enterprise license includes:

- **Source code access** - Full repository with all source files
- **Docker images** - Pre-built containers for instant deployment
- **1 year updates** - All patches and improvements
- **90-day guarantee** - Full refund if not satisfied, no questions asked

---

## Request Code Review

Want to see the actual implementation before purchasing?

Email **glass@cipscorps.io** with:
- Product of interest
- Company name
- Use case
- Timeline

We'll grant 48-hour read-only access to the private repository.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      CIPS Stack                              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│   │   CASCADE   │  │   PyTorch   │  │   Hebbian   │         │
│   │  (Temporal) │  │  (Semantic) │  │(Associative)│         │
│   │    <1ms     │  │    <2ms     │  │    <1ms     │         │
│   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘         │
│          │                │                │                 │
│          └────────────────┼────────────────┘                 │
│                           │                                  │
│                    ┌──────▼──────┐                          │
│                    │     CMM     │                          │
│                    │  (Unified)  │                          │
│                    │   Search    │                          │
│                    └──────┬──────┘                          │
│                           │                                  │
│                    ┌──────▼──────┐                          │
│                    │ Soul Matrix │                          │
│                    │  (Gating)   │                          │
│                    │   270μs     │                          │
│                    └─────────────┘                          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## License

Documentation is provided for evaluation purposes. Source code requires a commercial license.

See individual product pages for licensing details.

---

*CIPSCORPS - Enterprise AI Memory Infrastructure*
