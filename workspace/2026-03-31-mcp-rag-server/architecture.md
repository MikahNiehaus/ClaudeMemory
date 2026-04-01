# Architecture: MCP RAG Server for ClaudeMemory

## Status: COMPLETE

## Context

- **Problem**: Knowledge retrieval in the ClaudeMemory orchestrator is manual and explicit. The orchestrator hard-codes which `knowledge/*.md` and `agents/*.md` files each agent reads. There is no cross-cutting semantic search, no historical learning from past workspaces, and no codebase-awareness. This limits agent effectiveness to whatever the orchestrator author anticipated.
- **Constraints**: Local-only (no cloud dependencies), Windows 11, must integrate with Claude Code's MCP client, small corpus (~10K lines today but will grow), must start fast and not block the agent workflow.
- **Quality Attributes**: Latency (sub-second search), simplicity (easy to maintain), correctness (relevant results), operability (zero-config startup).

## Current State

- **Structure**: 36 knowledge bases (~6,800 lines), 21 agent definitions (~3,300 lines), 1 workspace context (growing).
- **Issues**: Hard-coded file routing means agents miss cross-cutting knowledge. No way to search historical task contexts. No codebase indexing.
- **Technical Debt**: None (greenfield).

---

## Alternatives Analysis

### Alternative 1: Python + ChromaDB + sentence-transformers + MCP Python SDK

| Aspect | Detail |
|--------|--------|
| Language | Python 3.11+ |
| Vector DB | ChromaDB (embedded, SQLite-backed) |
| Embeddings | sentence-transformers `all-MiniLM-L6-v2` (local, 384-dim) |
| MCP SDK | `mcp` Python SDK (official Anthropic package) |
| Pros | Mature ML ecosystem, ChromaDB is battle-tested for local use, sentence-transformers has huge model selection, Python SDK is first-class for MCP |
| Cons | Python startup time (~2-3s with model loading), heavier dependency chain (PyTorch), ~500MB disk for model + torch |

### Alternative 2: TypeScript + LanceDB + transformers.js + MCP TypeScript SDK

| Aspect | Detail |
|--------|--------|
| Language | TypeScript (Node.js) |
| Vector DB | LanceDB (embedded, Arrow-based) |
| Embeddings | @xenova/transformers (ONNX runtime, same models as sentence-transformers) |
| MCP SDK | `@modelcontextprotocol/sdk` (official TypeScript SDK) |
| Pros | Faster startup (ONNX vs PyTorch), lighter install (~150MB), TypeScript SDK is the reference MCP implementation, LanceDB is columnar and fast |
| Cons | transformers.js model selection is narrower, LanceDB is newer (less community examples), Node.js memory management less predictable for large corpora |

### Alternative 3: Python + SQLite FTS5 (no vector DB) + keyword search

