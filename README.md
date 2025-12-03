# Shodh-Memory

A local memory system for AI agents, designed to run offline on edge devices.

## What it does

Shodh-Memory stores and retrieves text memories using semantic search. It runs entirely on-device without requiring cloud services.

**Target use cases:**
- Robots and drones that need to remember observations
- Edge devices without reliable internet
- Applications requiring data privacy

## Features

- **Local storage** - RocksDB-based persistence
- **Semantic search** - MiniLM-L6 embeddings (~34MB, auto-downloaded on first use)
- **REST API** - HTTP endpoints for CRUD operations
- **Python bindings** - PyO3-based SDK (in development)

## Installation

### Build from source (requires Rust 1.70+)

```bash
git clone https://github.com/varun29ankuS/shodh-memory
cd shodh-memory
cargo build --release
./target/release/shodh-memory-server
```

The server starts on port 3030 by default.

## Usage

### Record a memory

```bash
curl -X POST http://localhost:3030/api/record \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "robot-001",
    "content": "Detected obstacle at X=5, Y=10",
    "experience_type": "observation"
  }'
```

### Search memories

```bash
curl -X POST http://localhost:3030/api/retrieve \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "robot-001",
    "query": "obstacle",
    "max_results": 10
  }'
```

### Python (REST client)

```python
import requests

# Record
requests.post("http://localhost:3030/api/record", json={
    "user_id": "robot-001",
    "content": "Battery at 20%",
    "experience_type": "sensor"
})

# Search
response = requests.post("http://localhost:3030/api/retrieve", json={
    "user_id": "robot-001",
    "query": "battery status",
    "max_results": 5
})
print(response.json())
```

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/record` | Store a memory |
| POST | `/api/retrieve` | Search memories |
| GET | `/api/memory/:id` | Get specific memory |
| DELETE | `/api/memory/:id` | Delete memory |
| GET | `/health` | Health check |

## Configuration

Environment variables:
- `PORT` - Server port (default: 3030)
- `STORAGE_PATH` - Data directory (default: ./shodh_memory_data)
- `RUST_LOG` - Log level (error, warn, info, debug)

## Requirements

- Rust 1.70+ (for building)
- ~50MB disk space for models (downloaded on first use)
- ~100MB RAM minimum

## Status

This is early-stage software. It works for basic use cases but:
- Limited testing on actual edge hardware
- Python SDK is work in progress
- No production deployments yet

## License

Apache 2.0

## Contact

- GitHub Issues: https://github.com/varun29ankuS/shodh-memory/issues
- Email: 29.varuns@gmail.com
