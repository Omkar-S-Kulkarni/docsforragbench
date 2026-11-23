# RAGBench Architecture

For detailed architecture information, see the [Comprehensive Guide - Part 1](../guides/COMPREHENSIVE_GUIDE.md#2-architecture-overview).

## Quick Overview

RAGBench is built on a **7-layer architecture** where each layer is completely independent and communicates through well-defined data contracts:

```
Layer 1: Ingestion (82 connectors) → Document
Layer 2: Chunking (106 methods) → Chunk  
Layer 3: Embedding (62 methods) → EmbeddedChunk
Layer 4: Vector Stores (41 stores) → Storage
Layer 5: Retrieval (28 methods) → RetrievalResult
Layer 6: Reranking (21 methods) → Reranked Results
Layer 7: Generation (9 providers) → Answer
```

## Core Principles

### 1. Layer Independence
Each layer can be used standalone without requiring other layers. No circular dependencies exist.

### 2. Data Contracts
Layers communicate through immutable data classes:
- **Document**: Raw content from ingestion
- **Chunk**: Split pieces with metadata
- **EmbeddedChunk**: Chunks with vector embeddings
- **RetrievalResult**: Retrieved chunks with scores
- **GeneratedAnswer**: Final answer with sources

### 3. Registry Pattern
Every layer uses a registry for method discovery:
```python
from ragbench.embedding import REGISTRY

# List all methods
REGISTRY.list_names()

# Get method info
REGISTRY.get_info("openai")

# Check availability
REGISTRY.is_available("bge")
```

### 4. Smart Auto-Routing
When method is not specified, RAGBench automatically selects the best one based on:
- Content type (text, code, image)
- Query characteristics  
- Available resources (API keys, models)
- Store capabilities

## Directory Structure

```
ragbench/
├── ingestion/        # Layer 1: 82 connectors
├── chunking/         # Layer 2: 106 methods
├── embedding/        # Layer 3: 62 embedders
├── vectorstore/      # Layer 4: 41 stores
├── retrieval/        # Layer 5: 28 retrievers
├── reranking/        # Layer 6: 21 rerankers
├── generation/       # Layer 7: 9 LLM providers
├── pipeline/         # High-level orchestration
└── core/             # Shared utilities
```

## Learn More

- [Complete Architecture Details](../guides/COMPREHENSIVE_GUIDE.md#2-architecture-overview)
- [Layer-by-Layer Deep Dive](../guides/COMPREHENSIVE_GUIDE.md#5-layer-1-document-ingestion)
- [Pipeline & Auto Mode](../guides/COMPREHENSIVE_GUIDE.md#12-pipeline-auto-mode)
