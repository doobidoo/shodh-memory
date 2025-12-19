# shodh-memory

**Persistent cognitive memory for AI agents. Local-first. Runs offline.**

[![crates.io](https://img.shields.io/crates/v/shodh-memory.svg)](https://crates.io/crates/shodh-memory)
[![Downloads](https://img.shields.io/crates/d/shodh-memory.svg)](https://crates.io/crates/shodh-memory)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

---

Give your AI agents memory that persists across sessions, learns from experience, and runs entirely on your hardware.

## Installation

```toml
[dependencies]
shodh-memory = "0.1"
```

On first use, models (~37MB) download automatically to `~/.cache/shodh-memory/`.

## Quick Start

```rust
use shodh_memory::{MemorySystem, MemoryConfig, MemoryType};
use anyhow::Result;

fn main() -> Result<()> {
    // Create memory system
    let config = MemoryConfig::default()
        .with_storage_path("./my_agent_data");
    let memory = MemorySystem::new(config)?;

    // Store memories
    memory.remember(
        "user-1",
        "User prefers dark mode",
        MemoryType::Decision,
        vec!["preferences".to_string()],
    )?;

    memory.remember(
        "user-1",
        "JWT tokens expire after 24h",
        MemoryType::Learning,
        vec!["auth".to_string()],
    )?;

    // Semantic search
    let results = memory.recall("user-1", "user preferences", 5)?;
    for mem in results {
        println!("{} (importance: {:.2})", mem.content, mem.importance);
    }

    Ok(())
}
```

## Features

- **Semantic search** — MiniLM-L6 embeddings (384-dim) for meaning-based retrieval
- **Hebbian learning** — Connections strengthen when memories co-activate
- **Activation decay** — Unused memories fade naturally (exponential decay)
- **Entity extraction** — TinyBERT NER extracts people, orgs, locations
- **Knowledge graph** — Entity relationships with spreading activation
- **3-tier architecture** — Working → Session → Long-term memory (Cowan's model)
- **100% offline** — Works on air-gapped systems after initial model download

## Memory Types

```rust
pub enum MemoryType {
    Decision,     // +0.30 importance
    Learning,     // +0.25
    Error,        // +0.25
    Discovery,    // +0.20
    Pattern,      // +0.20
    Task,         // +0.15
    Context,      // +0.10
    Conversation, // +0.10
    Observation,  // +0.05
}
```

## API Overview

### Core Operations

```rust
// Store
memory.remember(user_id, content, memory_type, tags)?;

// Semantic search
let results = memory.recall(user_id, query, limit)?;

// Tag-based search (fast, no embedding)
let results = memory.recall_by_tags(user_id, &["tag1", "tag2"], limit)?;

// Date range search
let results = memory.recall_by_date(user_id, start, end, limit)?;

// Get single memory
let mem = memory.get_memory(memory_id)?;

// List all
let all = memory.list_memories(user_id, limit)?;

// Statistics
let stats = memory.get_stats(user_id)?;
```

### Forget Operations

```rust
// Delete single
memory.forget(memory_id)?;

// Delete old memories
memory.forget_by_age(user_id, days)?;

// Delete low-importance
memory.forget_by_importance(user_id, threshold)?;

// Delete by pattern (regex)
memory.forget_by_pattern(user_id, r"test.*")?;

// Delete by tags
memory.forget_by_tags(user_id, &["temporary"])?;

// Delete date range
memory.forget_by_date(user_id, start, end)?;

// GDPR: Delete all
memory.forget_all(user_id)?;
```

### Context & Introspection

```rust
// Context summary for LLM bootstrap
let summary = memory.context_summary(user_id, max_items)?;

// 3-tier memory state
let state = memory.brain_state(user_id)?;

// Consolidation report (learning activity)
let report = memory.consolidation_report(user_id, since, until)?;

// Flush to disk
memory.flush()?;
```

## REST Server

Run the built-in HTTP server:

```rust
use shodh_memory::server::run_server;

#[tokio::main]
async fn main() -> Result<()> {
    run_server("0.0.0.0:3030").await
}
```

Or use the binary:

```bash
cargo install shodh-memory
shodh-memory-server
```

## Performance

Measured on Intel i7-1355U (10 cores, 1.7GHz):

| Operation | Latency |
|-----------|---------|
| `remember()` | 55-60ms |
| `recall()` semantic | 34-58ms |
| `recall_by_tags()` | ~1ms |
| Entity lookup | 763ns |
| Hebbian strengthen | 5.7µs |
| Graph traversal (3-hop) | 30µs |

## Configuration

```rust
let config = MemoryConfig::default()
    .with_storage_path("./data")
    .with_working_memory_capacity(100)
    .with_session_memory_limit_mb(500)
    .with_decay_factor(0.95)
    .with_maintenance_interval_secs(300);
```

Environment variables:

```bash
SHODH_MEMORY_PATH=./data
SHODH_MAINTENANCE_INTERVAL=300
SHODH_ACTIVATION_DECAY=0.95
SHODH_OFFLINE=true  # Disable auto-download
RUST_LOG=info
```

## Architecture

```
Working Memory ──overflow──> Session Memory ──importance──> Long-Term Memory
   (100 items)                  (500 MB)                      (RocksDB)
```

**Cognitive processing:**
- Spreading activation retrieval
- Exponential activation decay: A(t) = A₀ · e^(-λt)
- Hebbian strengthening on co-retrieval
- Long-term potentiation (permanent connections)
- Memory replay during maintenance
- Retroactive interference detection

## Platform Support

| Platform | Status |
|----------|--------|
| Linux x86_64 | Supported |
| macOS ARM64 | Supported |
| macOS x86_64 | Supported |
| Windows x86_64 | Supported |
| Linux ARM64 | Coming soon |

## Links

- [GitHub](https://github.com/varun29ankuS/shodh-memory)
- [Documentation](https://www.shodh-rag.com/memory)
- [PyPI (Python)](https://pypi.org/project/shodh-memory/)
- [npm (MCP Server)](https://www.npmjs.com/package/@shodh/memory-mcp)

## License

Apache 2.0
