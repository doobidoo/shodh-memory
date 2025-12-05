<p align="center">
  <img src="https://raw.githubusercontent.com/varun29ankuS/shodh-memory/main/assets/logo.png" width="120" alt="Shodh-Memory">
</p>

<h1 align="center">Shodh-Memory</h1>

<p align="center">
  <strong>Local-first AI memory for robotics, drones, and edge devices</strong>
</p>

<p align="center">
  <a href="https://www.shodh-rag.com/memory"><img src="https://img.shields.io/badge/Website-shodh--rag.com-blue" alt="Website"></a>
  <a href="https://pypi.org/project/shodh-memory/"><img src="https://img.shields.io/pypi/v/shodh-memory.svg" alt="PyPI"></a>
  <a href="https://github.com/varun29ankuS/shodh-memory/actions"><img src="https://github.com/varun29ankuS/shodh-memory/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache%202.0-blue.svg" alt="License"></a>
</p>

---

## Why Shodh-Memory?

Most AI memory solutions require cloud connectivity. **Shodh-Memory runs 100% offline** - critical for:

- **Robots** operating in warehouses, mines, or disaster zones
- **Drones** flying beyond network coverage
- **Defense systems** requiring air-gapped operation
- **Privacy-sensitive** applications

Built in Rust with Python bindings. ~50MB footprint. Sub-millisecond retrieval.

## How Memory Works

### Three Pillars: Grammar, Hebbian Learning, and Decay

Shodh-Memory is built on three principles from cognitive science:

1. **Grammar as Structure** - Nouns become entities, verbs become relationships, adjectives modify importance.
2. **Hebbian Learning** - Connections used together strengthen together. After 3 co-activations, they resist decay.
3. **Salience-Weighted Decay** - Important memories persist; unimportant ones fade to gist (not arbitrary FIFO).

```
                              ┌─────────────────────────────────────────────────────────────────┐
                              │                    GRAMMATICAL DECOMPOSITION                    │
                              │                                                                 │
                              │    "The drone detected a critical obstacle near the hangar"    │
                              │         │              │           │              │             │
                              │         ▼              ▼           ▼              ▼             │
                              │    ┌─────────┐   ┌──────────┐ ┌──────────┐  ┌──────────┐       │
                              │    │  NOUN   │   │   VERB   │ │ADJECTIVE │  │   NOUN   │       │
                              │    │ "drone" │   │"detected"│ │"critical"│  │"obstacle"│       │
                              │    └────┬────┘   └────┬─────┘ └────┬─────┘  └────┬─────┘       │
                              │         │             │            │             │              │
                              └─────────│─────────────│────────────│─────────────│──────────────┘
                                        │             │            │             │
                                        ▼             ▼            ▼             ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                      GRAVITATIONAL MEMORY FIELD                                           │
│                                                                                                           │
│   NOUNS = Gravitational Wells                VERBS = Neural Pathways              ADJECTIVES = Modifiers │
│   (Entities with mass/salience)              (Connections between wells)          (Filter & boost)        │
│                                                                                                           │
│        ╭──────────────────────────────────────────────────────────────────────────╮                       │
│        │                                                                          │                       │
│        │      ★ ═══════════════════════════════════════════════════ ★             │                       │
│        │    drone                        detected                obstacle         │                       │
│        │   (mass:0.7)                 [high arousal]             (mass:0.8)       │                       │
│        │      │                      importance +0.3                 │            │                       │
│        │      │                            │                         │◀── "critical" boosts              │
│        │      │                            │                         │    salience by 1.5x               │
│        │      │           ╭────────────────┴────────────╮            │                                    │
│        │      │           │    hangar (mass: 0.5)       │            │                                    │
│        │      │           │           ★                 │            │                                    │
│        │      │           │    └── "near" relationship ─┘            │                                    │
│        │      ╰───────────────────────────────────────────────────────╯                                   │
│        │                                                                                                  │
│        ╰──────────────────────────────────────────────────────────────────────────────────────────────────╯
│                                                                                                           │
│   ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐ │
│   │  HEBBIAN DYNAMICS: "Neurons that fire together, wire together"                                      │ │
│   │                                                                                                     │ │
│   │  Co-activation count:  [1] ──▶ [2] ──▶ [3] ══▶ POTENTIATED (resists decay 10x)                     │ │
│   │                                                                                                     │ │
│   │  Decay formula:  w(t) = w₀ × e^(-λt)    where λ_potentiated = λ_normal / 10                        │ │
│   │                                                                                                     │ │
│   │  Pruning: Non-potentiated connections with strength < 0.1 are removed                               │ │
│   └─────────────────────────────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                                           │
└───────────────────────────────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                         MEMORY TIERING                                                    │
│                                                                                                           │
│   ┌───────────────────┐      ┌───────────────────┐      ┌───────────────────┐      ┌───────────────────┐ │
│   │  WORKING MEMORY   │ ───▶ │  SESSION MEMORY   │ ───▶ │  LONG-TERM STORE  │ ───▶ │     ARCHIVE       │ │
│   │   (immediate)     │      │  (current task)   │      │   (persistent)    │      │   (compressed)    │ │
│   │                   │      │                   │      │                   │      │                   │ │
│   │  High-salience    │      │  Frequently       │      │  Potentiated      │      │  Low-salience     │ │
│   │  entities stay    │      │  accessed stay    │      │  connections      │      │  gist only        │ │
│   │  hot              │      │  active           │      │  persist          │      │  (nouns kept)     │ │
│   └───────────────────┘      └───────────────────┘      └───────────────────┘      └───────────────────┘ │
│                                                                                                           │
│   Promotion criteria:                              Demotion criteria:                                     │
│   • High-arousal verbs ("crashed", "discovered")   • Low access count                                     │
│   • Proper nouns (names, places)                   • Structural verbs only ("is", "has")                  │
│   • Frequent co-activation (3+ times)              • No connections to other memories                     │
│   • User-marked importance                         • Time decay without reinforcement                     │
│                                                                                                           │
└───────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### Research Basis

| Element | Role | Reference |
|---------|------|-----------|
| **Nouns** | Entities with salience | [Construction Grammar](https://direct.mit.edu/coli/article/50/4/1375/123787) |
| **Verbs** | Relationships between entities | [Frame Semantics](https://sites.la.utexas.edu/hcb/files/2021/07/Boas-CxG-and-FS-2021-DRAFT.pdf) |
| **Adjectives** | Importance modifiers | [Emotional Tagging](https://pmc.ncbi.nlm.nih.gov/articles/PMC10410470/) |
| **Decay** | Salience-weighted forgetting | [Central vs Peripheral Memory](https://pmc.ncbi.nlm.nih.gov/articles/PMC4183265/) |

Memory architecture based on [CoALA framework](https://arxiv.org/abs/2309.02427) (Princeton, 2024).

## Installation

```bash
pip install shodh-memory
```

## Quick Start

```python
from shodh_memory import MemorySystem

