# Shodh-Memory

**Persistent memory for AI agents. Single package. Local-first. Runs offline.**

[![PyPI](https://img.shields.io/pypi/v/shodh-memory.svg)](https://pypi.org/project/shodh-memory/)
[![Downloads](https://static.pepy.tech/badge/shodh-memory)](https://pepy.tech/project/shodh-memory)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

---

Give your AI agents memory that persists across sessions, learns from experience, and runs entirely on your hardware.

## Installation

```bash
pip install shodh-memory
```

That's it. No additional setup required. Models and runtime are bundled.

## Quick Start

```python
from shodh_memory import Memory

# Create memory (data stored locally)
memory = Memory(storage_path="./my_agent_data")

# Store memories
memory.remember("User prefers dark mode", memory_type="Decision")
memory.remember("JWT tokens expire after 24h", memory_type="Learning")
memory.remember("Deployment failed due to missing env var", memory_type="Error")

# Search semantically
results = memory.recall("user preferences", limit=5)
for r in results:
    print(f"{r['content']} (importance: {r['importance']:.2f})")

# Get context summary for LLM bootstrap
summary = memory.context_summary()
print(summary["decisions"])  # Recent decisions
print(summary["learnings"])  # Recent learnings
```

## Features

- **Zero setup** — Everything bundled. No API keys, no cloud, no Docker
- **Semantic search** — MiniLM embeddings for meaning-based retrieval
- **Hebbian learning** — Connections strengthen when memories are used together
- **Activation decay** — Unused memories fade naturally
- **Entity extraction** — TinyBERT NER extracts people, orgs, locations
- **100% offline** — Works on air-gapped systems

## Memory Types

Different types get different importance weights:

| Type | Weight | Use for |
|------|--------|---------|
| Decision | +0.30 | Choices, preferences, conclusions |
| Learning | +0.25 | New knowledge acquired |
| Error | +0.25 | Mistakes to avoid |
| Discovery | +0.20 | Findings, insights |
| Pattern | +0.20 | Recurring behaviors |
| Task | +0.15 | Work items |
| Context | +0.10 | General information |
| Conversation | +0.10 | Chat history |
| Observation | +0.05 | Low-priority notes |

## API Reference

### Core Memory

```python
# Store a memory
memory.remember(
    content="...",           # Required: the memory content
    memory_type="Learning",  # Optional: Decision, Learning, Error, etc.
    tags=["tag1", "tag2"],   # Optional: for filtering
    importance=0.8           # Optional: override auto-calculated importance
)

# Semantic search
results = memory.recall(
    query="...",             # Required: search query
    limit=10,                # Optional: max results (default: 10)
    mode="hybrid"            # Optional: semantic, associative, hybrid
)

# List all memories
memories = memory.list_memories(limit=100, memory_type="Decision")

# Get single memory by ID
mem = memory.get_memory("uuid-here")

# Get statistics
stats = memory.get_stats()
print(f"Total: {stats['total_memories']}, Graph edges: {stats['graph_edges']}")
```

### Forget Operations

```python
# Delete by ID
memory.forget("memory-uuid")

# Delete old memories
memory.forget_by_age(days=30)

# Delete low-importance memories
memory.forget_by_importance(threshold=0.3)

# Delete by pattern (regex)
memory.forget_by_pattern(r"test.*")

# Delete by tags
memory.forget_by_tags(["temporary", "draft"])

# Delete by date range
memory.forget_by_date(start="2024-01-01", end="2024-06-30")

# GDPR: Delete everything
memory.forget_all()
```

### Context & Introspection

```python
# Context summary for LLM bootstrap
summary = memory.context_summary(max_items=5)
# Returns: {"decisions": [...], "learnings": [...], "context": [...]}

# 3-tier memory visualization
state = memory.brain_state(longterm_limit=100)
# Returns: {"working": [...], "session": [...], "longterm": [...]}

# Memory learning activity report
report = memory.consolidation_report(since="2024-01-01")
# Returns: strengthening events, decay events, edge formations

# Flush to disk
memory.flush()
```

## LLM Framework Integration

### LangChain

```python
from shodh_memory.integrations.langchain import ShodhMemory

# Use as LangChain memory
memory = ShodhMemory(storage_path="./langchain_data")
```

### LlamaIndex

```python
from shodh_memory.integrations.llamaindex import ShodhLlamaMemory

# Use as LlamaIndex memory
memory = ShodhLlamaMemory(storage_path="./llamaindex_data")
```

## Performance

Measured on Intel i7-1355U (10 cores, 1.7GHz):

| Operation | Latency |
|-----------|---------|
| `remember()` | 55-60ms |
| `recall()` (semantic) | 34-58ms |
| `recall_by_tags()` | ~1ms |
| `list_memories()` | ~1ms |

## Architecture

Experiences flow through three tiers based on Cowan's working memory model:

```
Working Memory ──overflow──> Session Memory ──importance──> Long-Term Memory
   (100 items)                  (500 MB)                      (RocksDB)
```

**Cognitive processing:**
- Spreading activation retrieval
- Activation decay (exponential)
- Hebbian strengthening (co-retrieval strengthens connections)
- Long-term potentiation (frequently-used connections become permanent)
- Memory replay during maintenance
- Interference detection

## Platform Support

| Platform | Status |
|----------|--------|
| Windows x86_64 | Supported |
| Linux x86_64 | Supported |
| macOS ARM64 (Apple Silicon) | Supported |
| macOS x86_64 (Intel) | Supported |

## Links

- [GitHub](https://github.com/varun29ankuS/shodh-memory)
- [Documentation](https://www.shodh-rag.com/memory)
- [npm (MCP Server)](https://www.npmjs.com/package/@shodh/memory-mcp)
- [crates.io (Rust)](https://crates.io/crates/shodh-memory)

## License

Apache 2.0