| Aspect | Detail |
|--------|--------|
| Language | Python |
| Search | SQLite FTS5 full-text search with BM25 ranking |
| Embeddings | None (pure keyword/BM25) |
| MCP SDK | `mcp` Python SDK |
| Pros | Zero ML dependencies, instant startup, tiny footprint, dead simple |
| Cons | No semantic understanding (searching "error handling" won't find "exception management"), BM25 degrades on short/jargon-heavy queries, loses the "R" in RAG |

### Alternative 4: Python + ChromaDB + Ollama embeddings

| Aspect | Detail |
|--------|--------|
| Language | Python |
| Vector DB | ChromaDB |
| Embeddings | Ollama `nomic-embed-text` (requires Ollama server running) |
| MCP SDK | `mcp` Python SDK |
| Pros | High-quality embeddings, shared infrastructure if Ollama already in use |
| Cons | External dependency (Ollama must be running), added latency per embedding call, extra install burden |

### Decision: Alternative 1 -- Python + ChromaDB + sentence-transformers + MCP Python SDK

**Rationale**:
1. **Python is the natural home for ML tooling.** ChromaDB, sentence-transformers, and the MCP SDK all have Python as their primary target. Fighting against this grain (Alternative 2) adds friction for marginal startup gains.
2. **ChromaDB is the best fit for this scale.** The corpus is small (~10K lines now, maybe 100K eventually). ChromaDB's embedded mode with SQLite persistence is purpose-built for exactly this: local, single-process, no server needed.
3. **Semantic search is the whole point.** Alternative 3 (BM25 only) defeats the purpose. The knowledge bases use varied terminology -- agents need to find "error recovery" when searching for "failure handling."
4. **Self-contained beats external dependencies.** Alternative 4 requires Ollama running, which is an operational burden. sentence-transformers bundles everything.
5. **Startup cost is acceptable.** The ~2-3s cold start happens once per Claude Code session. Warm queries are <100ms. The model can be loaded lazily.

**Trade-offs accepted**:
- ~500MB disk for PyTorch + model (acceptable for a dev tool)
- Cold start latency (mitigated by lazy loading and background indexing)
- Python dependency management (mitigated by uv/pip with locked requirements)

---

## System Architecture

### High-Level Diagram

```
+------------------------------------------------------------------+
|                       Claude Code (MCP Client)                    |
|                                                                   |
|   Agent calls:  rag_search("error handling for API calls",       |
|                            scope="knowledge")                     |
+------------------------------|------------------------------------+
                               | MCP Protocol (stdio)
                               v
+------------------------------------------------------------------+
|                     MCP RAG Server (Python)                       |
|                                                                   |
|  +--------------------+   +-------------------+                   |
|  |   MCP Transport    |   |   Tool Registry   |                   |
|  |   (stdio server)   |-->|   rag_search      |                   |
|  |                    |   |   rag_index        |                   |
|  +--------------------+   |   rag_status       |                   |
|                           |   rag_context      |                   |
|                           +---------|----------+                   |
|                                     v                              |
|  +--------------------+   +-------------------+                    |
|  |  Indexing Engine    |   |  Search Engine    |                   |
|  |                    |   |                    |                   |
|  |  - Chunker         |   |  - Query Embedder |                   |
|  |  - Metadata Tagger |   |  - Scope Filter   |                   |
|  |  - File Watcher    |   |  - Re-ranker      |                   |
|  +---------|----------+   +---------|----------+                   |
|            |                        |                              |
|            v                        v                              |
|  +---------------------------------------------------+            |
|  |              Embedding Service                     |            |
|  |  sentence-transformers (all-MiniLM-L6-v2)         |            |
|  |  Lazy-loaded, singleton                            |            |
|  +---------------------------------------------------+            |
|            |                        |                              |
|            v                        v                              |
|  +---------------------------------------------------+            |
|  |              ChromaDB (Embedded Mode)              |            |
|  |  SQLite persistence at .rag-index/                 |            |
|  |                                                    |            |
|  |  Collections:                                      |            |
|  |    - knowledge    (knowledge/*.md chunks)          |            |
|  |    - agents       (agents/*.md chunks)             |            |
|  |    - workspaces   (workspace/*/context.md chunks)  |            |
|  |    - codebase     (target project files)           |            |
|  +---------------------------------------------------+            |
+------------------------------------------------------------------+
                               |
                               v
              +--------------------------------+
              |  File System (watched paths)   |
              |                                |
              |  crew/mikah/knowledge/*.md      |
              |  crew/mikah/agents/*.md         |
              |  crew/mikah/workspace/*/        |
              |  <target-project>/ (optional)   |
              +--------------------------------+
```

### Component Responsibilities

| Component | Responsibility | Depends On |
|-----------|---------------|------------|
| MCP Transport | Handles stdio MCP protocol, request/response lifecycle | `mcp` SDK |
| Tool Registry | Defines and dispatches MCP tool calls | MCP Transport |
| Search Engine | Executes semantic queries with scope filtering and re-ranking | Embedding Service, ChromaDB |
| Indexing Engine | Chunks documents, extracts metadata, triggers embeddings | Embedding Service, ChromaDB, File Watcher |
| Embedding Service | Loads model, generates embeddings for text chunks | sentence-transformers |
| ChromaDB Store | Persists vectors and metadata, handles similarity search | ChromaDB library |
| File Watcher | Detects file changes, triggers re-indexing | watchdog library |
| Chunker | Splits documents into semantically meaningful chunks | None (pure logic) |
| Metadata Tagger | Extracts scope, source file, section headers from chunks | None (pure logic) |

---

## MCP Tool Definitions

### Tool 1: `rag_search`

**Purpose**: Semantic search across indexed content.

```json
{
  "name": "rag_search",
  "description": "Search the ClaudeMemory knowledge base, agent definitions, workspace history, and optionally a target codebase using semantic similarity. Returns the most relevant text chunks with source attribution. Use this to find relevant knowledge, patterns, past decisions, or code examples.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "Natural language search query. Be specific. Example: 'error handling patterns for external API calls'"
      },
      "scope": {
        "type": "string",
        "enum": ["all", "knowledge", "agents", "workspaces", "codebase"],
        "description": "Which collection to search. 'all' searches everything. 'knowledge' searches knowledge bases only. 'agents' searches agent definitions. 'workspaces' searches historical task contexts. 'codebase' searches the target project.",
        "default": "all"
      },
      "limit": {
        "type": "integer",
        "description": "Maximum number of results to return (1-20).",
        "default": 5,
        "minimum": 1,
        "maximum": 20
      },
      "min_score": {
        "type": "number",
        "description": "Minimum similarity score threshold (0.0-1.0). Results below this are excluded. Default 0.3 filters out weak matches.",
        "default": 0.3,
        "minimum": 0.0,
        "maximum": 1.0
      }
    },
    "required": ["query"]
  }
}
```

**Return type**:
```json
{
  "results": [
    {
      "content": "The matching text chunk (up to ~500 tokens)",
      "source": "knowledge/error-handling.md",
      "section": "Error Categories",
      "scope": "knowledge",
      "score": 0.87,
      "line_start": 45
    }
  ],
  "total_found": 12,
  "query_time_ms": 42
}
```

### Tool 2: `rag_index`

**Purpose**: Trigger indexing of a path (or re-index all).

```json
{
  "name": "rag_index",
  "description": "Index or re-index files for RAG search. Use 'path' to index a specific directory (e.g., a target project codebase). Use without arguments to re-index all default collections (knowledge, agents, workspaces). Indexing runs in the background; use rag_status to check progress.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "path": {
        "type": "string",
        "description": "Absolute path to a directory to index as the 'codebase' scope. Omit to re-index default collections."
      },
      "scope": {
        "type": "string",
        "enum": ["knowledge", "agents", "workspaces", "codebase", "all"],
        "description": "Which collection to re-index. Only needed when re-indexing defaults. When 'path' is provided, scope is automatically 'codebase'.",
        "default": "all"
      },
      "force": {
        "type": "boolean",
        "description": "Force full re-index even if files haven't changed. Default false uses incremental indexing.",
        "default": false
      }
    }
  }
}
```

**Return type**:
```json
{
  "status": "indexing",
  "scope": "codebase",
  "path": "C:/Users/grayw/projects/my-app",
  "files_queued": 142,
  "message": "Indexing started. Use rag_status() to check progress."
}
```

### Tool 3: `rag_status`

**Purpose**: Check server health and indexing status.

```json
{
  "name": "rag_status",
  "description": "Check the RAG server status including index health, collection sizes, and any in-progress indexing operations.",
  "inputSchema": {
    "type": "object",
    "properties": {}
  }
}
```

**Return type**:
```json
{
  "status": "ready",
  "collections": {
    "knowledge": { "chunks": 245, "files": 36, "last_indexed": "2026-03-31T14:22:00Z" },
    "agents": { "chunks": 89, "files": 21, "last_indexed": "2026-03-31T14:22:00Z" },
    "workspaces": { "chunks": 12, "files": 1, "last_indexed": "2026-03-31T14:22:00Z" },
    "codebase": { "chunks": 0, "files": 0, "last_indexed": null }
  },
  "indexing_in_progress": false,
  "model": "all-MiniLM-L6-v2",
  "embedding_dimensions": 384
}
```

### Tool 4: `rag_context`

**Purpose**: Build a focused context package for an agent given a task description.

```json
{
  "name": "rag_context",
  "description": "Build a curated context package for an agent. Given an agent name and task description, returns the most relevant knowledge chunks, agent definitions, and past workspace decisions. Use this when spawning an agent to give it optimal context without manual file selection.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "agent": {
        "type": "string",
        "description": "Agent name (e.g., 'debug-agent', 'test-agent'). The agent's own definition is always included."
      },
      "task_description": {
        "type": "string",
        "description": "Natural language description of what the agent will work on."
      },
      "max_tokens": {
        "type": "integer",
        "description": "Approximate token budget for the context package. Chunks are selected to fit within this budget.",
        "default": 4000,
        "minimum": 500,
        "maximum": 16000
      }
    },
    "required": ["agent", "task_description"]
  }
}
```

**Return type**:
```json
{
  "agent_definition": "Full content of agents/debug-agent.md",
  "relevant_knowledge": [
    { "source": "knowledge/debugging.md", "section": "Systematic Debugging", "content": "..." },
    { "source": "knowledge/error-handling.md", "section": "Error Categories", "content": "..." }
  ],
  "relevant_history": [
    { "source": "workspace/2026-03-15-api-fix/context.md", "section": "Resolution", "content": "..." }
  ],
  "total_tokens_approx": 3800,
  "sources_used": 5
}
```

---

## Indexing Strategy

### Chunking

The corpus has two distinct document types requiring different chunking strategies:

**Markdown documents (knowledge bases, agent definitions, workspace contexts)**:
- **Primary strategy**: Section-based chunking. Split on `##` and `###` headers.
- **Chunk size target**: 200-500 tokens per chunk. If a section exceeds 500 tokens, split at paragraph boundaries.
- **Overlap**: 50 tokens of overlap between consecutive chunks within the same section to preserve context across boundaries.
- **Minimum chunk size**: 50 tokens. Merge very small sections with the next section.

**Code files (target codebase, if indexed)**:
- **Primary strategy**: Function/class-level chunking using tree-sitter or regex-based splitting.
- **Fallback**: Fixed-size chunking at 300 tokens with 50-token overlap for languages without parser support.
- **File-level metadata**: Always include the file path and language as metadata.

### Metadata Schema

Every chunk stored in ChromaDB carries this metadata:

```python
{
    "source_file": "knowledge/error-handling.md",  # Relative path from project root
    "scope": "knowledge",                            # Collection name
    "section": "Error Categories",                   # Nearest heading
    "line_start": 45,                                # Line number in source
    "line_end": 72,                                  # End line number
    "file_hash": "a1b2c3d4",                         # SHA-256 prefix for change detection
    "indexed_at": "2026-03-31T14:22:00Z",            # When this chunk was indexed
    "chunk_index": 3,                                # Position within the file's chunks
    "token_count": 287                               # Approximate token count
}
```

### Scoped Collections

ChromaDB collections map directly to scopes:

| Collection | Source Pattern | Re-index Trigger |
|------------|---------------|------------------|
| `knowledge` | `knowledge/*.md` | File watcher (mtime change) |
| `agents` | `agents/*.md` | File watcher (mtime change) |
| `workspaces` | `workspace/*/context.md`, `workspace/*/ticket.md` | File watcher + new workspace creation |
| `codebase` | User-specified path | Explicit `rag_index(path=...)` call |

### Incremental Indexing

To avoid unnecessary re-embedding:

1. On startup, compute SHA-256 hash of each source file.
2. Compare against stored `file_hash` in metadata.
3. Only re-chunk and re-embed files whose hash has changed.
4. Delete orphaned chunks (file was deleted or renamed).
5. Store a manifest file (`.rag-index/manifest.json`) mapping file paths to hashes for fast comparison.

### File Watcher

- Use the `watchdog` library (cross-platform, works on Windows 11).
- Watch `knowledge/`, `agents/`, `workspace/` directories.
- Debounce changes by 2 seconds (batch rapid saves).
- Re-index only changed files (incremental).
- Do NOT watch the codebase path by default (too noisy). Codebase re-indexing is explicit only.

---

## File Structure

```
crew/mikah/
  mcp-rag-server/
    pyproject.toml              # Project config, dependencies (uv/pip compatible)
    README.md                   # Setup and usage instructions
    
    src/
      rag_server/
        __init__.py
        server.py               # MCP server entry point (stdio transport)
        config.py               # Configuration (paths, model name, collection names)
        
        tools/
          __init__.py
          search.py             # rag_search tool implementation
          index.py              # rag_index tool implementation
          status.py             # rag_status tool implementation
          context.py            # rag_context tool implementation
        
        core/
          __init__.py
          embedding.py          # Embedding service (lazy-loaded singleton)
          store.py              # ChromaDB wrapper (collection management)
          chunker.py            # Document chunking strategies
          metadata.py           # Metadata extraction and tagging
          watcher.py            # File watcher (watchdog-based)
        
        indexing/
          __init__.py
          engine.py             # Indexing orchestration (incremental, manifest)
          markdown_chunker.py   # Markdown-specific chunking (section-based)
          code_chunker.py       # Code file chunking (function-level)
        
        ports/
          __init__.py
          embedding_port.py     # ABC: embedding generation
          store_port.py         # ABC: vector store operations
          watcher_port.py       # ABC: file system watching
    
    tests/
      __init__.py
      test_chunker.py           # Unit tests for chunking logic
      test_search.py            # Integration tests for search
      test_indexing.py          # Integration tests for indexing
      test_tools.py             # MCP tool contract tests
    
    .rag-index/                 # Persistent data (gitignored)
      chroma/                   # ChromaDB SQLite data
      manifest.json             # File hash manifest for incremental indexing
```

### Dependency List (pyproject.toml)

```
[project]
requires-python = ">=3.11"
dependencies = [
    "mcp>=1.0",                         # MCP Python SDK
    "chromadb>=0.5",                     # Embedded vector database
    "sentence-transformers>=3.0",        # Local embedding models
    "watchdog>=4.0",                     # Cross-platform file watcher
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-asyncio>=0.24",
]
```

---

## Integration with ClaudeMemory Orchestrator

### Claude Code Configuration

Add the MCP server to Claude Code's config (`~/.claude/mcp.json` or project-level):

```json
{
  "mcpServers": {
    "rag": {
      "command": "uv",
      "args": ["run", "--directory", "C:/Users/grayw/gt/claudememory/crew/mikah/mcp-rag-server", "python", "-m", "rag_server.server"],
      "env": {
        "RAG_PROJECT_ROOT": "C:/Users/grayw/gt/claudememory/crew/mikah"
      }
    }
  }
}
```

### Orchestrator Integration Points

1. **Agent spawning**: The orchestrator currently hard-codes `READ agents/[name]-agent.md` and `READ knowledge/[topic].md`. With `rag_context`, it can instead call:
   ```
   rag_context(agent="debug-agent", task_description="Fix null pointer in OrderService.calculateTotal")
   ```
   This returns the agent definition plus semantically relevant knowledge, replacing manual file selection.

2. **Planning phase**: During planning, the orchestrator can call:
   ```
   rag_search("similar past tasks involving API error handling", scope="workspaces")
   ```
   To find historical decisions and avoid repeating mistakes.

3. **Cross-cutting discovery**: An agent working on security can discover relevant content in `knowledge/error-handling.md` or `knowledge/api-design.md` that wouldn't have been hard-coded in its routing.

4. **Codebase awareness**: Before spawning an agent to work on a target project, index it:
   ```
   rag_index(path="C:/Users/grayw/projects/target-app")
   ```
   Then agents can search the codebase semantically during their work.

### Migration Path

This is additive, not disruptive. The existing hard-coded file routing continues to work. The orchestrator can adopt RAG tools incrementally:

- **Phase 1**: Deploy server, auto-index on startup. Agents can optionally use `rag_search`.
- **Phase 2**: Replace hard-coded knowledge file lists in `_orchestrator.md` with `rag_context` calls.
- **Phase 3**: Index target project codebases. Update agent definitions to use `rag_search(scope="codebase")`.

---

## SOLID Design Review

### SRP (Single Responsibility)

Each component has exactly one reason to change:

| Component | Single Responsibility | Change Trigger |
|-----------|----------------------|----------------|
| `server.py` | MCP protocol lifecycle | MCP SDK changes |
| `search.py` | Query execution and formatting | Search behavior changes |
| `index.py` | Index orchestration tool | Indexing workflow changes |
| `embedding.py` | Model loading and inference | Model swap |
| `store.py` | ChromaDB operations | Storage engine swap |
| `markdown_chunker.py` | Markdown splitting logic | Chunking strategy changes |
| `code_chunker.py` | Code splitting logic | Language support changes |
| `watcher.py` | File change detection | Watch strategy changes |

**PASS**: No component has more than one reason to change.

### OCP (Open/Closed)

- **Chunking strategies**: New file types (e.g., YAML, JSON) add a new chunker class implementing the chunker port. No existing code modified.
- **Scopes/collections**: New scopes add a configuration entry and optional new chunker. Core search logic is scope-agnostic.
- **Embedding models**: Swap models by changing config. The `EmbeddingPort` abstraction isolates the rest of the system.

**PASS**: Extension points exist at every likely change vector.

### LSP (Liskov Substitution)

- `MarkdownChunker` and `CodeChunker` both implement the same `Chunker` protocol. Either can be used wherever a chunker is expected.
- `ChromaStore` implements `StorePort`. A future LanceDB adapter would be a drop-in replacement.

**PASS**: All abstractions are substitutable.

### ISP (Interface Segregation)

- `EmbeddingPort`: only `embed(texts) -> vectors` and `embed_query(text) -> vector`. No bloated interface.
- `StorePort`: `add_chunks()`, `search()`, `delete()`, `count()`. Each tool uses only the methods it needs.
- `WatcherPort`: `start()`, `stop()`, `on_change(callback)`. Minimal surface.

**PASS**: Interfaces are narrow and focused.

### DIP (Dependency Inversion)

- Tools depend on `StorePort` and `EmbeddingPort` abstractions, not on ChromaDB or sentence-transformers directly.
- The `server.py` entry point wires concrete implementations via constructor injection.
- Tests can substitute mock implementations for all ports.

**PASS**: High-level modules depend on abstractions.

---

## Self-Critique

### Assumptions

1. **Corpus stays small-to-medium.** The design assumes <100K chunks total. If the codebase scope indexes massive monorepos (>10K files), ChromaDB's embedded mode may need tuning or replacement with a client-server mode.

2. **Single user, single process.** No concurrent access to the ChromaDB store. If multiple Claude Code sessions share the same index, SQLite locking could cause issues. Mitigation: each session gets its own server process, and ChromaDB handles file locking at the SQLite level.

3. **all-MiniLM-L6-v2 is sufficient.** This is a small, fast model (384 dimensions). It handles English technical text well but may underperform on highly specialized jargon or non-English content. The embedding port abstraction allows swapping to a larger model (e.g., `all-mpnet-base-v2`, 768-dim) if quality is insufficient.

4. **Markdown structure is consistent.** The chunker assumes knowledge bases use `##`/`###` headers. If files use inconsistent heading levels or no headers, chunking quality degrades.

5. **Windows file paths.** The `watchdog` library handles Windows paths, but path normalization (forward vs. backslash) must be consistent throughout. The config module should normalize all paths at entry.

### Edge Cases

1. **Empty or very short files.** A knowledge base with only 2 lines produces a single tiny chunk with weak embedding signal. Mitigation: minimum chunk merging.

2. **Binary files in codebase indexing.** If `rag_index(path=...)` encounters images, compiled files, or node_modules. Mitigation: exclude patterns (`.gitignore`-aware, plus hardcoded exclusions for `node_modules/`, `.git/`, `__pycache__/`, binary extensions).

3. **Concurrent indexing and search.** If a search happens while indexing is in progress, results may be partial. Mitigation: ChromaDB handles this at the collection level; partial results are acceptable (better than blocking).

4. **Model download on first run.** sentence-transformers will download the model (~90MB) on first use. This requires internet on first run. Mitigation: document this in README; optionally bundle the model or support pre-download.

5. **Large workspace history.** Over months, hundreds of workspace contexts accumulate. The workspaces collection grows unbounded. Mitigation: configurable retention (e.g., only index workspaces from last 90 days).

### Trade-offs

| Trade-off | Chose | Over | Rationale |
|-----------|-------|------|-----------|
| Semantic search | Vector embeddings | BM25 keyword search | The whole value prop is semantic discovery. BM25 misses synonyms and conceptual matches. |
| Local embeddings | sentence-transformers | Cloud API (OpenAI, etc.) | No cloud dependency, no API key, no cost, no latency. Quality is sufficient for this corpus size. |
| Embedded DB | ChromaDB in-process | Client-server (Weaviate, Qdrant) | Single-process simplicity. No server to manage. Appropriate for the scale. |
| Python | Python | TypeScript | Better ML ecosystem. The MCP server is a background process; language choice doesn't affect the Claude Code experience. |
| Lazy model loading | Deferred to first query | Eager load on startup | Faster server startup. First query pays ~2s cost, but subsequent queries are <100ms. |
| Section-based chunking | Semantic sections | Fixed-size windows | Preserves meaning boundaries. A chunk about "error handling" stays coherent rather than splitting mid-thought. |
| stdio transport | stdio | HTTP/SSE | Claude Code's MCP client uses stdio for local servers. Simpler, no port management. |

---

## Teaching

### Why This Approach

**The core insight is that this is a Retrieval-Augmented Generation problem at a small, local scale.** The same pattern used by ChatGPT plugins and enterprise RAG systems applies here, but dramatically simplified because:
- The corpus is small (thousands of lines, not millions of documents)
- The user is local (no multi-tenancy, no auth)
- The queries come from AI agents (high-quality, specific queries)
- Latency tolerance is generous (sub-second is fine)

This means we can use the simplest possible implementation of each component and still get excellent results.

### Patterns Applied

1. **Ports and Adapters (Hexagonal Architecture)**: The `ports/` directory defines abstract interfaces for external concerns (embedding model, vector store, file system). Concrete implementations are injected at startup. This is the key architectural decision -- it makes every external dependency swappable without touching business logic.

2. **Strategy Pattern**: Chunking strategies (`MarkdownChunker`, `CodeChunker`) are interchangeable implementations of a common interface. The indexing engine selects the strategy based on file type. New file types (YAML, JSON) add strategies without modifying existing code.

3. **Facade Pattern**: Each MCP tool (`rag_search`, `rag_context`, etc.) is a facade over the underlying search engine, indexing engine, and store. The tools present a simple, agent-friendly interface that hides the complexity of embedding, similarity computation, and metadata filtering.

4. **Singleton Pattern (via lazy loading)**: The embedding model is expensive to load (~2s, ~200MB RAM). It's loaded once on first use and shared across all subsequent operations. This is implemented as a lazy property, not a classic singleton, to maintain testability.

5. **Observer Pattern**: The file watcher uses the observer pattern (via `watchdog`) to trigger re-indexing when source files change. This decouples file system events from indexing logic.

### Why NOT a Simpler Approach

One might ask: "Why not just `grep` the knowledge bases?" The answer is that grep finds exact text matches, but agents need conceptual matches. When a debug-agent is investigating a "timeout in the payment service," it needs to find knowledge about:
- Error handling patterns (keyword: "timeout" might not appear)
- API design retry strategies (conceptually related, different words)
- Past workspace where a similar issue was resolved (historical learning)

Semantic embeddings capture these conceptual relationships. That's the entire value proposition.

### Why NOT More Complex

One might also ask: "Why not use a graph database, or hybrid search (BM25 + vector), or re-ranking with a cross-encoder?" Because YAGNI. The corpus is small, the queries are high-quality (from AI agents, not casual users), and the embedding model is good enough for this scale. Every added component increases operational complexity. Start simple, measure, add complexity only when the simple approach demonstrably fails.

---

## Implementation Guidance

- **Start with**: `embedding.py` and `store.py` (core ports + ChromaDB implementation). These are the foundation everything else builds on. Verify they work in isolation with a simple script.
- **Then**: `markdown_chunker.py` + `engine.py`. Get the knowledge bases indexed and searchable from a Python REPL before touching MCP.
- **Then**: `server.py` + `search.py`. Wire up the MCP server with just `rag_search`. Test with Claude Code.
- **Then**: `rag_index`, `rag_status`, `rag_context` tools.
- **Last**: `watcher.py` (file watching is a nice-to-have; manual re-indexing works fine initially).
- **Critical path**: The embedding model download and ChromaDB persistence. Test on Windows early -- path handling and file locking are the most likely sources of platform-specific bugs.
- **Risk areas**: `watchdog` on Windows (generally works but can miss events on network drives), ChromaDB SQLite locking under concurrent access, sentence-transformers model compatibility with newer Python versions.

## Handoff Notes

This architecture is ready for implementation by a workflow-agent or direct coding agent. Key decisions are made; the file structure, dependencies, tool contracts, and integration points are all specified. The implementation agent should:

1. Read this document fully before starting.
2. Set up the project skeleton (`pyproject.toml`, directory structure).
3. Follow the implementation order in "Implementation Guidance" above.
4. Write tests alongside each component (especially chunker tests -- these are pure logic and easy to unit test).
5. Test on Windows 11 throughout, not just at the end.