# Initialize (models download automatically on first use)
memory = MemorySystem("./robot_memory")

# Store experiences
memory.record(
    content="Detected obstacle at coordinates X=5.2, Y=10.1",
    experience_type="observation",
    tags=["obstacle", "navigation"]
)

memory.record(
    content="Battery level critical at 15%",
    experience_type="sensor",
    tags=["battery", "warning"]
)

# Semantic search - finds relevant memories
results = memory.retrieve("obstacle near position 5", limit=5)
for mem in results:
    print(f"[{mem['relevance']:.2f}] {mem['content']}")

# Output:
# [0.89] Detected obstacle at coordinates X=5.2, Y=10.1
```

## Features

### Multi-tier Memory Architecture
```python
# Working memory (immediate context)
# Session memory (current mission)
# Long-term memory (persistent knowledge)

# Memories automatically tier based on access patterns
stats = memory.get_stats()
print(f"Working: {stats['working_memory_count']}")
print(f"Session: {stats['session_memory_count']}")
print(f"Long-term: {stats['long_term_memory_count']}")
```

### Mission Tracking
```python
# Track missions with waypoints and decisions
memory.start_mission("patrol_sector_7", mission_type="patrol")

memory.record_waypoint(
    name="checkpoint_alpha",
    position={"x": 10.0, "y": 20.0, "z": 0.0}
)

memory.record_decision(
    situation="Path blocked by debris",
    options=["go_around", "wait", "request_help"],
    chosen="go_around",
    reasoning="Alternative route available, time-critical mission"
)

memory.end_mission(
    success=True,
    summary="Patrol completed, 3 anomalies logged"
)
```

### Anomaly Detection
```python
# Record and search anomalies
memory.record_anomaly(
    description="Unexpected heat signature in sector 4",
    severity="high",
    sensor_data={"temperature": 85.2, "baseline": 22.0}
)

# Find similar past anomalies
anomalies = memory.find_anomalies(
    query="heat anomaly",
    severity="high",
    limit=10
)
```

### Spatial Memory
```python
from shodh_memory import GeoLocation, GeoFilter

# Store with GPS coordinates
memory.record(
    content="Landing zone clear",
    experience_type="observation",
    geo_location=GeoLocation(
        latitude=37.7749,
        longitude=-122.4194,
        altitude=10.0
    )
)

