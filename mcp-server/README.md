<p align="center">
  <img src="https://raw.githubusercontent.com/varun29ankuS/shodh-memory/main/assets/logo.png" width="120" alt="Shodh-Memory">
</p>

<h1 align="center">Shodh-Memory MCP Server</h1>

<p align="center">Persistent AI memory with semantic search. Store observations, decisions, learnings, and recall them across sessions.</p>

## Features

- **1-Click Install**: Auto-downloads and starts the backend server
- **Semantic Search**: Find memories by meaning, not just keywords
- **Memory Types**: Categorize as Observation, Decision, Learning, Error, Pattern, etc.
- **Persistent**: Memories survive across sessions and restarts
- **Fast**: Sub-millisecond retrieval with vector indexing
- **Offline**: All models bundled, no internet required after install

## Installation

Add to your MCP client config with your API key:

**Claude Desktop / Claude Code** (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "shodh-memory": {
      "command": "npx",
      "args": ["-y", "@shodh/memory-mcp"],
      "env": {
        "SHODH_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

Config file locations:
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

**For Cursor/other MCP clients**: Similar configuration with the npx command.

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `SHODH_API_KEY` | **Required**. API key for authentication | - |
| `SHODH_API_URL` | Backend server URL | `http://127.0.0.1:3030` |
| `SHODH_USER_ID` | User ID for memory isolation | `claude-code` |
| `SHODH_NO_AUTO_SPAWN` | Set to `true` to disable auto-starting the backend | `false` |
| `SHODH_STREAM` | Enable/disable streaming ingestion | `true` |
| `SHODH_PROACTIVE` | Enable/disable proactive memory surfacing | `true` |

## Tools

| Tool | Description |
|------|-------------|
| `remember` | Store a memory with optional type and tags |
| `recall` | Semantic search to find relevant memories |
| `proactive_context` | Auto-surface relevant memories for current context |
| `context_summary` | Get categorized context for session bootstrap |
| `list_memories` | List all stored memories |
| `forget` | Delete a specific memory by ID |
| `memory_stats` | Get statistics about stored memories |
| `recall_by_tags` | Find memories by tag |
| `recall_by_date` | Find memories within a date range |

## How It Works

1. **Install**: `npx -y @shodh/memory-mcp` downloads the package
2. **Auto-spawn**: On first run, downloads the native server binary for your platform
3. **Connect**: MCP client connects to the server via stdio
4. **Ready**: Start using `remember` and `recall` tools

The backend server runs locally and stores all data on your machine. No cloud dependency.

## Usage Examples

```
"Remember that the user prefers Rust over Python for systems programming"
"Recall what I know about user's programming preferences"
"What context do you have about this project?"
"List my recent memories"
```

## License

Apache-2.0