# Query by location
results = memory.retrieve(
    query="landing",
    geo_filter=GeoFilter(
        center_lat=37.77,
        center_lon=-122.42,
        radius_km=1.0
    )
)
```

## API Reference

### MemorySystem

| Method | Description |
|--------|-------------|
| `record(content, experience_type, ...)` | Store a memory |
| `retrieve(query, limit)` | Semantic search |
| `start_mission(name, mission_type)` | Begin mission tracking |
| `end_mission(success, summary)` | End mission |
| `record_waypoint(name, position)` | Log waypoint |
| `record_decision(situation, options, chosen, reasoning)` | Log decision |
| `record_anomaly(description, severity)` | Log anomaly |
| `record_sensor(sensor_type, values)` | Log sensor data |
| `find_anomalies(query, severity)` | Search anomalies |
| `find_similar_decisions(situation)` | Find past decisions |
| `get_stats()` | Memory statistics |
| `flush()` | Persist to disk |

### Experience Types

| Type | Use Case |
|------|----------|
| `observation` | Visual/sensor observations |
| `action` | Actions taken |
| `decision` | Decision points |
| `sensor` | Raw sensor data |
| `navigation` | Movement/waypoints |
| `communication` | Messages sent/received |
| `anomaly` | Unexpected events |

## REST API Server

For microservice architectures, run the HTTP server:

```bash
# From source
cargo build --release
./target/release/shodh-memory-server

# Environment variables
PORT=3030                           # Server port
STORAGE_PATH=./shodh_memory_data    # Data directory
RUST_LOG=info                       # Log level
```

```bash
# Store memory (simple)
curl -X POST http://localhost:3030/api/remember \
  -H "Content-Type: application/json" \
  -d '{"user_id": "robot-001", "content": "Obstacle detected at entrance"}'

# Search (simple)
curl -X POST http://localhost:3030/api/recall \
  -H "Content-Type: application/json" \
  -d '{"user_id": "robot-001", "query": "obstacle", "limit": 5}'

# Store with full metadata
curl -X POST http://localhost:3030/api/record \
  -H "Content-Type: application/json" \
  -d '{"user_id": "robot-001", "content": "Obstacle detected", "experience_type": "observation"}'

# Search with filters
curl -X POST http://localhost:3030/api/retrieve \
  -H "Content-Type: application/json" \
  -d '{"user_id": "robot-001", "query": "obstacle", "max_results": 5}'
```

### API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/remember` | POST | Store memory (simple) |
| `/api/recall` | POST | Search memories (simple) |
| `/api/record` | POST | Store with full metadata |
| `/api/retrieve` | POST | Search with filters |
| `/api/batch_remember` | POST | Batch store |
| `/api/memory/{id}` | GET/PUT/DELETE | CRUD operations |
| `/api/users/{id}/stats` | GET | User statistics |
| `/api/graph/{user_id}/stats` | GET | Graph statistics |
| `/health` | GET | Health check |

## Performance

| Metric | Value |
|--------|-------|
| Embedding latency | ~5ms (MiniLM-L6-v2) |
| Search latency | <1ms (10K memories) |
| Memory footprint | ~50MB |
| Disk per 1K memories | ~2MB |

## Platform Support

| Platform | Status |
|----------|--------|
| Linux x86_64 | Supported |
| macOS ARM64 (Apple Silicon) | Supported |
| Windows x86_64 | Supported |
| Linux ARM64 (Jetson, Pi) | Coming soon |

## Comparison

| Feature | Shodh-Memory | ChromaDB | Mem0 |
|---------|-------------|----------|------|
| Offline-first | Yes | Partial | No |
| Edge-optimized | Yes | No | No |
| Mission tracking | Yes | No | No |
| Spatial queries | Yes | No | No |
| Memory footprint | 50MB | 200MB+ | Cloud |
| Language | Rust+Python | Python | Python |

## Development

```bash
# Build from source
git clone https://github.com/varun29ankuS/shodh-memory
cd shodh-memory
cargo build --release

# Run tests
cargo test

# Build Python wheel
pip install maturin
maturin build --release
```

## Roadmap

### Near-term
- [ ] ARM64 Linux builds (Jetson Nano, Raspberry Pi 4/5)
- [ ] Temporal queries ("what happened yesterday", "last mission")
- [ ] Memory consolidation for long-term storage compression
- [ ] TypeScript/JavaScript client SDK

### Future
- [ ] ROS2 integration package
- [ ] Fleet memory sync with sharding (multi-robot coordination)
- [ ] Alternative embedding models (BGE-small, E5)

## License

Apache 2.0

## Links

- [Website](https://www.shodh-rag.com/memory)
- [PyPI Package](https://pypi.org/project/shodh-memory/)
- [GitHub](https://github.com/varun29ankuS/shodh-memory)
- [Issues](https://github.com/varun29ankuS/shodh-memory/issues)
- Email: 29.varuns@gmail.com
