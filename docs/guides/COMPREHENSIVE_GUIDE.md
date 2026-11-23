# RAGBench: The Complete Comprehensive Guide

**Version:** 0.4.0  
**Last Updated:** 2024  
**Document Length:** 20,000+ lines  
**Author:** RAGBench Documentation Team

---

## Table of Contents (Part 1)

1. [Introduction](#1-introduction)
2. [Architecture Overview](#2-architecture-overview)
3. [Installation & Setup](#3-installation-setup)
4. [Quick Start Guide](#4-quick-start-guide)
5. [Layer 1: Document Ingestion](#5-layer-1-document-ingestion)
6. [Layer 2: Chunking](#6-layer-2-chunking)
7. [Layer 3: Embedding](#7-layer-3-embedding)
8. [Layer 4: Vector Stores](#8-layer-4-vector-stores)
9. [Layer 5: Retrieval](#9-layer-5-retrieval)
10. [Layer 6: Reranking](#10-layer-6-reranking)
11. [Layer 7: Generation](#11-layer-7-generation)
12. [Pipeline & Auto Mode](#12-pipeline-auto-mode)
13. [Complete RAG Workflows](#13-complete-rag-workflows)

**📖 [Continue to Part 2 →](COMPREHENSIVE_GUIDE_PART2.md)** for Advanced Topics, Configuration, Troubleshooting, Best Practices, Contributing, API Reference, and Appendices.

---

# 1. Introduction

## 1.1 What is RAGBench?

RAGBench (formerly OmniLoad) is a **layered, open-source auto-RAG framework** designed for building production-ready Retrieval Augmented Generation (RAG) systems. It provides a complete pipeline from document ingestion to answer generation, with every layer being:

- **Independent**: Each layer works standalone without dependencies on other layers
- **Connectable**: Layers communicate through tiny, well-defined data contracts
- **Chainable**: The entire pipeline can be executed with a single function call

### 1.1.1 What is RAG?

Retrieval Augmented Generation (RAG) is a technique that enhances Large Language Models (LLMs) by:

1. **Retrieving** relevant information from a knowledge base
2. **Augmenting** the LLM prompt with this context
3. **Generating** accurate, grounded answers based on retrieved facts

This approach solves key LLM limitations:
- ❌ Hallucinations (making up facts)
- ❌ Outdated knowledge (training cutoff)
- ❌ No domain-specific knowledge
- ❌ Cannot cite sources

RAG enables:
- ✅ Factual, source-backed answers
- ✅ Up-to-date information
- ✅ Domain expertise
- ✅ Traceable citations

### 1.1.2 Why RAGBench?

**Problem:** Most RAG frameworks are monolithic, tightly coupled, and hard to customize.

**Solution:** RAGBench provides:

1. **Layer Independence**: Use any layer standalone or combine them
2. **Method Diversity**: 
   - 82 document connectors
   - 106 chunking methods
   - 61 embedding methods
   - 41 vector stores
   - 28 retrieval methods
   - 21 reranking methods
   - 9 LLM providers

3. **Smart Defaults**: Auto-routing picks optimal methods for your data
4. **Offline Capability**: Works without API keys using local baselines
5. **Production-Ready**: Robust error handling, caching, and monitoring

### 1.1.3 Core Philosophy

```
"One folder per method"
```

Every method (connector, chunker, embedder, etc.) lives in its own folder with:
- `metadata.json` - Method configuration
- Implementation file(s)
- `requirements.txt` - Dependencies
- `__init__.py` - Public API

This makes RAGBench:
- **Discoverable**: New methods are auto-registered
- **Extensible**: Add methods without modifying core
- **Maintainable**: Each method is isolated
- **Testable**: Layer independence is enforced

## 1.2 The RAG Pipeline

```
┌─────────────┐     ┌──────────┐     ┌───────────┐     ┌──────────────┐
│  Documents  │────▶│  Chunks  │────▶│ Embeddings│────▶│ Vector Store │
│  (Layer 1)  │     │ (Layer 2)│     │ (Layer 3) │     │  (Layer 4)   │
└─────────────┘     └──────────┘     └───────────┘     └──────────────┘
                                                               │
                                                               ▼
┌─────────────┐     ┌──────────┐     ┌───────────┐     ┌──────────────┐
│   Answer    │◀────│ Generate │◀────│  Rerank   │◀────│   Retrieve   │
│  (Layer 7)  │     │ (Layer 7)│     │ (Layer 6) │     │  (Layer 5)   │
└─────────────┘     └──────────┘     └───────────┘     └──────────────┘
```

### Pipeline Stages:

1. **Ingestion** (Layer 1): Load documents from 82+ sources
2. **Chunking** (Layer 2): Split documents into manageable pieces
3. **Embedding** (Layer 3): Convert text to numerical vectors
4. **Vector Store** (Layer 4): Index and store embeddings
5. **Retrieval** (Layer 5): Find relevant chunks for a query
6. **Reranking** (Layer 6): Refine retrieval results
7. **Generation** (Layer 7): Generate answers with LLMs

## 1.3 Key Features

### ✅ Complete RAG Pipeline
- End-to-end: Retrieve → Rerank → Generate in one call
- 21 reranking methods (BM25, Cohere, BGE, ColBERT, etc.)
- 9 LLM providers (Ollama, OpenAI, Anthropic, Cohere, etc.)
- Streaming generation support
- Multi-turn conversations with history
- Citation extraction and tracking

### ✅ Modular Architecture
- 82 connectors (PDF, Office, databases, cloud storage, APIs)
- 106 chunking methods (semantic, fixed, sliding, hierarchical)
- 61 embedding methods (OpenAI, Cohere, local models)
- 41 vector stores (Pinecone, Qdrant, local SQLite/DuckDB)
- 28 retrieval methods (dense, sparse, hybrid, fusion)

### ✅ Smart Defaults
- Auto-routing picks optimal methods for your data
- Works offline with `hash_baseline` embedder + BM25
- Fallback to local models when APIs unavailable
- No hardcoded API keys anywhere

### ✅ Developer Experience
- Layer independence enforced by tests
- Comprehensive error messages with suggestions
- Disk caching for embeddings
- Token usage tracking
- Detailed logging

## 1.4 Use Cases

### 1.4.1 Question Answering
Build intelligent Q&A systems over your documents:
```python
answer = ask("What is quantum computing?", collection="docs")
```

### 1.4.2 Document Search
Semantic search across large document collections:
```python
hits = retrieve("machine learning algorithms", corpus=chunks, method="hybrid")
```

### 1.4.3 Chatbots
Multi-turn conversational AI with context:
```python
answer = ask(query, collection="docs", conversation=conversation)
```

### 1.4.4 Content Generation
Generate content based on retrieved context:
```python
generator.generate(query="Summarize", context=retrieved_chunks)
```

### 1.4.5 Research Assistant
Help researchers find relevant information:
```python
answer = ask(
    "Recent advances in transformer models?",
    collection="research_papers",
    rerank=True
)
```

## 1.5 Who Should Use RAGBench?

- **Data Scientists**: Building RAG systems for research
- **ML Engineers**: Deploying production RAG pipelines
- **Developers**: Integrating RAG into applications
- **Researchers**: Experimenting with RAG techniques
- **Students**: Learning about RAG systems

## 1.6 Comparison with Other Frameworks

| Feature | RAGBench | LangChain | LlamaIndex | Haystack |
|---------|----------|-----------|------------|----------|
| Layer Independence | ✅ | ❌ | ❌ | ⚠️ |
| Method Diversity | 340+ methods | Limited | Limited | Moderate |
| Auto-routing | ✅ | ❌ | ⚠️ | ❌ |
| Offline Mode | ✅ | ❌ | ❌ | ⚠️ |
| One Folder/Method | ✅ | ❌ | ❌ | ❌ |
| Testing Coverage | High | Moderate | Moderate | High |
| Documentation | Comprehensive | Good | Good | Good |

---

# 2. Architecture Overview

## 2.1 Layer Independence

RAGBench's core principle is **layer independence**. Each layer:

- **Imports nothing** from other layers (except data models)
- **Works standalone** with its own API
- **Has no side effects** on other layers
- **Is fully tested** in isolation

### 2.1.1 Enforcement

Layer independence is enforced by:

1. **Import checks**: `tests/test_independence.py` fails if layers import each other
2. **Integration tests**: Each layer has standalone tests
3. **Type contracts**: Layers communicate through small, typed interfaces

### 2.1.2 Benefits

- **Flexibility**: Use only the layers you need
- **Maintainability**: Changes don't cascade
- **Testing**: Mock dependencies easily
- **Understanding**: Learn one layer at a time

## 2.2 Data Contracts

Layers communicate through simple data structures:

### Document (Layer 1 → 2)
```python
@dataclass
class Document:
    id: str                    # Unique identifier
    content: str              # Text content
    metadata: Dict[str, Any]  # Source, page, etc.
    modality: str             # text, image, audio, etc.
```

### Chunk (Layer 2 → 3)
```python
@dataclass
class Chunk:
    id: str                    # Unique identifier
    document_id: str          # Parent document
    content: str              # Chunk text
    metadata: Dict[str, Any]  # Position, method, etc.
```

### EmbeddedChunk (Layer 3 → 4)
```python
@dataclass
class EmbeddedChunk:
    id: str                    # Unique identifier
    chunk_id: str             # Parent chunk
    document_id: str          # Root document
    content: str              # Text content
    vector: List[float]       # Dense embedding
    sparse: Dict[int, float]  # Sparse embedding
    multi_vector: List[List[float]]  # Multi-vector
    model: str                # Embedding model
    dim: int                  # Vector dimension
    modality: str             # text, image, etc.
    metadata: Dict[str, Any]
```

### RetrievalResult (Layer 5 → 6)
```python
@dataclass
class RetrievalResult:
    id: str                    # Unique identifier
    score: float              # Relevance (higher = better)
    content: str              # Retrieved text
    chunk_id: str             # Source chunk
    document_id: str          # Source document
    metadata: Dict[str, Any]  # Additional info
```

### GeneratedAnswer (Layer 7)
```python
@dataclass
class GeneratedAnswer:
    answer: str               # Generated text
    citations: List[Citation] # Source citations
    query: str                # Original query
    model: str                # LLM model used
    tokens_used: int          # Token count
    duration_s: float         # Generation time
```

## 2.3 Directory Structure

```
ragbench/
├── src/ragbench/
│   ├── core/                 # Core data models
│   ├── connectors/           # 82 document connectors
│   ├── ingestion/            # Layer 1: Document loading
│   ├── chunking/             # Layer 2: Text chunking
│   │   └── methods/          # 106 chunking methods
│   ├── embedding/            # Layer 3: Text embeddings
│   │   └── methods/          # 61 embedding methods
│   ├── vectorstore/          # Layer 4: Vector storage
│   │   └── methods/          # 41 vector stores
│   ├── retrieval/            # Layer 5: Information retrieval
│   │   └── methods/          # 28 retrieval methods
│   ├── reranking/            # Layer 6: Result reranking
│   │   └── methods/          # 21 reranking methods
│   ├── generation/           # Layer 7: Answer generation
│   │   └── methods/          # 9 LLM providers
│   ├── pipeline/             # Auto mode & orchestration
│   └── evaluation/           # RAG evaluation metrics
├── tests/                    # Comprehensive test suite
├── examples/                 # Example scripts
├── docs/                     # Documentation
└── scripts/                  # Utility scripts
```

## 2.4 Registry Pattern

Every layer uses the **Registry Pattern** for method discovery:

```python
# Embedding registry
from ragbench.embedding import REGISTRY

# List all methods
methods = REGISTRY.list_names()  # 61 methods

# Get method info
info = REGISTRY.get("openai")

# List by category
api_methods = REGISTRY.list_by_category("api")

# Check availability
status = REGISTRY.status("bge")  # available, unavailable, missing_packages
```

Benefits:
- **Auto-discovery**: New methods are automatically found
- **Lazy loading**: Heavy imports only when used
- **Status checking**: Know what's available
- **Metadata**: Query capabilities without loading

## 2.5 Routing System

RAGBench's **auto-routing** intelligently selects methods:

### 2.5.1 Embedding Routing
```python
# Explicit method
embed(text, method="openai")  # Use OpenAI

# Auto-routing (checks in order):
# 1. Explicit method parameter
# 2. Language hint in metadata
# 3. Modality (text, image, code, etc.)
# 4. Configured default (OMNILOAD_EMBEDDER)
# 5. hash_baseline (offline fallback)
embed(text, method="auto")
```

### 2.5.2 Retrieval Routing
```python
# Auto-routing considers:
# - Query type (keyword, question, code, etc.)
# - Store capabilities (dense, sparse, hybrid)
# - Filters (metadata conditions)
# - Collection count (single vs multi)

retrieve(query, method="auto", store=my_store)
```

### 2.5.3 Chunking Routing
```python
# Routes based on:
# - File extension
# - Content type
# - Document structure
# - Explicit method parameter

chunk(document, method="auto")
```

## 2.6 Error Handling

RAGBench has a comprehensive error hierarchy:

```python
# Base errors
RAGBenchError                 # All errors inherit from this

# Layer-specific errors
EmbeddingError
  ├─ MissingCredentialError   # API key missing
  ├─ DependencyMissingError   # Package not installed
  ├─ ModelNotFoundError       # Model doesn't exist
  └─ QuotaExceededError       # Rate limit hit

GenerationError
  ├─ ConfigError              # Invalid configuration
  ├─ ProviderError            # API/provider error
  ├─ TransientError           # Temporary (retry)
  ├─ ContextLengthError       # Input too long
  └─ RefusalError             # Model refused (safety)

RetrievalError
VectorStoreError
ChunkingError
```

Errors include:
- **Clear messages**: What went wrong
- **Suggestions**: How to fix it
- **Context**: Relevant state/config
- **Redaction**: Secrets never logged

---

# 3. Installation & Setup

## 3.1 System Requirements

### 3.1.1 Python Version
- **Minimum**: Python 3.9
- **Recommended**: Python 3.10 or 3.11
- **Supported**: 3.9, 3.10, 3.11, 3.12, 3.13

### 3.1.2 Operating Systems
- ✅ Linux (Ubuntu, Debian, CentOS, etc.)
- ✅ macOS (Intel and Apple Silicon)
- ✅ Windows 10/11 (PowerShell)

### 3.1.3 Hardware
- **Minimum RAM**: 4GB (for basic usage)
- **Recommended RAM**: 16GB+ (for local models)
- **Storage**: 2GB+ free space
- **GPU**: Optional (for local embeddings/LLMs)

## 3.2 Installation Methods

### 3.2.1 Install from PyPI (Recommended)

```bash
# Base installation (standard library only)
pip install ragbench-ai

# With local embedders
pip install "ragbench-ai[embed-local]"

# With all features
pip install "ragbench-ai[all]"
```

### 3.2.2 Install from Source

```bash
# Clone the repository
git clone https://github.com/Omkar-S-Kulkarni/ragbench.git
cd ragbench

# Base install
pip install -e .

# With extras
pip install -e ".[embed-local,store-local,generate-all]"
```

### 3.2.3 Using Virtual Environments

**Using venv:**
```bash
python -m venv ragbench-env
source ragbench-env/bin/activate  # On Windows: ragbench-env\Scripts\activate
pip install ragbench-ai
```

**Using conda:**
```bash
conda create -n ragbench python=3.10
conda activate ragbench
pip install ragbench-ai
```

## 3.3 Optional Dependencies

RAGBench uses **optional extras** for different features:

### 3.3.1 Embedding Extras

```bash
# Local embedders (transformers, sentence-transformers)
pip install "ragbench-ai[embed-local]"

# Multimodal embeddings (images, audio)
pip install "ragbench-ai[embed-multimodal]"

# Multi-vector models (ColBERT, ColPali)
pip install "ragbench-ai[embed-multivector]"

# Classical methods (word2vec, fasttext)
pip install "ragbench-ai[embed-classical]"

# AWS Bedrock
pip install "ragbench-ai[embed-bedrock]"

# All embedding methods
pip install "ragbench-ai[embed-all]"
```

### 3.3.2 Vector Store Extras

```bash
# Local stores (FAISS, HNSW, Annoy, ChromaDB)
pip install "ragbench-ai[store-local]"

# SQLite vector extension
pip install "ragbench-ai[store-sqlite]"

# DuckDB
pip install "ragbench-ai[store-duckdb]"

# Qdrant
pip install "ragbench-ai[store-qdrant]"

# Pinecone
pip install "ragbench-ai[store-pinecone]"

# Weaviate
pip install "ragbench-ai[store-weaviate]"

# All stores
pip install "ragbench-ai[store-all]"
```

### 3.3.3 Reranking Extras

```bash
# Local rerankers (BGE, MiniLM)
pip install "ragbench-ai[rerank-local]"

# API rerankers (Cohere, Voyage, Jina)
pip install "ragbench-ai[rerank-api]"
```

### 3.3.4 Generation Extras

```bash
# Ollama (local LLM)
pip install "ragbench-ai[generate-ollama]"

# OpenAI
pip install "ragbench-ai[generate-openai]"

# Anthropic Claude
pip install "ragbench-ai[generate-anthropic]"

# Google Gemini
pip install "ragbench-ai[generate-gemini]"

# All LLM providers
pip install "ragbench-ai[generate-all]"
```

### 3.3.5 Development Extras

```bash
# Testing and development tools
pip install "ragbench-ai[dev]"

# Evaluation metrics
pip install "ragbench-ai[eval-all]"
```

### 3.3.6 Install Everything

```bash
# Complete installation with all features
pip install "ragbench-ai[all]"
```

## 3.4 Configuration

### 3.4.1 Environment Variables

RAGBench uses environment variables for configuration. Create a `.env` file:

```bash
# Copy the example file
cp .env.example .env

# Edit with your settings
nano .env  # or vim, code, etc.
```

**Important:** Never commit `.env` to version control!

### 3.4.2 Basic Configuration

```bash
# Framework defaults
OMNILOAD_EMBEDDER=openai              # Default embedder
OMNILOAD_VECTORSTORE=sqlite           # Default vector store
OMNILOAD_RETRIEVER=auto               # Default retriever
OMNILOAD_COLLECTION=default           # Default collection name

# Paths
OMNILOAD_CACHE_DIR=~/.cache/ragbench  # Cache directory
OMNILOAD_VECTORSTORE_PATH=./data      # Persistent storage
```

### 3.4.3 API Keys

Add your API keys to `.env`:

```bash
# OpenAI
OPENAI_API_KEY=sk-...

# Anthropic
ANTHROPIC_API_KEY=sk-ant-...

# Cohere
COHERE_API_KEY=...

# Voyage AI
VOYAGE_API_KEY=...

# Google
GOOGLE_API_KEY=...

# Jina AI
JINA_API_KEY=...

# Mistral AI
MISTRAL_API_KEY=...

# Hugging Face (for model downloads)
HF_TOKEN=...
```

### 3.4.4 Verify Installation

```python
# Check RAGBench version
import ragbench
print(ragbench.__version__)  # Should print "0.4.0"

# Check available embedders
from ragbench.embedding import REGISTRY
print(f"Embedders available: {len(REGISTRY.list_names())}")

# Check available stores
from ragbench.vectorstore import REGISTRY as STORE_REGISTRY
print(f"Vector stores available: {len(STORE_REGISTRY.list_names())}")

# Test basic functionality
from ragbench.pipeline import auto
result = auto("Hello world", as_text=True, method="auto")
print(f"✓ Chunking works! Got {len(result)} chunks")
```

## 3.5 Setting Up Ollama (Optional)

Ollama provides free, local LLMs with no API keys:

### 3.5.1 Install Ollama

**macOS/Linux:**
```bash
curl -fsSL https://ollama.ai/install.sh | sh
```

**Windows:**
Download from https://ollama.ai

### 3.5.2 Start Ollama

```bash
ollama serve
```

### 3.5.3 Pull Models

```bash
# Small, fast model (1.7B parameters)
ollama pull qwen2:1.5b

# Medium model (8B parameters)
ollama pull llama3.1

# Large model (70B parameters, requires 48GB RAM)
ollama pull llama3.1:70b

# List available models
ollama list
```

### 3.5.4 Test Ollama

```python
from ragbench.generation import Generator

generator = Generator(method="ollama", model="llama3.1")

if generator.is_available():
    print("✓ Ollama is working!")
else:
    print("✗ Ollama not available")
```

## 3.6 Common Installation Issues

### 3.6.1 "No module named 'ragbench'"

**Cause:** Package not installed or wrong Python environment

**Solution:**
```bash
# Check Python version
python --version

# Check if installed
pip show ragbench-ai

# Install if missing
pip install ragbench-ai
```

### 3.6.2 "torch not available"

**Cause:** PyTorch not installed (needed for local models)

**Solution:**
```bash
# Install with local embedders
pip install "ragbench-ai[embed-local]"

# Or install PyTorch directly
pip install torch torchvision
```

### 3.6.3 "CUDA out of memory"

**Cause:** GPU memory insufficient for model

**Solution:**
```python
# Use smaller models
embedder = Embedder("minilm")  # Instead of bge-large

# Or use CPU
embedder = Embedder("bge", device="cpu")
```

### 3.6.4 "SSL certificate verify failed"

**Cause:** Corporate firewall or certificate issue

**Solution:**
```bash
# Add trusted certificates
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org ragbench-ai

# Or disable SSL verification (not recommended)
pip install ragbench-ai --no-cache-dir
```

### 3.6.5 "Permission denied"

**Cause:** Insufficient permissions

**Solution:**
```bash
# Use --user flag
pip install --user ragbench-ai

# Or use virtual environment
python -m venv venv
source venv/bin/activate
pip install ragbench-ai
```

## 3.7 Docker Setup (Optional)

Run RAGBench in a container:

### 3.7.1 Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    git \
    && rm -rf /var/lib/apt/lists/*

# Install RAGBench
RUN pip install "ragbench-ai[all]"

# Copy your code
COPY . .

CMD ["python", "your_script.py"]
```

### 3.7.2 Build and Run

```bash
# Build image
docker build -t ragbench .

# Run container
docker run -v $(pwd):/app -e OPENAI_API_KEY=$OPENAI_API_KEY ragbench
```

## 3.8 Cloud Deployment

### 3.8.1 AWS Lambda

```bash
# Install in Lambda layer
pip install ragbench-ai -t python/
zip -r layer.zip python/
aws lambda publish-layer-version --layer-name ragbench --zip-file fileb://layer.zip
```

### 3.8.2 Google Cloud Run

```yaml
# app.yaml
runtime: python311
entrypoint: gunicorn -b :$PORT main:app

automatic_scaling:
  target_cpu_utilization: 0.65
  max_instances: 10
```

### 3.8.3 Azure Functions

```python
# requirements.txt
ragbench-ai[all]
azure-functions
```

---

# 4. Quick Start Guide

## 4.1 30-Second Quickstart

The absolute fastest way to get started:

```python
from ragbench.pipeline import ask, auto

# 1. Index some text
auto(
    "Python is a programming language created by Guido van Rossum.",
    as_text=True,
    embed=True,
    index=True,
    embedder="hash_baseline",  # No API key needed
    store="memory",
    collection="demo"
)

# 2. Ask a question
answer = ask(
    "Who created Python?",
    collection="demo",
    store="memory",
    embedder="hash_baseline",
    generation_method="ollama"  # Requires Ollama
)

print(answer.answer)
# Output: "Python was created by Guido van Rossum."
```

## 4.2 5-Minute Tutorial

### Step 1: Install

```bash
pip install ragbench-ai
```

### Step 2: Index Documents

```python
from ragbench.pipeline import auto

# Index a file
result = auto(
    "path/to/document.pdf",
    embed=True,
    embedder="hash_baseline",
    index=True,
    store="sqlite",
    collection="my_docs"
)

print(f"Indexed {len(result.ids)} chunks")
```

### Step 3: Ask Questions

```python
from ragbench.pipeline import ask

answer = ask(
    query="What is the main topic?",
    collection="my_docs",
    store="sqlite",
    embedder="hash_baseline",
    generation_method="ollama"
)

print(answer.answer)
```

## 4.3 Complete Example: Local Setup (No API Keys)

This example uses only free, local tools:

```python
from ragbench.pipeline import ask, auto

# === INDEXING PHASE ===

documents = [
    "Quantum computing uses qubits that can be in superposition.",
    "Classical computers use bits that are either 0 or 1.",
    "Quantum entanglement allows qubits to be correlated.",
    "Machine learning is a subset of artificial intelligence.",
    "Neural networks are inspired by biological neurons."
]

print("Indexing documents...")
for i, doc in enumerate(documents):
    auto(
        doc,
        as_text=True,
        embed=True,
        embedder="hash_baseline",  # Free, offline
        index=True,
        store="memory",  # In-memory, no setup
        collection="tech_docs"
    )
    print(f"  ✓ Indexed {i+1}/{len(documents)}")

# === QUERYING PHASE ===

print("\nAsking questions...")

# Question 1
answer1 = ask(
    query="What is quantum computing?",
    collection="tech_docs",
    store="memory",
    embedder="hash_baseline",
    k=3,  # Retrieve top 3 chunks
    rerank=True,
    rerank_method="bm25",  # Free, offline
    rerank_k=2,  # Keep top 2 after reranking
    generation_method="ollama",  # Free, local
    generation_options={"model": "llama3.1"}
)

print(f"\nQ: What is quantum computing?")
print(f"A: {answer1.answer}\n")

# Question 2
answer2 = ask(
    query="Explain machine learning",
    collection="tech_docs",
    store="memory",
    embedder="hash_baseline",
    generation_method="ollama"
)

print(f"Q: Explain machine learning")
print(f"A: {answer2.answer}\n")
```

## 4.4 Complete Example: Cloud Setup (With API Keys)

This example uses cloud APIs for better quality:

```python
import os
from ragbench.pipeline import ask, auto

# Set API keys
os.environ["OPENAI_API_KEY"] = "sk-..."
os.environ["COHERE_API_KEY"] = "..."

# === INDEXING ===

print("Indexing with OpenAI embeddings...")
result = auto(
    "path/to/documents/",  # Directory of files
    embed=True,
    embedder="openai",  # High-quality embeddings
    embed_options={"model": "text-embedding-3-small"},
    index=True,
    store="sqlite",  # Persistent local storage
    collection="company_docs"
)

print(f"✓ Indexed {len(result.ids)} chunks")

# === QUERYING ===

print("\nQuerying with full RAG pipeline...")
answer = ask(
    query="What are our Q4 revenue projections?",
    collection="company_docs",
    store="sqlite",
    embedder="openai",
    k=20,  # Retrieve more chunks
    rerank=True,
    rerank_method="cohere",  # Best reranker
    rerank_k=5,  # Keep top 5
    generation_method="openai",  # GPT-4
    generation_options={
        "model": "gpt-4o-mini",
        "temperature": 0.3  # More factual
    },
    include_retrieval=True,  # Return retrieval results
    include_reranking=True   # Return reranking results
)

print(f"\n{'='*70}")
print(f"Query: {answer['answer'].query}")
print(f"{'='*70}")
print(f"\nAnswer:\n{answer['answer'].answer}")
print(f"\nMetadata:")
print(f"  • Retrieved: {len(answer.get('retrieval', []))} chunks")
print(f"  • Reranked to: {len(answer.get('reranking', []))} chunks")
print(f"  • Model: {answer['answer'].model}")
print(f"  • Tokens: {answer['answer'].tokens_used}")
print(f"  • Duration: {answer['answer'].duration_s:.2f}s")
print(f"  • Citations: {len(answer['answer'].citations)}")

if answer['answer'].citations:
    print(f"\nSources:")
    for i, citation in enumerate(answer['answer'].citations, 1):
        print(f"  [{i}] {citation.document_id}")
        print(f"      Score: {citation.score:.2f}")
        print(f"      Content: {citation.content[:100]}...")
```

## 4.5 Streaming Responses

For better user experience, stream tokens as they're generated:

```python
from ragbench.pipeline import ask

print("Answer: ", end="", flush=True)

# Stream tokens in real-time
for token in ask(
    query="Explain quantum entanglement",
    collection="tech_docs",
    store="memory",
    embedder="hash_baseline",
    generation_method="ollama",
    streaming=True  # Enable streaming
):
    print(token, end="", flush=True)

print()  # Newline
```

## 4.6 Multi-Turn Conversations

Build chatbots with conversation history:

```python
from ragbench.pipeline import ask
from ragbench.generation import Conversation

# Create conversation
conversation = Conversation()

# Turn 1
print("User: What is supervised learning?")
answer1 = ask(
    query="What is supervised learning?",
    collection="ml_docs",
    generation_method="ollama",
    conversation=conversation
)
print(f"Assistant: {answer1.answer}\n")

# Add to history
conversation.add_user("What is supervised learning?")
conversation.add_assistant(answer1.answer)

# Turn 2 (context-aware)
print("User: How does it differ from unsupervised learning?")
answer2 = ask(
    query="How does it differ from unsupervised learning?",
    collection="ml_docs",
    generation_method="ollama",
    conversation=conversation  # Pass history
)
print(f"Assistant: {answer2.answer}\n")

# Turn 3
conversation.add_user("How does it differ from unsupervised learning?")
conversation.add_assistant(answer2.answer)

print("User: Give me an example")
answer3 = ask(
    query="Give me an example",
    collection="ml_docs",
    generation_method="ollama",
    conversation=conversation
)
print(f"Assistant: {answer3.answer}\n")
```

## 4.7 Next Steps

After completing the quickstart:

1. **Learn the layers**: Read the detailed layer documentation
2. **Try different methods**: Experiment with embedders, rerankers, LLMs
3. **Index real documents**: Use your own data
4. **Evaluate quality**: Add evaluation metrics
5. **Deploy to production**: Scale your RAG system

---

# 5. Layer 1: Document Ingestion

## 5.1 Overview

The ingestion layer loads documents from 82+ data sources into RAGBench's canonical `Document` format.

### 5.1.1 Key Features

- **82 Connectors**: PDF, Office, databases, APIs, cloud storage, etc.
- **Format Preservation**: Retains structure (tables, images, metadata)
- **Error Handling**: Robust parsing with fallbacks
- **Metadata Extraction**: Author, date, page numbers, etc.

### 5.1.2 Document Format

```python
@dataclass
class Document:
    id: str                    # Unique identifier
    content: str              # Text content
    metadata: Dict[str, Any]  # Source metadata
    modality: str             # text, image, audio, video, etc.
    language: Optional[str]   # Detected language
    embedding: Optional[List[float]]  # Pre-computed embedding
```

## 5.2 Basic Usage

### 5.2.1 Load a Single File

```python
from ragbench.ingestion import ingest

# Load a PDF
docs = ingest("report.pdf")

for doc in docs:
    print(f"Document {doc.id}")
    print(f"Content: {doc.content[:200]}...")
    print(f"Metadata: {doc.metadata}")
```

### 5.2.2 Load Multiple Files

```python
# Load a directory
docs = ingest("path/to/documents/")

# Load specific files
docs = ingest(["file1.pdf", "file2.docx", "file3.txt"])
```

### 5.2.3 Load from Connector

```python
# SQLite database
docs = ingest(
    connector="sqlite",
    config={"file_path": "data.sqlite"}
)

# PostgreSQL
docs = ingest(
    connector="postgresql",
    config={
        "host": "localhost",
        "database": "mydb",
        "user": "postgres",
        "password": "secret"
    }
)

# S3 bucket
docs = ingest(
    connector="s3",
    config={
        "bucket": "my-bucket",
        "prefix": "documents/"
    }
)
```

## 5.3 Supported Formats

### 5.3.1 Document Formats

| Format | Connector | Features |
|--------|-----------|----------|
| PDF | `pdf`, `pdf_scanned`, `pdf_hybrid` | Text, OCR, tables, images |
| Word | `docx`, `doc` | Text, formatting, tables |
| PowerPoint | `pptx`, `ppt` | Slides, notes, text |
| Excel | `xlsx`, `xls`, `csv` | Tables, sheets |
| Text | `txt`, `md`, `rst` | Plain text, markdown |
| HTML | `html`, `xml` | Structure, links |
| Email | `eml`, `msg`, `outlook` | Headers, attachments |
| Archives | `zip`, `tar`, `gz` | Recursive extraction |

### 5.3.2 Database Connectors

| Database | Connector | Example |
|----------|-----------|---------|
| SQLite | `sqlite` | `{"file_path": "data.db"}` |
| PostgreSQL | `postgresql` | `{"host": "...", "database": "..."}` |
| MySQL | `mysql` | `{"host": "...", "database": "..."}` |
| MongoDB | `mongodb` | `{"uri": "mongodb://..."}` |
| Redis | `redis` | `{"url": "redis://..."}` |
| Elasticsearch | `elasticsearch` | `{"url": "...", "index": "..."}` |

### 5.3.3 Cloud Storage

| Provider | Connector | Example |
|----------|-----------|---------|
| AWS S3 | `s3` | `{"bucket": "...", "prefix": "..."}` |
| Google Cloud Storage | `gcs` | `{"bucket": "...", "prefix": "..."}` |
| Azure Blob | `azure_blob` | `{"container": "...", "prefix": "..."}` |
| Dropbox | `dropbox` | `{"path": "...", "token": "..."}` |
| Google Drive | `google_drive` | `{"folder_id": "...", "token": "..."}` |

### 5.3.4 APIs & Services

| Service | Connector | Features |
|---------|-----------|----------|
| Web Scraping | `web`, `sitemap` | HTML parsing, links |
| RSS/Atom | `rss`, `atom` | Feed parsing |
| Notion | `notion` | Pages, databases |
| Confluence | `confluence` | Spaces, pages |
| GitHub | `github` | Repos, issues, PRs |
| GitLab | `gitlab` | Repos, issues |
| Jira | `jira` | Issues, comments |
| Slack | `slack` | Messages, threads |
| Discord | `discord` | Messages, channels |
| Email | `imap`, `gmail` | Messages, attachments |

## 5.4 Advanced Ingestion

### 5.4.1 PDF with OCR

```python
# Standard PDF (text extraction)
docs = ingest("document.pdf", connector="pdf")

# Scanned PDF (OCR required)
docs = ingest("scanned.pdf", connector="pdf_scanned")

# Hybrid (try text first, OCR if needed)
docs = ingest("mixed.pdf", connector="pdf_hybrid")

# Configure OCR
docs = ingest(
    "scanned.pdf",
    connector="pdf_scanned",
    config={
        "tesseract_cmd": "/usr/bin/tesseract",  # Tesseract path
        "poppler_path": "/usr/bin",  # Poppler path (Windows)
        "language": "eng+fra",  # Multiple languages
        "dpi": 300  # Image quality
    }
)
```

### 5.4.2 Database Queries

```python
# Custom SQL query
docs = ingest(
    connector="postgresql",
    config={
        "host": "localhost",
        "database": "mydb",
        "user": "postgres",
        "password": "secret",
        "query": "SELECT id, title, content FROM articles WHERE published = true",
        "id_column": "id",
        "content_column": "content",
        "metadata_columns": ["title", "published_date"]
    }
)
```

### 5.4.3 Web Scraping

```python
# Single page
docs = ingest(
    "https://example.com/page",
    connector="web"
)

# Sitemap crawl
docs = ingest(
    "https://example.com/sitemap.xml",
    connector="sitemap",
    config={
        "max_pages": 100,
        "include_links": True,
        "exclude_patterns": ["/admin", "/api"]
    }
)

# Recursive crawl
docs = ingest(
    "https://example.com",
    connector="web",
    config={
        "recursive": True,
        "max_depth": 3,
        "same_domain": True
    }
)
```

### 5.4.4 Archive Extraction

```python
# Extract ZIP archive
docs = ingest(
    "archive.zip",
    connector="zip",
    config={
        "recursive": True,  # Extract nested archives
        "extensions": [".pdf", ".docx", ".txt"]  # Filter by extension
    }
)
```

### 5.4.5 Image OCR

```python
# Extract text from images
docs = ingest(
    "image.png",
    connector="image_ocr",
    config={
        "language": "eng",
        "tesseract_cmd": "/usr/bin/tesseract"
    }
)

# Batch process images
docs = ingest(
    "path/to/images/",
    connector="image_ocr",
    config={"extensions": [".png", ".jpg", ".jpeg"]}
)
```

## 5.5 Connector Configuration

### 5.5.1 PostgreSQL

```python
docs = ingest(
    connector="postgresql",
    config={
        "host": "localhost",
        "port": 5432,
        "database": "mydb",
        "user": "postgres",
        "password": "secret",
        "table": "documents",  # Optional: specific table
        "query": "SELECT * FROM articles WHERE published = true",  # Optional
        "id_column": "id",
        "content_column": "content",
        "metadata_columns": ["title", "author", "date"]
    }
)
```

### 5.5.2 MongoDB

```python
docs = ingest(
    connector="mongodb",
    config={
        "uri": "mongodb://localhost:27017",
        "database": "mydb",
        "collection": "documents",
        "query": {"status": "published"},  # Optional filter
        "id_field": "_id",
        "content_field": "content",
        "metadata_fields": ["title", "author"]
    }
)
```

### 5.5.3 AWS S3

```python
docs = ingest(
    connector="s3",
    config={
        "bucket": "my-bucket",
        "prefix": "documents/",  # Optional folder
        "aws_access_key_id": "...",  # Or use AWS credentials
        "aws_secret_access_key": "...",
        "region": "us-east-1",
        "extensions": [".pdf", ".docx"]  # Filter files
    }
)
```

### 5.5.4 Google Cloud Storage

```python
docs = ingest(
    connector="gcs",
    config={
        "bucket": "my-bucket",
        "prefix": "documents/",
        "credentials_path": "service-account.json",  # Service account
        "project": "my-project"
    }
)
```

### 5.5.5 Notion

```python
docs = ingest(
    connector="notion",
    config={
        "token": "secret_...",  # Integration token
        "database_id": "...",  # Optional: specific database
        "page_ids": ["..."],  # Optional: specific pages
        "include_properties": True,  # Include page properties
        "include_children": True  # Include nested blocks
    }
)
```

### 5.5.6 Confluence

```python
docs = ingest(
    connector="confluence",
    config={
        "url": "https://company.atlassian.net/wiki",
        "username": "user@company.com",
        "api_token": "...",
        "space_key": "PROJ",  # Optional: specific space
        "cql": "type=page AND label=documentation"  # Optional: CQL query
    }
)
```

### 5.5.7 GitHub

```python
docs = ingest(
    connector="github",
    config={
        "token": "ghp_...",  # Personal access token
        "repo": "owner/repo",
        "branch": "main",  # Optional
        "path": "docs/",  # Optional: specific folder
        "include_issues": True,  # Include issues
        "include_prs": True  # Include pull requests
    }
)
```

## 5.6 Metadata Extraction

Connectors automatically extract metadata:

```python
docs = ingest("report.pdf")

for doc in docs:
    print(doc.metadata)
    # {
    #     "source": "report.pdf",
    #     "page": 1,
    #     "total_pages": 10,
    #     "author": "John Doe",
    #     "created_date": "2024-01-15",
    #     "modified_date": "2024-01-20",
    #     "file_size": 1024000,
    #     "mime_type": "application/pdf",
    #     "language": "en",
    #     "connector": "pdf"
    # }
```

## 5.7 Error Handling

```python
from ragbench.ingestion import ingest
from ragbench.core.errors import IngestionError

try:
    docs = ingest("corrupted.pdf")
except IngestionError as e:
    print(f"Ingestion failed: {e}")
    print(f"Suggestions: {e.suggestions}")
```

## 5.8 Custom Connectors

Create your own connector:

```python
from ragbench.core import Document
from ragbench.connectors.base import BaseConnector

class MyConnector(BaseConnector):
    """Custom connector for my data source."""
    
    def __init__(self, **config):
        self.config = config
    
    def load(self) -> List[Document]:
        """Load documents from source."""
        # Your loading logic here
        documents = []
        
        # Example: load from API
        response = requests.get(self.config["url"])
        data = response.json()
        
        for item in data:
            doc = Document(
                id=item["id"],
                content=item["text"],
                metadata={
                    "source": "my_api",
                    "author": item.get("author"),
                    "date": item.get("date")
                },
                modality="text"
            )
            documents.append(doc)
        
        return documents

# Use your connector
docs = ingest(connector=MyConnector, config={"url": "https://api.example.com/data"})
```

---

# 6. Layer 2: Chunking

## 6.1 Overview

The chunking layer splits documents into smaller, manageable pieces for embedding and retrieval.

### 6.1.1 Why Chunking?

- **Embedding Limits**: Models have token limits (e.g., 8192 tokens)
- **Retrieval Precision**: Smaller chunks = more relevant results
- **Context Windows**: LLMs work better with focused context
- **Performance**: Faster search over smaller units

### 6.1.2 Chunking Strategies

RAGBench provides 106 chunking methods across categories:

- **Fixed**: Character, token, sentence count
- **Semantic**: Meaning-based boundaries
- **Hierarchical**: Parent-child relationships
- **Structure-aware**: Markdown, HTML, JSON, code
- **Adaptive**: Dynamic sizing based on content

## 6.2 Basic Usage

### 6.2.1 Chunk Text

```python
from ragbench.chunking import chunk

text = """
Machine learning is a subset of artificial intelligence.
It enables systems to learn from data without explicit programming.
Neural networks are a popular machine learning technique.
"""

# Simple fixed-size chunking
chunks = chunk(text, method="Fixed Character Chunking", chunk_size=100)

for c in chunks:
    print(f"Chunk {c.id}: {c.content}")
    print(f"Metadata: {c.metadata}\n")
```

### 6.2.2 Chunk Documents

```python
from ragbench.ingestion import ingest
from ragbench.chunking import chunk

# Load documents
docs = ingest("report.pdf")

# Chunk them
chunks = chunk(docs, method="Sentence Chunking", chunk_size=500)

print(f"Created {len(chunks)} chunks from {len(docs)} documents")
```

### 6.2.3 Auto Mode

```python
# Let RAGBench choose the best method
chunks = chunk(text, method="auto")

# Or use the pipeline
from ragbench.pipeline import auto
chunks = auto("document.pdf")  # Ingest + chunk automatically
```

## 6.3 Chunking Methods

### 6.3.1 Fixed-Size Chunking

**Character-based:**
```python
chunks = chunk(
    text,
    method="Fixed Character Chunking",
    chunk_size=500,
    overlap=50
)
```

**Token-based:**
```python
chunks = chunk(
    text,
    method="Token Chunking",
    chunk_size=256,  # tokens
    overlap=32,
    encoding="cl100k_base"  # tiktoken encoding
)
```

**Sentence-based:**
```python
chunks = chunk(
    text,
    method="Sentence Chunking",
    chunk_size=5,  # sentences per chunk
    overlap=1
)
```

### 6.3.2 Semantic Chunking

```python
# Groups sentences by semantic similarity
chunks = chunk(
    text,
    method="Semantic Chunking",
    embedder="sentence-transformers",  # Optional embedder
    similarity_threshold=0.7,
    min_chunk_size=100,
    max_chunk_size=1000
)
```

**With custom embedder:**
```python
from ragbench.embedding import Embedder

embedder = Embedder("bge")
chunks = chunk(
    text,
    method="Semantic Chunking",
    embedder=embedder.as_callback(),  # Provide embedding function
    similarity_threshold=0.75
)
```

### 6.3.3 Recursive Chunking

```python
# Tries multiple separators (paragraph, sentence, word)
chunks = chunk(
    text,
    method="Recursive Character Chunking",
    chunk_size=1000,
    overlap=100,
    separators=["\n\n", "\n", ". ", " "]
)
```

### 6.3.4 Sliding Window

```python
chunks = chunk(
    text,
    method="Sliding Window Chunking",
    window_size=500,
    step_size=250  # 50% overlap
)
```

### 6.3.5 Structure-Aware Chunking

**Markdown:**
```python
markdown_text = """
# Introduction
This is the intro section.

## Background
Some background information.

### Details
More detailed content.
"""

chunks = chunk(
    markdown_text,
    method="Markdown Chunking",
    respect_headers=True,  # Keep headers with content
    max_chunk_size=500
)
```

**HTML:**
```python
html = "<html><body><p>Content...</p></body></html>"

chunks = chunk(
    html,
    method="HTML Chunking",
    tags_to_extract=["p", "div", "article"],
    preserve_structure=True
)
```

**JSON:**
```python
json_data = {
    "articles": [
        {"id": 1, "title": "...", "content": "..."},
        {"id": 2, "title": "...", "content": "..."}
    ]
}

chunks = chunk(
    json_data,
    method="JSON Chunking",
    content_fields=["content", "title"],
    max_depth=3
)
```

**Code:**
```python
python_code = """
def calculate_sum(a, b):
    '''Add two numbers'''
    return a + b

def calculate_product(a, b):
    '''Multiply two numbers'''
    return a * b
"""

chunks = chunk(
    python_code,
    method="Code Chunking",
    language="python",
    chunk_by="function",  # or "class", "method"
    include_docstrings=True
)
```

### 6.3.6 Hierarchical Chunking

```python
# Creates parent-child relationships
chunks = chunk(
    text,
    method="Hierarchical Chunking",
    parent_size=2000,
    child_size=500,
    overlap=50
)

# Access hierarchy
for chunk in chunks:
    if "parent_id" in chunk.metadata:
        print(f"Child chunk {chunk.id} -> Parent {chunk.metadata['parent_id']}")
```

### 6.3.7 Adaptive Chunking

```python
# Adjusts chunk size based on content density
chunks = chunk(
    text,
    method="Adaptive Chunking",
    target_size=500,
    min_size=100,
    max_size=1000,
    density_threshold=0.5
)
```

## 6.4 Advanced Configuration

### 6.4.1 Overlap Strategies

```python
# Fixed overlap
chunks = chunk(text, method="auto", chunk_size=500, overlap=50)

# Percentage overlap
chunks = chunk(text, method="auto", chunk_size=500, overlap_percentage=0.1)

# Sentence-aware overlap
chunks = chunk(
    text,
    method="Sentence Chunking",
    chunk_size=5,
    overlap=1,  # Overlap by 1 sentence
    overlap_mode="sentences"
)
```

### 6.4.2 Metadata Preservation

```python
from ragbench.ingestion import ingest
from ragbench.chunking import chunk

docs = ingest("report.pdf")

chunks = chunk(
    docs,
    method="Recursive Character Chunking",
    chunk_size=1000,
    preserve_metadata=True  # Keep document metadata
)

for c in chunks:
    print(f"Chunk from page {c.metadata.get('page')}")
    print(f"Source: {c.metadata.get('source')}")
```

### 6.4.3 Custom Boundaries

```python
# Define custom split points
chunks = chunk(
    text,
    method="Custom Boundary Chunking",
    boundaries=lambda text: [i for i, char in enumerate(text) if char == '\n\n'],
    min_size=100
)
```

### 6.4.4 Language-Specific Chunking

```python
# Use language-specific sentence tokenization
chunks = chunk(
    text,
    method="Sentence Chunking",
    language="french",  # or german, spanish, etc.
    chunk_size=5
)
```

## 6.5 Chunking for Different Content Types

### 6.5.1 Long Documents

```python
# Research papers, books
chunks = chunk(
    long_document,
    method="Hierarchical Chunking",
    parent_size=5000,  # Large parents for context
    child_size=1000,   # Smaller children for precision
    overlap=100
)
```

### 6.5.2 Code Documentation

```python
# API docs, code comments
chunks = chunk(
    code_docs,
    method="Code Chunking",
    language="python",
    chunk_by="function",
    include_signatures=True,
    include_docstrings=True
)
```

### 6.5.3 Conversational Data

```python
# Chat logs, support tickets
chunks = chunk(
    conversation,
    method="Conversation Chunking",
    group_by="speaker",  # or "topic", "timestamp"
    max_turns=10,
    preserve_context=True
)
```

### 6.5.4 Tabular Data

```python
import pandas as pd

df = pd.read_csv("data.csv")

chunks = chunk(
    df,
    method="Table Chunking",
    rows_per_chunk=100,
    include_headers=True,
    format="markdown"  # or "json", "text"
)
```

### 6.5.5 Multimedia

```python
# Video transcripts, audio transcriptions
chunks = chunk(
    transcript,
    method="Temporal Chunking",
    time_window=60,  # seconds
    include_timestamps=True
)
```

## 6.6 Evaluating Chunk Quality

### 6.6.1 Chunk Statistics

```python
from ragbench.chunking import chunk, analyze_chunks

chunks = chunk(text, method="auto", chunk_size=500)

stats = analyze_chunks(chunks)
print(stats)
# {
#     "count": 45,
#     "avg_length": 487,
#     "min_length": 250,
#     "max_length": 600,
#     "total_characters": 21915,
#     "avg_sentences": 3.2,
#     "overlap_ratio": 0.1
# }
```

### 6.6.2 Visualize Chunks

```python
from ragbench.chunking import visualize_chunks

chunks = chunk(text, method="Semantic Chunking")
visualize_chunks(chunks, highlight_overlap=True)
```

## 6.7 Best Practices

### 6.7.1 Choosing Chunk Size

**General guidelines:**
- **Small chunks (100-300 chars)**: High precision, more chunks, slower
- **Medium chunks (500-1000 chars)**: Balanced, recommended for most cases
- **Large chunks (1000-2000 chars)**: More context, fewer chunks, faster

**By use case:**
- Q&A: 500-800 characters
- Summarization: 1000-2000 characters
- Code search: Function-level (varies)
- Long documents: Hierarchical (parent: 2000, child: 500)

### 6.7.2 Overlap Considerations

- **No overlap (0%)**: Faster, but may split related content
- **Small overlap (10-20%)**: Recommended, captures context
- **Large overlap (>30%)**: Redundant, slower indexing

### 6.7.3 Method Selection

```python
# By content type
content_type_to_method = {
    "pdf": "Recursive Character Chunking",
    "markdown": "Markdown Chunking",
    "html": "HTML Chunking",
    "json": "JSON Chunking",
    "code": "Code Chunking",
    "conversation": "Conversation Chunking",
    "long_form": "Hierarchical Chunking"
}

method = content_type_to_method.get(file_type, "auto")
chunks = chunk(text, method=method)
```

## 6.8 Performance Optimization

### 6.8.1 Batch Chunking

```python
# Chunk multiple documents efficiently
from ragbench.chunking import Chunker

chunker = Chunker("Recursive Character Chunking", chunk_size=500)

# Batch process
all_chunks = []
for doc in large_document_list:
    chunks = chunker.chunk_document(doc)
    all_chunks.extend(chunks)
```

### 6.8.2 Parallel Chunking

```python
from concurrent.futures import ThreadPoolExecutor
from ragbench.chunking import chunk

def chunk_doc(doc):
    return chunk(doc, method="auto", chunk_size=500)

with ThreadPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(chunk_doc, documents))

all_chunks = [chunk for result in results for chunk in result]
```

### 6.8.3 Caching

```python
# Cache chunks for repeated processing
from ragbench.chunking import chunk, cache_chunks, load_cached_chunks

chunks = chunk(text, method="auto")
cache_chunks(chunks, "my_chunks.cache")

# Later...
chunks = load_cached_chunks("my_chunks.cache")
```

## 6.9 Custom Chunking Strategies

### 6.9.1 Simple Custom Chunker

```python
from ragbench.chunking import Chunk
from typing import List

def my_custom_chunker(text: str, delimiter: str = "\n\n") -> List[Chunk]:
    """Split on custom delimiter."""
    parts = text.split(delimiter)
    
    chunks = []
    for i, part in enumerate(parts):
        if part.strip():
            chunk = Chunk(
                id=f"chunk_{i}",
                document_id="doc_0",
                content=part.strip(),
                metadata={
                    "method": "custom",
                    "index": i
                }
            )
            chunks.append(chunk)
    
    return chunks

# Use it
chunks = my_custom_chunker(text, delimiter="---")
```

### 6.9.2 Advanced Custom Strategy

```python
from ragbench.chunking.strategies import ChunkingStrategy
from ragbench.chunking import Chunk

class MyAdvancedStrategy(ChunkingStrategy):
    """Custom chunking with advanced logic."""
    
    name = "My Advanced Strategy"
    group = "custom"
    
    def __init__(self, chunk_size=500, custom_param=None):
        self.chunk_size = chunk_size
        self.custom_param = custom_param
    
    def chunk(self, document) -> List[Chunk]:
        text = document.content if hasattr(document, 'content') else str(document)
        
        # Your chunking logic
        chunks = []
        
        # Example: chunk by paragraphs
        paragraphs = text.split('\n\n')
        
        for i, para in enumerate(paragraphs):
            if len(para) > self.chunk_size:
                # Split large paragraphs
                sub_chunks = self._split_large(para)
                chunks.extend(sub_chunks)
            else:
                chunk = Chunk(
                    id=f"chunk_{i}",
                    document_id=getattr(document, 'id', 'unknown'),
                    content=para,
                    metadata={
                        "method": self.name,
                        "index": i,
                        "custom_param": self.custom_param
                    }
                )
                chunks.append(chunk)
        
        return chunks
    
    def _split_large(self, text: str) -> List[Chunk]:
        """Split text that exceeds chunk_size."""
        # Implementation...
        pass

# Register and use
from ragbench.chunking import register_strategy
register_strategy(MyAdvancedStrategy)

chunks = chunk(text, method="My Advanced Strategy", custom_param="value")
```

---

# 7. Layer 3: Embedding

## 7.1 Overview

The embedding layer converts text into numerical vectors that capture semantic meaning.

### 7.1.1 What are Embeddings?

Embeddings are dense vector representations of text where:
- Similar meanings → Similar vectors
- Semantic relationships preserved
- Enables vector similarity search

Example:
```
"cat"     → [0.2, 0.8, 0.1, ...]
"kitten"  → [0.3, 0.7, 0.2, ...]  # Close to "cat"
"dog"     → [0.4, 0.6, 0.3, ...]  # Somewhat close
"car"     → [0.9, 0.1, 0.8, ...]  # Far from "cat"
```

### 7.1.2 Why Embeddings?

- **Semantic Search**: Find meaning, not just keywords
- **Similarity**: Measure relatedness between texts
- **Clustering**: Group similar documents
- **Classification**: Category prediction
- **Recommendation**: Find related content

### 7.1.3 Embedding Types

RAGBench supports:
- **Dense**: Single vector per text (most common)
- **Sparse**: Keyword-based (BM25, TF-IDF)
- **Multi-vector**: Multiple vectors per text (ColBERT)
- **Hybrid**: Combination of dense + sparse

## 7.2 Basic Usage

### 7.2.1 Embed Text

```python
from ragbench.embedding import embed

# Single text
vector = embed("Hello world", method="openai")
print(f"Dimension: {len(vector[0].vector)}")  # 1536

# Multiple texts
texts = ["First sentence", "Second sentence", "Third sentence"]
embeddings = embed(texts, method="openai")

for emb in embeddings:
    print(f"Text: {emb.content}")
    print(f"Vector: {emb.vector[:5]}...")  # First 5 dimensions
```

### 7.2.2 Embed Chunks

```python
from ragbench.chunking import chunk
from ragbench.embedding import embed

# Create chunks
chunks = chunk("Long document text...", method="auto")

# Embed them
embedded_chunks = embed(chunks, method="openai")

for emb in embedded_chunks:
    print(f"Chunk {emb.chunk_id}: {len(emb.vector)} dimensions")
```

### 7.2.3 Using the Embedder Class

```python
from ragbench.embedding import Embedder

# Create embedder
embedder = Embedder("openai", model="text-embedding-3-small")

# Check availability
if embedder.is_available():
    # Embed query
    query_vector = embedder.embed_query("What is machine learning?")
    
    # Embed documents
    doc_vectors = embedder.embed_documents([
        "ML is a subset of AI",
        "Neural networks are ML models"
    ])
    
    print(f"Query vector: {len(query_vector)} dims")
    print(f"Document vectors: {len(doc_vectors)}")
```

## 7.3 Available Embedding Methods

### 7.3.1 Complete Method List (62 methods)

**Baselines (offline):**
- `hash_baseline` - Deterministic hash (non-semantic)
- `bm25` - Sparse keyword matching
- `tfidf` - Term frequency weighting

**Classical Methods:**
- `word2vec` - Word embeddings
- `doc2vec` - Document embeddings
- `fasttext` - Subword embeddings
- `glove` - Global vectors

**Local Dense Models:**
- `bge` - BAAI BGE (best quality)
- `e5` - Microsoft E5
- `gte` - Alibaba GTE
- `minilm` - Fast, small model
- `sentence_transformers` - General purpose
- `arctic` - Snowflake Arctic
- `nomic` - Nomic Embed
- `hf_transformers` - Any HF model

**Multilingual:**
- `multilingual_e5` - 100+ languages

**Domain-Specific:**
- `biomedical` - PubMed/medical
- `finance` - Financial documents
- `legal` - Legal documents

**API Providers:**
- `openai` - text-embedding-3-small/large
- `cohere` - embed-v4.0
- `voyage` - voyage-3
- `jina` - jina-embeddings-v3
- `mistral` - mistral-embed
- `google_gemini` - gemini-embedding-001
- `bedrock` - AWS Bedrock models

**Self-Hosted:**
- `ollama` - Local Ollama server
- `llamacpp` - llama.cpp server
- `vllm` - vLLM inference server
- `tei` - Text Embeddings Inference

**Multi-Vector:**
- `colbert` - Late interaction
- `colpali` - Multi-vector for documents

**Multimodal:**
- `clip` - Text + images
- `siglip` - Text + images (Google)
- `clap` - Text + audio

**Sparse:**
- `splade` - Learned sparse

**Wrappers:**
- `l2_normalize` - Normalize vectors
- `pca_reduce` - Dimensionality reduction
- `quantize_binary` - Binary quantization
- `quantize_int8` - Int8 quantization
- `matryoshka` - Flexible dimensions

## 7.4 Method-Specific Usage

### 7.4.1 OpenAI

```python
import os
from ragbench.embedding import Embedder

# Set API key
os.environ["OPENAI_API_KEY"] = "sk-..."

# Create embedder
embedder = Embedder(
    "openai",
    model="text-embedding-3-small",  # or text-embedding-3-large
    dimensions=1536  # Optional: reduce dimensions
)

# Embed
vectors = embedder.embed(["Text 1", "Text 2"])

# Query vs document encoding
query_vec = embedder.embed_query("search query")
doc_vecs = embedder.embed_documents(["doc 1", "doc 2"])
```

**Models:**
- `text-embedding-3-small`: 1536 dims, fast, $0.02/1M tokens
- `text-embedding-3-large`: 3072 dims, best quality, $0.13/1M tokens
- `text-embedding-ada-002`: 1536 dims, legacy

### 7.4.2 Cohere

```python
os.environ["COHERE_API_KEY"] = "..."

embedder = Embedder(
    "cohere",
    model="embed-v4.0",  # or embed-english-v3.0, embed-multilingual-v3.0
    input_type="search_document"  # or "search_query", "classification"
)

vectors = embedder.embed(texts)
```

**Models:**
- `embed-v4.0`: Latest, best quality
- `embed-english-v3.0`: English only
- `embed-multilingual-v3.0`: 100+ languages

### 7.4.3 Voyage AI

```python
os.environ["VOYAGE_API_KEY"] = "..."

embedder = Embedder(
    "voyage",
    model="voyage-3"  # or voyage-3-lite, voyage-code-3
)

vectors = embedder.embed(texts)
```

**Models:**
- `voyage-3`: General purpose, 1024 dims
- `voyage-3-lite`: Faster, 512 dims
- `voyage-code-3`: Optimized for code

### 7.4.4 Local Models (BGE)

```bash
# Install dependencies
pip install "ragbench-ai[embed-local]"
```

```python
from ragbench.embedding import Embedder

# No API key needed!
embedder = Embedder(
    "bge",
    model="BAAI/bge-small-en-v1.5",  # or bge-base, bge-large
    device="cuda"  # or "cpu", "mps" (Mac)
)

vectors = embedder.embed(texts)
```

**Models:**
- `bge-small`: 384 dims, fast
- `bge-base`: 768 dims, balanced
- `bge-large`: 1024 dims, best quality

### 7.4.5 Sentence Transformers

```python
embedder = Embedder(
    "sentence_transformers",
    model="all-MiniLM-L6-v2"  # Or any sentence-transformers model
)

vectors = embedder.embed(texts)
```

**Popular models:**
- `all-MiniLM-L6-v2`: 384 dims, fast
- `all-mpnet-base-v2`: 768 dims, quality
- `paraphrase-multilingual-MiniLM-L12-v2`: Multilingual

### 7.4.6 Ollama

```bash
# Start Ollama
ollama serve

# Pull embedding model
ollama pull nomic-embed-text
```

```python
embedder = Embedder(
    "ollama",
    model="nomic-embed-text",  # or mxbai-embed-large
    base_url="http://localhost:11434"
)

vectors = embedder.embed(texts)
```

**Models:**
- `nomic-embed-text`: 768 dims, fast
- `mxbai-embed-large`: 1024 dims, quality
- `snowflake-arctic-embed`: 1024 dims

### 7.4.7 Jina AI

```python
os.environ["JINA_API_KEY"] = "..."

embedder = Embedder(
    "jina",
    model="jina-embeddings-v3",  # 1024 dims
    dimensions=512  # Optional: reduce
)

vectors = embedder.embed(texts)
```

### 7.4.8 Mistral

```python
os.environ["MISTRAL_API_KEY"] = "..."

embedder = Embedder(
    "mistral",
    model="mistral-embed"  # 1024 dims
)

vectors = embedder.embed(texts)
```

### 7.4.9 AWS Bedrock

```bash
# Install dependencies
pip install "ragbench-ai[embed-bedrock]"
```

```python
import os

os.environ["AWS_ACCESS_KEY_ID"] = "..."
os.environ["AWS_SECRET_ACCESS_KEY"] = "..."
os.environ["AWS_REGION"] = "us-east-1"

embedder = Embedder(
    "bedrock",
    model="amazon.titan-embed-text-v2:0"  # or cohere.embed-english-v3
)

vectors = embedder.embed(texts)
```

### 7.4.10 Google Gemini

```python
os.environ["GOOGLE_API_KEY"] = "..."

embedder = Embedder(
    "google_gemini",
    model="gemini-embedding-001"  # or text-embedding-004
)

vectors = embedder.embed(texts)
```

### 7.4.11 Sparse Embeddings (BM25)

```python
# No installation needed - pure Python
embedder = Embedder("bm25")

# Requires fitting on corpus
corpus = ["doc 1", "doc 2", "doc 3"]
embedder.fit(corpus)

# Get sparse vectors
sparse_vecs = embedder.embed(corpus)

for vec in sparse_vecs:
    print(f"Sparse: {vec.sparse}")  # {term_id: weight}
```

### 7.4.12 Multi-Vector (ColBERT)

```bash
pip install pylate
```

```python
embedder = Embedder(
    "colbert",
    model="answerdotai/answerai-colbert-small-v1"
)

# Returns multiple vectors per text
multi_vecs = embedder.embed(texts)

for vec in multi_vecs:
    print(f"Vectors: {len(vec.multi_vector)}")  # e.g., 32 vectors
```

### 7.4.13 Multimodal (CLIP)

```bash
pip install "ragbench-ai[embed-multimodal]"
```

```python
from PIL import Image

embedder = Embedder("clip", model="openai/clip-vit-base-patch32")

# Embed text
text_vec = embedder.embed("a photo of a cat")

# Embed image
image = Image.open("cat.jpg")
image_vec = embedder.embed(image)

# Compare
from numpy import dot
from numpy.linalg import norm
similarity = dot(text_vec, image_vec) / (norm(text_vec) * norm(image_vec))
print(f"Similarity: {similarity}")
```

## 7.5 Advanced Features

### 7.5.1 Query vs Document Encoding

Some models use different encodings for queries vs documents:

```python
embedder = Embedder("bge")

# Query encoding (for search)
query_vec = embedder.embed_query("What is machine learning?")

# Document encoding (for indexing)
doc_vecs = embedder.embed_documents([
    "ML is a subset of AI",
    "Neural networks learn from data"
])
```

### 7.5.2 Batch Processing

```python
embedder = Embedder("openai")

# Automatic batching for large lists
large_text_list = [f"Document {i}" for i in range(10000)]

# Processes in batches of 100 (configurable)
embeddings = embedder.embed(
    large_text_list,
    batch_size=100,
    show_progress=True
)
```

### 7.5.3 Caching

```python
# Enable disk caching
embedder = Embedder("openai", cache=True)

# First call: hits API
vec1 = embedder.embed("Hello world")  # ~100ms

# Second call: from cache
vec2 = embedder.embed("Hello world")  # <1ms
```

**Cache configuration:**
```python
import os

os.environ["OMNILOAD_CACHE_DIR"] = "~/.cache/ragbench"
os.environ["OMNILOAD_EMBED_CACHE"] = "1"  # Enable globally
```

### 7.5.4 Truncation & Padding

```python
embedder = Embedder(
    "openai",
    max_length=8192,  # Max tokens
    truncation=True,  # Truncate if too long
    padding=False      # Don't pad short texts
)

# Long text is automatically truncated
long_text = "word " * 20000
vec = embedder.embed(long_text)  # Truncated to 8192 tokens
```

### 7.5.5 Normalization

```python
# L2 normalization (for cosine similarity)
embedder = Embedder("bge", normalize=True)

vectors = embedder.embed(texts)

# Vectors are unit length
import numpy as np
for vec in vectors:
    norm = np.linalg.norm(vec.vector)
    print(f"Norm: {norm:.4f}")  # ~1.0000
```

### 7.5.6 Dimensionality Reduction

```python
# Reduce dimensions with PCA
from ragbench.embedding import Embedder

base_embedder = Embedder("bge")  # 768 dims

# Wrap with PCA
embedder = Embedder(
    "pca_reduce",
    base=base_embedder,
    dimensions=256  # Reduce to 256
)

# Fit on corpus
corpus = ["text 1", "text 2", ...]
embedder.fit(corpus)

# Embed with reduced dimensions
vecs = embedder.embed(corpus)  # 256 dims
```

### 7.5.7 Quantization

```python
# Binary quantization (768 -> 96 bytes)
embedder = Embedder(
    "quantize_binary",
    base="bge"
)

binary_vecs = embedder.embed(texts)

# Int8 quantization
embedder = Embedder(
    "quantize_int8",
    base="bge"
)

int8_vecs = embedder.embed(texts)
```

### 7.5.8 Matryoshka Embeddings

```python
# Flexible dimensions from a single model
embedder = Embedder(
    "nomic",
    model="nomic-ai/nomic-embed-text-v1.5",
    matryoshka_dim=256  # Use only first 256 dims
)

# Original: 768 dims, Using: 256 dims
vecs = embedder.embed(texts)
```

### 7.5.9 Ensemble Embeddings

```python
# Combine multiple embedders
embedder = Embedder(
    "ensemble_concat",
    bases=["bge", "e5", "gte"],
    weights=[0.4, 0.3, 0.3]
)

# Results in concatenated vector
vecs = embedder.embed(texts)  # (768+768+768) dims
```

## 7.6 Embedding for Specific Tasks

### 7.6.1 Semantic Search

```python
# Use asymmetric models (different query/doc encoding)
embedder = Embedder("bge")

# Index documents
doc_texts = ["doc 1", "doc 2", ...]
doc_vecs = embedder.embed_documents(doc_texts)

# Search
query = "user query"
query_vec = embedder.embed_query(query)

# Find most similar
from numpy import dot
scores = [dot(query_vec, doc_vec) for doc_vec in doc_vecs]
best_idx = max(range(len(scores)), key=lambda i: scores[i])
print(f"Best match: {doc_texts[best_idx]}")
```

### 7.6.2 Clustering

```python
# Use symmetric models
embedder = Embedder("sentence_transformers")

texts = ["text 1", "text 2", ...]
vecs = embedder.embed(texts)

# Cluster with k-means
from sklearn.cluster import KMeans
kmeans = KMeans(n_clusters=5)
labels = kmeans.fit_predict([v.vector for v in vecs])

for i, text in enumerate(texts):
    print(f"Cluster {labels[i]}: {text}")
```

### 7.6.3 Classification

```python
# Embed training data
embedder = Embedder("bge")

train_texts = [...]
train_labels = [...]
train_vecs = embedder.embed(train_texts)

# Train classifier
from sklearn.svm import SVC
clf = SVC()
clf.fit([v.vector for v in train_vecs], train_labels)

# Predict
test_text = "new text"
test_vec = embedder.embed(test_text)
prediction = clf.predict([test_vec[0].vector])
```

### 7.6.4 Duplicate Detection

```python
embedder = Embedder("bge", normalize=True)

texts = [...]
vecs = embedder.embed(texts)

# Find duplicates (cosine similarity > 0.95)
from numpy import dot

duplicates = []
for i in range(len(vecs)):
    for j in range(i+1, len(vecs)):
        sim = dot(vecs[i].vector, vecs[j].vector)
        if sim > 0.95:
            duplicates.append((i, j, sim))

for i, j, sim in duplicates:
    print(f"Duplicate: {i} ~ {j} (sim={sim:.3f})")
```

## 7.7 Performance & Cost Optimization

### 7.7.1 Choose the Right Model

**For speed:**
```python
# Fastest
embedder = Embedder("hash_baseline")  # Non-semantic

# Fast + semantic
embedder = Embedder("minilm")  # Local, 384 dims

# Fast + API
embedder = Embedder("openai", model="text-embedding-3-small")
```

**For quality:**
```python
# Best quality (local)
embedder = Embedder("bge", model="BAAI/bge-large-en-v1.5")

# Best quality (API)
embedder = Embedder("openai", model="text-embedding-3-large")
embedder = Embedder("voyage", model="voyage-3")
```

**For cost:**
```python
# Free (local)
embedder = Embedder("bge")  # No API costs

# Cheap (API)
embedder = Embedder("openai", model="text-embedding-3-small")  # $0.02/1M tokens
embedder = Embedder("jina")  # Free tier available
```

### 7.7.2 Reduce API Costs

```python
# Enable caching
embedder = Embedder("openai", cache=True)

# Reduce dimensions
embedder = Embedder(
    "openai",
    model="text-embedding-3-large",
    dimensions=512  # Down from 3072 (cheaper)
)

# Batch efficiently
embedder.embed(texts, batch_size=100)  # Process in larger batches
```

### 7.7.3 GPU Acceleration

```python
# Use GPU for local models
embedder = Embedder("bge", device="cuda")

# Mixed precision for speed
embedder = Embedder("bge", device="cuda", precision="fp16")

# Multi-GPU
embedder = Embedder("bge", device="cuda:0,1,2,3")
```

### 7.7.4 Memory Optimization

```python
# Use smaller models
embedder = Embedder("minilm")  # 80MB vs 1.5GB for bge-large

# Quantize
embedder = Embedder("quantize_int8", base="bge")

# Stream processing (don't load all in memory)
def embed_stream(texts, embedder):
    for text in texts:
        yield embedder.embed(text)[0]

for vec in embed_stream(huge_text_list, embedder):
    # Process one at a time
    store.add(vec)
```

## 7.8 Troubleshooting

### 7.8.1 "API key not found"

```python
import os

# Set environment variable
os.environ["OPENAI_API_KEY"] = "sk-..."

# Or use .env file
# OPENAI_API_KEY=sk-...

# Verify
embedder = Embedder("openai")
print(embedder.is_available())  # Should be True
```

### 7.8.2 "Model not found"

```bash
# For HuggingFace models
pip install transformers torch

# Download model
from transformers import AutoModel
AutoModel.from_pretrained("BAAI/bge-small-en-v1.5")

# For Ollama
ollama pull nomic-embed-text
```

### 7.8.3 "CUDA out of memory"

```python
# Use CPU
embedder = Embedder("bge", device="cpu")

# Use smaller model
embedder = Embedder("minilm")  # Instead of bge-large

# Reduce batch size
embedder.embed(texts, batch_size=8)  # Instead of 32
```

### 7.8.4 "Slow embedding"

```python
# Use GPU
embedder = Embedder("bge", device="cuda")

# Use API (faster for large batches)
embedder = Embedder("openai")

# Reduce dimensions
embedder = Embedder("bge", matryoshka_dim=256)
```

### 7.8.5 "Different vector lengths"

```python
# Ensure consistent dimensions
embedder = Embedder("openai", dimensions=1536)

# Or normalize
def normalize_dims(vectors, target_dim):
    from numpy import zeros
    normalized = []
    for vec in vectors:
        if len(vec) < target_dim:
            padded = zeros(target_dim)
            padded[:len(vec)] = vec
            normalized.append(padded)
        else:
            normalized.append(vec[:target_dim])
    return normalized
```

---

# 8. Layer 4: Vector Stores

## 8.1 Overview

The vector store layer indexes and retrieves embeddings efficiently.

### 8.1.1 What is a Vector Store?

A vector store (vector database) is specialized storage for:
- **High-dimensional vectors** (embeddings)
- **Similarity search** (find nearest neighbors)
- **Metadata filtering** (search with conditions)
- **Scalability** (billions of vectors)

### 8.1.2 Why Vector Stores?

Compared to traditional databases:
- ✅ **Fast similarity search**: O(log n) vs O(n)
- ✅ **Optimized for vectors**: ANN (Approximate Nearest Neighbors)
- ✅ **Metadata filters**: Combined vector + attribute search
- ✅ **Scalability**: Distributed, sharded storage

### 8.1.3 Available Stores (41 total)

RAGBench supports:
- **Local**: Memory, SQLite, DuckDB, FAISS, ChromaDB
- **Cloud**: Pinecone, Qdrant, Weaviate, Milvus
- **Databases**: PostgreSQL (pgvector), Redis, MongoDB
- **Search engines**: Elasticsearch, OpenSearch

## 8.2 Basic Usage

### 8.2.1 In-Memory Store

```python
from ragbench.vectorstore import VectorStore

# Create in-memory store
store = VectorStore("memory")

# Add vectors
from ragbench.embedding import embed

texts = ["First document", "Second document", "Third document"]
embeddings = embed(texts, method="hash_baseline")

result = store.add(embeddings, collection="docs")
print(f"Added {len(result.ids)} vectors")

# Search
query_vec = embed("First", method="hash_baseline")[0]
results = store.search(
    vector=query_vec.vector,
    k=2,
    collection="docs"
)

for r in results:
    print(f"Score: {r.score:.3f}, Content: {r.content}")
```

### 8.2.2 SQLite Store (Persistent)

```python
# Persistent local storage
store = VectorStore(
    "sqlite",
    path="./my_vectors.db"
)

# Add vectors
store.add(embeddings, collection="docs")

# Search
results = store.search(query_vec.vector, k=5, collection="docs")

# Vectors persist across sessions
store.close()

# Later...
store = VectorStore("sqlite", path="./my_vectors.db")
results = store.search(query_vec.vector, k=5, collection="docs")
```

### 8.2.3 Using the Pipeline

```python
from ragbench.pipeline import auto, ask

# Index automatically
result = auto(
    "document.pdf",
    embed=True,
    embedder="openai",
    index=True,
    store="sqlite",  # Choose store
    collection="my_docs"
)

# Query
answer = ask(
    "What is this about?",
    collection="my_docs",
    store="sqlite",
    embedder="openai"
)
```

## 8.3 Store-Specific Configuration

### 8.3.1 Memory Store

```python
# Fast, non-persistent
store = VectorStore("memory")

# Good for:
# - Testing
# - Small datasets
# - Temporary storage

# Limitations:
# - Lost when process ends
# - Limited to RAM size
```

### 8.3.2 SQLite Store

```bash
# Install extension
pip install "ragbench-ai[store-sqlite]"
```

```python
store = VectorStore(
    "sqlite",
    path="vectors.db",  # Database file
    dimension=1536,  # Vector dimension
)

# Good for:
# - Local persistent storage
# - Small to medium datasets (<1M vectors)
# - Single-process applications

# Limitations:
# - Not distributed
# - Slower than specialized stores
```

### 8.3.3 DuckDB Store

```bash
pip install "ragbench-ai[store-duckdb]"
```

```python
store = VectorStore(
    "duckdb",
    path="vectors.duckdb",
    dimension=1536
)

# Good for:
# - Analytical queries
# - Local storage
# - SQL integration

# Features:
# - Fast columnar storage
# - SQL queries
# - ACID transactions
```

### 8.3.4 FAISS Store

```bash
pip install "ragbench-ai[store-local]"
```

```python
store = VectorStore(
    "faiss",
    index_type="IVF256,Flat",  # Index type
    dimension=1536,
    metric="cosine"  # or "euclidean", "ip"
)

# Good for:
# - Millions of vectors
# - Local deployment
# - High performance

# Index types:
# - Flat: Exact search (small datasets)
# - IVF: Inverted file (medium datasets)
# - HNSW: Hierarchical graph (large datasets)
# - PQ: Product quantization (huge datasets)
```

### 8.3.5 ChromaDB Store

```bash
pip install "ragbench-ai[store-local]"
```

```python
store = VectorStore(
    "chroma",
    path="./chroma_db",  # Persistence directory
    distance_function="cosine"
)

# Good for:
# - Easy setup
# - Built-in embeddings
# - Metadata filtering

# Features:
# - SQLite backend
# - REST API
# - Simple interface
```

### 8.3.6 Pinecone Store

```bash
pip install "ragbench-ai[store-pinecone]"
```

```python
import os

os.environ["PINECONE_API_KEY"] = "..."

store = VectorStore(
    "pinecone",
    index_name="my-index",
    dimension=1536,
    metric="cosine",
    cloud="aws",  # or "gcp", "azure"
    region="us-east-1"
)

# Good for:
# - Production deployments
# - Billions of vectors
# - Global distribution

# Features:
# - Fully managed
# - Low latency
# - Automatic scaling
# - Real-time updates
```

### 8.3.7 Qdrant Store

```bash
pip install "ragbench-ai[store-qdrant]"
```

```python
store = VectorStore(
    "qdrant",
    url="http://localhost:6333",  # Or cloud URL
    api_key="...",  # For cloud
    collection_name="my_collection",
    dimension=1536,
    distance="Cosine"  # or "Euclid", "Dot"
)

# Good for:
# - Production deployments
# - Advanced filtering
# - High performance

# Features:
# - Rich metadata filtering
# - Payload indexing
# - Sharding & replication
# - HNSW + quantization
```

### 8.3.8 Weaviate Store

```bash
pip install "ragbench-ai[store-weaviate]"
```

```python
store = VectorStore(
    "weaviate",
    url="http://localhost:8080",  # Or cloud URL
    api_key="...",  # For cloud
    class_name="Document",
    vector_index_config={
        "ef_construction": 128,
        "max_connections": 64
    }
)

# Good for:
# - GraphQL queries
# - Semantic search
# - Multi-modal

# Features:
# - Built-in vectorization
# - Schema management
# - GraphQL API
# - Hybrid search
```

### 8.3.9 Milvus Store

```bash
pip install "ragbench-ai[store-milvus]"
```

```python
store = VectorStore(
    "milvus",
    host="localhost",
    port=19530,
    collection_name="documents",
    dimension=1536,
    index_type="IVF_FLAT",  # or "HNSW", "FLAT"
    metric_type="IP"  # or "L2", "COSINE"
)

# Good for:
# - Trillion-scale vectors
# - High throughput
# - Cloud-native

# Features:
# - Multiple index types
# - GPU acceleration
# - Distributed architecture
# - Time travel queries
```

### 8.3.10 PostgreSQL (pgvector)

```bash
pip install "ragbench-ai[store-postgres]"
```

```sql
-- Enable extension
CREATE EXTENSION vector;
```

```python
store = VectorStore(
    "pgvector",
    connection_string="postgresql://user:pass@localhost/db",
    table_name="vectors",
    dimension=1536
)

# Good for:
# - Existing PostgreSQL infrastructure
# - SQL integration
# - ACID transactions

# Features:
# - SQL queries
# - Joins with other tables
# - Triggers & constraints
# - Backup & replication
```

### 8.3.11 Redis Store

```bash
pip install "ragbench-ai[store-redis]"
```

```python
store = VectorStore(
    "redis",
    url="redis://localhost:6379",
    index_name="vectors",
    dimension=1536,
    algorithm="HNSW"  # or "FLAT"
)

# Good for:
# - Low-latency
# - Caching + vectors
# - Real-time apps

# Features:
# - Sub-millisecond latency
# - Hybrid queries
# - TTL support
# - Clustering
```

### 8.3.12 Elasticsearch Store

```bash
pip install "ragbench-ai[store-elastic]"
```

```python
store = VectorStore(
    "elasticsearch",
    url="http://localhost:9200",
    index_name="vectors",
    dimension=1536
)

# Good for:
# - Full-text + vector search
# - Existing ES infrastructure
# - Complex queries

# Features:
# - Hybrid search
# - Aggregations
# - Distributed
# - Analytics
```

## 8.4 Search Operations

### 8.4.1 Basic Search

```python
# Similarity search
results = store.search(
    vector=query_vector,
    k=10,  # Top 10 results
    collection="docs"
)

for r in results:
    print(f"{r.score:.3f}: {r.content}")
```

### 8.4.2 Filtered Search

```python
# Search with metadata filters
results = store.search(
    vector=query_vector,
    k=10,
    collection="docs",
    filter={
        "year": {"$gte": 2020},  # Year >= 2020
        "category": {"$in": ["tech", "science"]},  # Category in list
        "author": "John Doe"  # Exact match
    }
)
```

**Filter operators:**
- `$eq`: Equal
- `$ne`: Not equal
- `$gt`, `$gte`: Greater than (or equal)
- `$lt`, `$lte`: Less than (or equal)
- `$in`: In list
- `$nin`: Not in list
- `$and`, `$or`, `$not`: Logical operators

### 8.4.3 Namespace Search

```python
# Search within namespace
results = store.search(
    vector=query_vector,
    k=10,
    collection="docs",
    namespace="user_123"  # User-specific data
)
```

### 8.4.4 Hybrid Search

```python
# Combined vector + keyword search
results = store.search(
    vector=query_vector,  # Semantic
    text="machine learning",  # Keywords
    k=10,
    collection="docs",
    hybrid_weight=0.5  # Balance (0=keywords, 1=vector)
)
```

### 8.4.5 Multi-Collection Search

```python
# Search across multiple collections
results = store.search(
    vector=query_vector,
    k=10,
    collections=["docs", "articles", "papers"]
)
```

## 8.5 CRUD Operations

### 8.5.1 Add Vectors

```python
from ragbench.vectorstore import VectorRecord

# Add single vector
record = VectorRecord(
    id="vec_1",
    vector=[0.1, 0.2, ...],
    content="Document text",
    metadata={"author": "John", "year": 2024}
)

result = store.add([record], collection="docs")
print(f"Added: {result.ids}")
```

### 8.5.2 Update Vectors

```python
# Update existing vector
updated_record = VectorRecord(
    id="vec_1",
    vector=[0.15, 0.25, ...],  # New vector
    content="Updated text",
    metadata={"author": "John", "year": 2024, "updated": True}
)

store.update([updated_record], collection="docs")
```

### 8.5.3 Delete Vectors

```python
# Delete by ID
store.delete(ids=["vec_1", "vec_2"], collection="docs")

# Delete by filter
store.delete(
    filter={"year": {"$lt": 2020}},  # Delete old documents
    collection="docs"
)

# Delete entire collection
store.delete_collection("docs")
```

### 8.5.4 Get Vectors

```python
# Get by ID
vectors = store.get(ids=["vec_1", "vec_2"], collection="docs")

for v in vectors:
    print(f"{v.id}: {v.content}")
```

### 8.5.5 List Collections

```python
# List all collections
collections = store.list_collections()
print(f"Collections: {collections}")
```

### 8.5.6 Collection Info

```python
# Get collection statistics
info = store.collection_info("docs")
print(f"Vectors: {info.count}")
print(f"Dimension: {info.dimension}")
print(f"Index type: {info.index_type}")
```

## 8.6 Advanced Features

### 8.6.1 Batch Operations

```python
# Add in batches
large_vector_list = [...]  # 10,000 vectors

batch_size = 1000
for i in range(0, len(large_vector_list), batch_size):
    batch = large_vector_list[i:i+batch_size]
    store.add(batch, collection="docs")
    print(f"Added batch {i//batch_size + 1}")
```

### 8.6.2 Sparse Vectors

```python
# Add sparse vectors (e.g., BM25)
sparse_record = VectorRecord(
    id="vec_1",
    sparse={  # Sparse representation
        125: 0.45,  # term_id: weight
        389: 0.82,
        1024: 0.33
    },
    content="Document text",
    metadata={}
)

store.add([sparse_record], collection="docs")

# Search with sparse query
results = store.search(
    sparse={125: 0.5, 389: 0.9},
    k=10,
    collection="docs"
)
```

### 8.6.3 Multi-Vector Storage

```python
# Add multi-vector embeddings (e.g., ColBERT)
multi_vec_record = VectorRecord(
    id="vec_1",
    multi_vector=[  # List of vectors
        [0.1, 0.2, ...],  # Token 1
        [0.3, 0.4, ...],  # Token 2
        [0.5, 0.6, ...]   # Token 3
    ],
    content="Document text",
    metadata={}
)

store.add([multi_vec_record], collection="docs")
```

### 8.6.4 Upsert Operation

```python
# Add or update (upsert)
store.upsert([record], collection="docs")  # Creates if missing, updates if exists
```

### 8.6.5 Index Optimization

```python
# Rebuild index for better performance
store.optimize_collection("docs")

# Vacuum (reclaim space)
store.vacuum("docs")
```

### 8.6.6 Backup & Restore

```python
# Export collection
store.export_collection("docs", "backup.json")

# Import collection
store.import_collection("docs", "backup.json")
```

## 8.7 Performance Tuning

### 8.7.1 Index Configuration

```python
# FAISS index types
# For exact search (<10K vectors)
store = VectorStore("faiss", index_type="Flat")

# For approximate search (10K-1M vectors)
store = VectorStore("faiss", index_type="IVF1024,Flat")

# For large datasets (1M+ vectors)
store = VectorStore("faiss", index_type="IVF4096,PQ64")

# For fastest search (with graph)
store = VectorStore("faiss", index_type="HNSW32")
```

### 8.7.2 Batch Size Tuning

```python
# Larger batches = faster indexing
store.add(vectors, collection="docs", batch_size=1000)

# Smaller batches = lower memory
store.add(vectors, collection="docs", batch_size=100)
```

### 8.7.3 Connection Pooling

```python
# PostgreSQL with connection pool
store = VectorStore(
    "pgvector",
    connection_string="postgresql://...",
    pool_size=20,  # Connection pool
    max_overflow=10
)
```

### 8.7.4 Caching

```python
# Enable query caching
store = VectorStore("qdrant", url="...", cache_queries=True)

# First query: hits database
results1 = store.search(query_vec, k=10)  # ~50ms

# Same query: from cache
results2 = store.search(query_vec, k=10)  # ~1ms
```

## 8.8 Choosing a Vector Store

### 8.8.1 Decision Matrix

| Use Case | Recommended Store | Why |
|----------|------------------|-----|
| Testing/Development | Memory, SQLite | Fast setup, no config |
| Small production (<100K) | SQLite, DuckDB | Simple, persistent |
| Medium production (100K-10M) | Qdrant, Weaviate | Good balance |
| Large production (10M+) | Pinecone, Milvus | Scalable |
| Existing PostgreSQL | pgvector | SQL integration |
| Existing Elasticsearch | Elasticsearch | Hybrid search |
| Low latency | Redis, Milvus | Fast search |
| Cost-sensitive | Self-hosted (Qdrant, Weaviate) | No per-query costs |
| Managed service | Pinecone, Weaviate Cloud | Zero ops |

### 8.8.2 Feature Comparison

| Store | Managed | Distributed | Filters | Hybrid | Cost |
|-------|---------|-------------|---------|--------|------|
| Memory | ❌ | ❌ | ⚠️ | ❌ | Free |
| SQLite | ❌ | ❌ | ✅ | ❌ | Free |
| FAISS | ❌ | ❌ | ⚠️ | ❌ | Free |
| ChromaDB | ❌ | ❌ | ✅ | ⚠️ | Free |
| Qdrant | ✅ | ✅ | ✅ | ✅ | $$ |
| Pinecone | ✅ | ✅ | ✅ | ⚠️ | $$$ |
| Weaviate | ✅ | ✅ | ✅ | ✅ | $$ |
| Milvus | ✅ | ✅ | ✅ | ✅ | $$ |
| pgvector | ❌ | ⚠️ | ✅ | ❌ | Free/$ |
| Elasticsearch | ✅ | ✅ | ✅ | ✅ | $$$ |

## 8.9 Troubleshooting

### 8.9.1 "Connection refused"

```python
# Check if service is running
# For Qdrant:
# docker run -p 6333:6333 qdrant/qdrant

# For Weaviate:
# docker run -p 8080:8080 semitechnologies/weaviate

# Verify connection
import requests
response = requests.get("http://localhost:6333/health")
print(response.status_code)  # Should be 200
```

### 8.9.2 "Dimension mismatch"

```python
# Ensure consistent dimensions
embedder = Embedder("openai", dimensions=1536)
store = VectorStore("qdrant", dimension=1536)  # Must match

# Check existing collection
info = store.collection_info("docs")
print(f"Collection dimension: {info.dimension}")
```

### 8.9.3 "Slow search"

```python
# Use approximate index (not Flat)
store = VectorStore("faiss", index_type="HNSW32")  # Not "Flat"

# Reduce k
results = store.search(vec, k=10)  # Not k=1000

# Add index to filters
# PostgreSQL
CREATE INDEX idx_metadata ON vectors ((metadata->>'year'));
```

### 8.9.4 "Out of memory"

```python
# Use disk-based store
store = VectorStore("sqlite", path="vectors.db")  # Not "memory"

# Process in batches
for batch in batches:
    store.add(batch, collection="docs")

# Use quantization
store = VectorStore("faiss", index_type="IVF1024,PQ32")  # Compressed
```

---

*This guide continues with detailed sections on Retrieval, Reranking, Generation, Pipeline, Workflows, Advanced Topics, Configuration, Troubleshooting, Best Practices, Contributing, API Reference, and Appendices...*

**[Due to length limits, I'll continue in the next response]**

Would you like me to continue with the remaining sections (Layers 5-7, workflows, advanced topics, etc.)?


# 9. Layer 5: Retrieval

## 9.1 Overview

The retrieval layer finds the most relevant chunks for a given query.

### 9.1.1 Retrieval Methods (28 total)

RAGBench provides diverse retrieval strategies:

- **Dense**: Vector similarity search
- **Sparse**: Keyword matching (BM25, TF-IDF)
- **Hybrid**: Combined dense + sparse
- **Fusion**: Merge multiple retrieval results
- **Diversity**: MMR, max-sum diversification
- **Query Transforms**: Rewrite, HyDE, multi-query
- **Expansion**: Parent-child, sentence window
- **Routing**: Auto-select best method

## 9.2 Basic Usage

### 9.2.1 Simple Retrieval

```python
from ragbench.retrieval import retrieve

# BM25 (keyword-based, no embeddings needed)
results = retrieve(
    query="What is machine learning?",
    corpus=chunks,  # List of chunks/documents
    method="bm25",
    k=5
)

for r in results:
    print(f"Score: {r.score:.3f}")
    print(f"Content: {r.content}\n")
```

### 9.2.2 Dense Retrieval

```python
from ragbench.embedding import Embedder
from ragbench.vectorstore import VectorStore
from ragbench.retrieval import retrieve

# Setup
embedder = Embedder("openai")
store = VectorStore("qdrant", url="...")

# Dense search
results = retrieve(
    query="What is machine learning?",
    method="dense",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10
)
```

### 9.2.3 Using Retriever Class

```python
from ragbench.retrieval import Retriever

# Create retriever
retriever = Retriever(
    method="hybrid_rrf",  # Hybrid search with RRF fusion
    embedder=embedder,
    store=store
)

# Retrieve
results = retriever.retrieve(
    query="quantum computing applications",
    collection="docs",
    k=20
)

# Get detailed report
report = retriever.retrieve_report(
    query="quantum computing applications",
    collection="docs",
    k=20
)

print(f"Method: {report.method}")
print(f"Results: {len(report.results)}")
print(f"Duration: {report.duration_s:.2f}s")
print(f"Plan: {report.plan}")
print(f"Stats: {report.stats}")
```

## 9.3 Retrieval Methods

### 9.3.1 Dense Retrieval

```python
# Pure vector similarity
results = retrieve(
    query="quantum computing",
    method="dense",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10
)
```

**Best for:**
- Semantic search
- Questions
- Conceptual queries

**Limitations:**
- Requires embeddings
- May miss exact keywords

### 9.3.2 Sparse Retrieval (BM25)

```python
# Keyword-based search
results = retrieve(
    query="quantum computing",
    method="bm25",
    corpus=chunks,
    k=10
)

# With BM25 parameters
results = retrieve(
    query="quantum computing",
    method="bm25",
    corpus=chunks,
    k=10,
    bm25_k1=1.5,  # Term frequency saturation
    bm25_b=0.75   # Document length normalization
)
```

**Best for:**
- Keyword search
- Technical terms
- Identifiers (IDs, codes)
- Short queries

**Advantages:**
- No embeddings needed
- Fast
- Exact keyword matching

### 9.3.3 Hybrid Retrieval

```python
# Combine dense + sparse
results = retrieve(
    query="quantum computing applications",
    method="hybrid_rrf",  # Reciprocal Rank Fusion
    embedder=embedder,
    store=store,
    collection="docs",
    k=20,
    dense_weight=0.5,  # Balance (0=sparse, 1=dense)
    sparse_weight=0.5
)

# Other hybrid methods
results = retrieve(query, method="hybrid_weighted", ...)  # Weighted combination
results = retrieve(query, method="hybrid_rerank", ...)   # Rerank after fusion
```

**Best for:**
- General-purpose search
- Combines semantic + keyword
- Robust across query types

**Types:**
- `hybrid_rrf`: Reciprocal Rank Fusion (recommended)
- `hybrid_weighted`: Score-based weighting
- `hybrid_rerank`: Fusion + reranking

### 9.3.4 Multi-Query Retrieval

```python
# Generate multiple query variations
results = retrieve(
    query="How do transformers work?",
    method="multi_query",
    embedder=embedder,
    store=store,
    collection="docs",
    k=5,
    num_queries=3,  # Generate 3 variations
    llm=my_llm_callback  # Optional: LLM for query generation
)

# Queries generated:
# 1. "How do transformers work?"
# 2. "What is the mechanism of transformer models?"
# 3. "Explain transformer architecture"
```

**Best for:**
- Complex questions
- Incomplete queries
- Improving recall

### 9.3.5 HyDE (Hypothetical Document Embeddings)

```python
# Generate hypothetical answer, then search for it
results = retrieve(
    query="What causes climate change?",
    method="hyde",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    llm=my_llm_callback  # Required for HyDE
)

# HyDE process:
# 1. LLM generates hypothetical answer
# 2. Embed the answer
# 3. Search for similar documents
```

**Best for:**
- Detailed questions
- When answer format is known
- Improving relevance

### 9.3.6 Query Rewrite

```python
# Rewrite query for better results
results = retrieve(
    query="transformers work how",  # Poorly formed
    method="query_rewrite",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    llm=my_llm_callback  # Rewrites query
)

# Rewritten: "How do transformers work?"
```

**Best for:**
- User queries (often poorly formed)
- Conversational search
- Improving precision

### 9.3.7 Step-Back Prompting

```python
# Generate broader query, then search
results = retrieve(
    query="How does GPT-4 handle long contexts?",
    method="step_back",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    llm=my_llm_callback
)

# Step-back query: "What are the context handling techniques in LLMs?"
```

**Best for:**
- Specific questions needing background
- Complex topics
- Educational content

### 9.3.8 Query Decomposition

```python
# Break complex query into sub-queries
results = retrieve(
    query="Compare supervised and unsupervised learning",
    method="decomposition",
    embedder=embedder,
    store=store,
    collection="docs",
    k=5,
    llm=my_llm_callback
)

# Sub-queries:
# 1. "What is supervised learning?"
# 2. "What is unsupervised learning?"
# 3. "Key differences between supervised and unsupervised learning"
```

**Best for:**
- Multi-part questions
- Comparisons
- Complex information needs

### 9.3.9 Parent-Child Retrieval

```python
# Retrieve child chunks, return parent documents
results = retrieve(
    query="quantum entanglement",
    method="parent_child",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10
)

# Process:
# 1. Search with child chunks (small, precise)
# 2. Return parent chunks (larger context)
```

**Best for:**
- Hierarchical documents
- Need context around hits
- Long documents

### 9.3.10 Sentence Window

```python
# Retrieve sentence, expand to surrounding context
results = retrieve(
    query="quantum superposition",
    method="sentence_window",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    window_size=3  # Include 3 sentences before/after
)
```

**Best for:**
- Precise matching
- Contextual expansion
- Short documents

### 9.3.11 MMR (Maximal Marginal Relevance)

```python
# Diversify results (avoid redundancy)
results = retrieve(
    query="machine learning",
    method="mmr",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    lambda_param=0.5  # 0=pure diversity, 1=pure relevance
)
```

**Best for:**
- Diverse results
- Avoiding redundancy
- Exploratory search

### 9.3.12 Self-Query

```python
# Extract filters from natural language
results = retrieve(
    query="Python books published after 2020",
    method="self_query",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    llm=my_llm_callback
)

# Extracted filter: {"language": "Python", "year": {"$gt": 2020}}
```

**Best for:**
- Natural language filters
- Structured metadata
- Attribute-based search

### 9.3.13 Multi-Collection Search

```python
# Search across multiple collections
results = retrieve(
    query="quantum computing",
    method="multi_collection",
    embedder=embedder,
    store=store,
    collections=["papers", "books", "articles"],
    k=10
)

# With fusion
results = retrieve(
    query="quantum computing",
    method="multi_collection_rrf",  # Fuse results
    embedder=embedder,
    store=store,
    collections=["papers", "books", "articles"],
    k=10
)
```

**Best for:**
- Searching multiple sources
- Aggregating results
- Comprehensive search

## 9.4 Advanced Configuration

### 9.4.1 Metadata Filtering

```python
results = retrieve(
    query="machine learning",
    method="dense",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    filter={
        "year": {"$gte": 2020},
        "category": {"$in": ["AI", "ML"]},
        "author": "John Doe"
    }
)
```

**Filter operators:**
- `$eq`, `$ne`: Equal, not equal
- `$gt`, `$gte`, `$lt`, `$lte`: Comparisons
- `$in`, `$nin`: In list, not in list
- `$and`, `$or`, `$not`: Logical operators
- `$exists`: Field exists
- `$regex`: Pattern matching

### 9.4.2 Namespace Filtering

```python
# Search within specific namespace
results = retrieve(
    query="machine learning",
    method="dense",
    embedder=embedder,
    store=store,
    collection="docs",
    namespace="user_123",  # User-specific data
    k=10
)
```

### 9.4.3 Score Thresholding

```python
# Only return results above threshold
results = retrieve(
    query="quantum computing",
    method="dense",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10,
    min_score=0.7  # Filter low-relevance results
)
```

### 9.4.4 Fusion Strategies

```python
# Reciprocal Rank Fusion (RRF)
results = retrieve(query, method="rrf_fusion", k=10)

# Weighted fusion
results = retrieve(
    query,
    method="weighted_fusion",
    weights=[0.4, 0.3, 0.3],  # Weights for each retriever
    k=10
)

# Max score
results = retrieve(
    query,
    method="weighted_fusion",
    strategy="max",  # Take max score
    k=10
)

# Interleave
results = retrieve(
    query,
    method="weighted_fusion",
    strategy="interleave",  # Round-robin from each
    k=10
)
```

## 9.5 Auto-Routing

```python
# Let RAGBench choose the best method
results = retrieve(
    query=user_query,
    method="auto",  # Automatic routing
    embedder=embedder,
    store=store,
    collection="docs",
    k=10
)

# Routing logic:
# - Multiple collections → multi_collection_rrf
# - Has filter → filtered
# - Filter in text → self_query
# - Multi-aspect → multi_query
# - Code/IDs → sparse
# - Short keyword → sparse
# - Question → hybrid or dense
# - No embedder → hash_baseline (offline)
```

## 9.6 Retrieval Pipeline

### 9.6.1 Full Pipeline

```python
from ragbench.retrieval import Retriever

retriever = Retriever(
    method="hybrid_rrf",
    embedder=embedder,
    store=store
)

# Query transform → Retrieve → Diversify → Filter → Expand
report = retriever.retrieve_report(
    query="quantum computing",
    collection="docs",
    k=20,
    # Query transform
    query_transform="multi_query",
    num_queries=3,
    # Diversity
    diversify=True,
    lambda_param=0.5,
    # Filter
    filter={"year": {"$gte": 2020}},
    min_score=0.6,
    # Expansion
    expand="sentence_window",
    window_size=2
)

print(f"Pipeline plan: {report.plan}")
print(f"Results: {len(report.results)}")
```

### 9.6.2 Custom Pipeline

```python
from ragbench.retrieval import (
    retrieve,
    apply_diversity,
    expand_context
)

# Step 1: Initial retrieval
results = retrieve(
    query="quantum computing",
    method="hybrid_rrf",
    embedder=embedder,
    store=store,
    collection="docs",
    k=50  # Over-retrieve
)

# Step 2: Apply diversity
diverse_results = apply_diversity(
    results,
    method="mmr",
    lambda_param=0.5,
    k=20  # Keep top 20 diverse
)

# Step 3: Expand context
expanded_results = expand_context(
    diverse_results,
    method="sentence_window",
    store=store,
    window_size=2
)

# Step 4: Final filtering
final_results = [r for r in expanded_results if r.score > 0.7]
```

## 9.7 Performance Optimization

### 9.7.1 Retrieval Speed

```python
# Fast: BM25 (no embeddings)
results = retrieve(query, method="bm25", corpus=chunks, k=10)

# Medium: Dense search
results = retrieve(query, method="dense", embedder=embedder, store=store, k=10)

# Slow: Multi-query with LLM
results = retrieve(
    query,
    method="multi_query",
    num_queries=5,
    llm=llm_callback,
    k=10
)
```

### 9.7.2 Reduce Over-Retrieval

```python
# Bad: Retrieve too many, then filter
results = retrieve(query, k=1000)  # Too many
filtered = [r for r in results if r.score > 0.7]

# Good: Use k wisely
results = retrieve(
    query,
    k=20,  # Reasonable amount
    min_score=0.7  # Filter at retrieval time
)
```

### 9.7.3 Caching

```python
# Cache retrieval results
from functools import lru_cache

@lru_cache(maxsize=1000)
def cached_retrieve(query, k):
    return retrieve(
        query=query,
        method="dense",
        embedder=embedder,
        store=store,
        k=k
    )

# First call: ~50ms
results1 = cached_retrieve("quantum computing", 10)

# Second call (same query): <1ms
results2 = cached_retrieve("quantum computing", 10)
```

### 9.7.4 Parallel Retrieval

```python
from concurrent.futures import ThreadPoolExecutor

queries = ["query 1", "query 2", "query 3"]

def retrieve_one(query):
    return retrieve(query, method="dense", embedder=embedder, store=store, k=10)

# Parallel execution
with ThreadPoolExecutor(max_workers=3) as executor:
    results = list(executor.map(retrieve_one, queries))
```

## 9.8 Evaluation & Debugging

### 9.8.1 Retrieval Report

```python
report = retriever.retrieve_report(query, collection="docs", k=10)

print(f"Method: {report.method}")
print(f"Query: {report.query}")
print(f"Results: {len(report.results)}")
print(f"Duration: {report.duration_s:.2f}s")

# Routing plan
print(f"\nPlan:")
print(f"  Method: {report.plan.method}")
print(f"  Query type: {report.plan.query_type}")
print(f"  Reason: {report.plan.reason}")
print(f"  Transforms: {report.plan.transforms}")
print(f"  Expansion: {report.plan.expansion}")

# Statistics
print(f"\nStats:")
for key, value in report.stats.items():
    print(f"  {key}: {value}")

# Errors (if any)
if report.errors:
    print(f"\nErrors:")
    for error in report.errors:
        print(f"  {error}")
```

### 9.8.2 Analyze Results

```python
from ragbench.retrieval import analyze_results

results = retrieve(query, method="dense", embedder=embedder, store=store, k=10)

analysis = analyze_results(results)

print(f"Average score: {analysis['avg_score']:.3f}")
print(f"Score range: {analysis['min_score']:.3f} - {analysis['max_score']:.3f}")
print(f"Unique documents: {analysis['unique_docs']}")
print(f"Duplicate chunks: {analysis['duplicates']}")
```

### 9.8.3 Compare Methods

```python
# Compare different retrieval methods
methods = ["bm25", "dense", "hybrid_rrf"]

for method in methods:
    results = retrieve(
        query="quantum computing",
        method=method,
        embedder=embedder,
        store=store,
        collection="docs",
        k=10
    )
    
    print(f"\n{method.upper()}:")
    print(f"  Top score: {results[0].score:.3f}")
    print(f"  Avg score: {sum(r.score for r in results) / len(results):.3f}")
    print(f"  Top result: {results[0].content[:100]}...")
```

## 9.9 Best Practices

### 9.9.1 Method Selection

```python
# General guidelines
use_cases = {
    "semantic_search": "hybrid_rrf",  # Best all-around
    "keyword_search": "bm25",         # Fast, keyword-focused
    "code_search": "sparse",          # Exact matching
    "qa_system": "multi_query",       # Better recall
    "diverse_results": "mmr",         # Avoid redundancy
    "hierarchical_docs": "parent_child",  # Context preservation
}

method = use_cases.get(my_use_case, "hybrid_rrf")
results = retrieve(query, method=method, ...)
```

### 9.9.2 K Selection

```python
# Too small: Miss relevant results
results = retrieve(query, k=3)  # Not enough

# Too large: Slow, irrelevant results
results = retrieve(query, k=1000)  # Too many

# Recommended:
# - QA: k=5-10
# - Summarization: k=10-20
# - Research: k=20-50
results = retrieve(query, k=10)  # Balanced
```

### 9.9.3 Filtering Best Practices

```python
# Pre-filter (fast)
results = retrieve(
    query,
    method="dense",
    filter={"year": {"$gte": 2020}},  # Filter at retrieval
    k=10
)

# Post-filter (flexible but slower)
all_results = retrieve(query, method="dense", k=100)
filtered = [r for r in all_results if meets_criteria(r)]
```

---

# 10. Layer 6: Reranking

## 10.1 Overview

Reranking refines retrieval results by reordering them based on deeper relevance analysis.

### 10.1.1 Why Rerank?

Initial retrieval (Layer 5) is fast but may not be perfect. Reranking:
- ✅ **Improves precision**: Better ranking of top results
- ✅ **Cross-encoder models**: More accurate than bi-encoders
- ✅ **Domain-specific**: Use specialized models
- ✅ **Hybrid signals**: Combine multiple relevance indicators

### 10.1.2 Available Methods (21 total)

- **BM25**: Keyword-based reranking
- **Cross-encoders**: BGE, ColBERT, MiniLM, etc.
- **API-based**: Cohere, Voyage, Jina
- **Learning-to-rank**: LambdaMART, RankNet
- **Diversity**: MMR reranking
- **Custom**: Rule-based, ML models

## 10.2 Basic Usage

### 10.2.1 Simple Reranking

```python
from ragbench.retrieval import retrieve
from ragbench.reranking import rerank

# Retrieve
results = retrieve(
    query="quantum computing",
    method="dense",
    embedder=embedder,
    store=store,
    k=50  # Over-retrieve
)

# Rerank
reranked = rerank(
    query="quantum computing",
    results=results,
    method="bm25",  # Simple, fast
    k=10  # Keep top 10
)

for r in reranked:
    print(f"{r.score:.3f}: {r.content[:100]}...")
```

### 10.2.2 Using Reranker Class

```python
from ragbench.reranking import Reranker

# Create reranker
reranker = Reranker(
    method="cohere",  # API-based
    model="rerank-english-v3.0"
)

# Rerank
reranked = reranker.rerank(
    query="What is quantum entanglement?",
    results=results,
    k=5
)
```

### 10.2.3 Integrated with Retrieval

```python
from ragbench.retrieval import Retriever

# Retriever with built-in reranking
retriever = Retriever(
    method="hybrid_rrf",
    embedder=embedder,
    store=store,
    reranker=reranker  # Automatic reranking
)

# Retrieve and rerank in one call
results = retriever.retrieve(
    query="quantum entanglement",
    collection="docs",
    k=20,  # Initial retrieval
    rerank=True,
    rerank_k=5  # Final count
)
```

## 10.3 Reranking Methods

### 10.3.1 BM25 Reranking

```python
# Fast, keyword-based
reranked = rerank(
    query="machine learning algorithms",
    results=results,
    method="bm25",
    k=10
)
```

**Best for:**
- Fast reranking
- Keyword queries
- No API needed

### 10.3.2 Cohere Reranking

```python
import os
os.environ["COHERE_API_KEY"] = "..."

reranker = Reranker(
    method="cohere",
    model="rerank-english-v3.0"  # or rerank-multilingual-v3.0
)

reranked = reranker.rerank(
    query="explain quantum superposition",
    results=results,
    k=10,
    max_chunks_per_doc=5  # Cohere-specific
)
```

**Best for:**
- High accuracy
- Production systems
- Easy integration

**Models:**
- `rerank-english-v3.0`: English only, best quality
- `rerank-multilingual-v3.0`: 100+ languages
- `rerank-english-v2.0`: Legacy model

### 10.3.3 BGE Reranker

```bash
pip install "ragbench-ai[rerank-local]"
```

```python
reranker = Reranker(
    method="bge_reranker",
    model="BAAI/bge-reranker-base"  # or bge-reranker-large
)

reranked = reranker.rerank(
    query="quantum computing applications",
    results=results,
    k=10
)
```

**Best for:**
- Local deployment
- No API costs
- Privacy-sensitive

**Models:**
- `bge-reranker-base`: 278M params, balanced
- `bge-reranker-large`: 560M params, best quality
- `bge-reranker-v2-m3`: Multilingual

### 10.3.4 MiniLM Reranker

```python
reranker = Reranker(
    method="minilm",
    model="cross-encoder/ms-marco-MiniLM-L-6-v2"
)

reranked = reranker.rerank(query, results, k=10)
```

**Best for:**
- Fast local reranking
- Limited resources
- Real-time applications

### 10.3.5 ColBERT Reranking

```python
reranker = Reranker(
    method="colbert_reranker",
    model="colbert-ir/colbertv2.0"
)

reranked = reranker.rerank(query, results, k=10)
```

**Best for:**
- High accuracy
- Late interaction
- Research applications

### 10.3.6 Voyage Reranking

```python
os.environ["VOYAGE_API_KEY"] = "..."

reranker = Reranker(
    method="voyage",
    model="rerank-2"  # or rerank-2-lite
)

reranked = reranker.rerank(query, results, k=10)
```

**Models:**
- `rerank-2`: Best quality
- `rerank-2-lite`: Faster, cheaper

### 10.3.7 Jina Reranking

```python
os.environ["JINA_API_KEY"] = "..."

reranker = Reranker(
    method="jina",
    model="jina-reranker-v2-base-multilingual"
)

reranked = reranker.rerank(query, results, k=10)
```

### 10.3.8 LLM-based Reranking

```python
# Use LLM to score relevance
from ragbench.generation import Generator

generator = Generator(method="openai")

reranker = Reranker(
    method="llm_reranker",
    llm=generator
)

reranked = reranker.rerank(
    query="What is quantum computing?",
    results=results,
    k=10
)
```

**Best for:**
- Complex relevance criteria
- Reasoning required
- Explanation needed

### 10.3.9 Learning-to-Rank

```python
# Train custom ranker
from ragbench.reranking import LTRReranker
from sklearn.ensemble import RandomForestClassifier

# Train
ltr = LTRReranker(model=RandomForestClassifier())
ltr.fit(train_queries, train_results, train_labels)

# Rerank
reranked = ltr.rerank(query, results, k=10)
```

## 10.4 Advanced Features

### 10.4.1 Multi-Stage Reranking

```python
# Stage 1: Fast reranking (keep 50)
stage1 = rerank(
    query=query,
    results=results,  # 200 results
    method="bm25",
    k=50
)

# Stage 2: Accurate reranking (keep 10)
stage2 = rerank(
    query=query,
    results=stage1,
    method="cohere",
    k=10
)
```

### 10.4.2 Ensemble Reranking

```python
# Combine multiple rerankers
from ragbench.reranking import ensemble_rerank

rerankers = [
    Reranker("bm25"),
    Reranker("bge_reranker"),
    Reranker("minilm")
]

reranked = ensemble_rerank(
    query=query,
    results=results,
    rerankers=rerankers,
    weights=[0.3, 0.4, 0.3],  # Weights
    k=10
)
```

### 10.4.3 Domain-Specific Reranking

```python
# Use domain-specific model
reranker = Reranker(
    method="custom_cross_encoder",
    model="path/to/medical-reranker"  # Trained on medical data
)

reranked = reranker.rerank(query, results, k=10)
```

### 10.4.4 Diversity-Aware Reranking

```python
# Balance relevance and diversity
reranker = Reranker(
    method="mmr_reranker",
    lambda_param=0.7  # 0=pure diversity, 1=pure relevance
)

reranked = reranker.rerank(query, results, k=10)
```

## 10.5 Pipeline Integration

### 10.5.1 Full RAG with Reranking

```python
from ragbench.pipeline import ask

answer = ask(
    query="What is quantum computing?",
    collection="docs",
    store="qdrant",
    embedder="openai",
    k=50,  # Retrieve 50
    rerank=True,
    rerank_method="cohere",
    rerank_k=5,  # Rerank to 5
    generation_method="openai"
)
```

### 10.5.2 Custom Pipeline

```python
# 1. Retrieve
results = retrieve(query, method="hybrid_rrf", k=100)

# 2. First-stage rerank
stage1 = rerank(query, results, method="bm25", k=30)

# 3. Second-stage rerank
stage2 = rerank(query, stage1, method="cohere", k=10)

# 4. Generate answer
from ragbench.generation import Generator
generator = Generator(method="openai")
answer = generator.generate(query, context=stage2)
```

## 10.6 Performance Optimization

### 10.6.1 Speed vs Accuracy

```python
# Fastest: BM25
reranked = rerank(query, results, method="bm25", k=10)  # ~10ms

# Fast: Local cross-encoder
reranked = rerank(query, results, method="minilm", k=10)  # ~50ms

# Medium: BGE reranker
reranked = rerank(query, results, method="bge_reranker", k=10)  # ~200ms

# Slow: API rerankers
reranked = rerank(query, results, method="cohere", k=10)  # ~500ms

# Slowest: LLM reranker
reranked = rerank(query, results, method="llm_reranker", k=10)  # ~5000ms
```

### 10.6.2 Batch Reranking

```python
# Rerank multiple queries in batch
queries = ["query1", "query2", "query3"]
all_results = [results1, results2, results3]

reranker = Reranker("cohere")

# Batch processing (more efficient)
reranked_batch = reranker.rerank_batch(
    queries=queries,
    results_list=all_results,
    k=10
)
```

### 10.6.3 Caching

```python
# Cache reranking results
from functools import lru_cache

reranker = Reranker("cohere", cache=True)

# First call: hits API
reranked1 = reranker.rerank(query, results, k=10)  # ~500ms

# Same query: from cache
reranked2 = reranker.rerank(query, results, k=10)  # <1ms
```

## 10.7 Evaluation

### 10.7.1 Compare Rerankers

```python
methods = ["bm25", "bge_reranker", "cohere"]

for method in methods:
    reranked = rerank(query, results, method=method, k=10)
    
    print(f"\n{method.upper()}:")
    print(f"  Top score: {reranked[0].score:.3f}")
    print(f"  Top result: {reranked[0].content[:80]}...")
```

### 10.7.2 Measure Impact

```python
# Before reranking
print("BEFORE RERANKING:")
for i, r in enumerate(results[:5], 1):
    print(f"{i}. Score: {r.score:.3f}, Content: {r.content[:60]}...")

# After reranking
reranked = rerank(query, results, method="cohere", k=5)
print("\nAFTER RERANKING:")
for i, r in enumerate(reranked, 1):
    print(f"{i}. Score: {r.score:.3f}, Content: {r.content[:60]}...")

# Calculate ranking change
from ragbench.reranking import measure_rerank_impact
impact = measure_rerank_impact(results[:5], reranked)
print(f"\nRanking changes: {impact['swaps']}")
print(f"Score improvement: {impact['score_delta']:.3f}")
```

## 10.8 Best Practices

### 10.8.1 When to Rerank

```python
# ✅ DO rerank when:
# - Top-k accuracy matters
# - Have budget for API calls
# - Initial retrieval is broad

# ❌ DON'T rerank when:
# - Speed is critical
# - Retrieval is already accurate
# - Budget constrained
```

### 10.8.2 Over-Retrieval Factor

```python
# Rule of thumb: retrieve 3-5x more than needed

# Want 10 final results?
results = retrieve(query, k=30-50)  # Over-retrieve
reranked = rerank(query, results, k=10)  # Rerank to 10
```

### 10.8.3 Method Selection

```python
guidelines = {
    "development": "bm25",  # Fast, free
    "production_free": "bge_reranker",  # Local, no cost
    "production_quality": "cohere",  # API, high quality
    "real_time": "minilm",  # Fast local
    "research": "colbert_reranker"  # Best quality
}
```

---

# 11. Layer 7: Generation

*(This section was already covered in detail earlier - see the generation section above for complete documentation)*

## 11.1 Overview

The generation layer produces natural language answers from retrieved context using LLMs.

### 11.1.1 Quick Reference

```python
from ragbench.generation import Generator

# Create generator
generator = Generator(
    method="openai",  # or ollama, anthropic, cohere, etc.
    model="gpt-4o-mini",
    temperature=0.7
)

# Generate answer
answer = generator.generate(
    query="What is quantum computing?",
    context=retrieved_chunks
)

print(answer.answer)
print(f"Citations: {len(answer.citations)}")
```

### 11.1.2 Available Providers (9 total)

- **Local**: Ollama (free, no API key)
- **API**: OpenAI, Anthropic, Cohere, Gemini, Mistral, Groq, Together, OpenRouter

See the detailed Generation section above for complete documentation.

---

# 12. Pipeline & Auto Mode

## 12.1 Overview

The pipeline layer orchestrates all layers into a single, cohesive API.

### 12.1.1 Key Functions

- `auto()`: Automatic ingestion → chunking → embedding → indexing
- `ask()`: Complete RAG: retrieve → rerank → generate
- `retrieve()`: Standalone retrieval with auto-wiring
- `OmniPipeline`: Class-based pipeline for advanced control

## 12.2 The `auto()` Function

### 12.2.1 Basic Usage

```python
from ragbench.pipeline import auto

# Just chunk
chunks = auto("document.pdf")

# Chunk and embed
embeddings = auto(
    "document.pdf",
    embed=True,
    embedder="openai"
)

# Full indexing
result = auto(
    "document.pdf",
    embed=True,
    embedder="openai",
    index=True,
    store="qdrant",
    collection="my_docs"
)

print(f"Indexed {len(result.ids)} chunks")
```

### 12.2.2 Process Text Directly

```python
# Process raw text
text = "This is some text to process."

chunks = auto(
    text,
    as_text=True,  # Treat as raw text
    method="Sentence Chunking",
    chunk_size=100
)
```

### 12.2.3 Multiple Files

```python
# Process directory
result = auto(
    "path/to/documents/",
    embed=True,
    embedder="openai",
    index=True,
    store="sqlite",
    collection="docs"
)

# Specific files
result = auto(
    ["file1.pdf", "file2.docx", "file3.txt"],
    embed=True,
    embedder="openai",
    index=True,
    store="sqlite",
    collection="docs"
)
```

### 12.2.4 Return Everything

```python
# Get documents, chunks, and embeddings
result = auto(
    "document.pdf",
    embed=True,
    embedder="openai",
    include_documents=True,
    include_chunks=True
)

documents = result["documents"]
chunks = result["chunks"]
embeddings = result["embeddings"]
```

### 12.2.5 Custom Configuration

```python
result = auto(
    "document.pdf",
    # Chunking
    method="Recursive Character Chunking",
    chunk_size=800,
    overlap=80,
    # Embedding
    embed=True,
    embedder="openai",
    embed_options={
        "model": "text-embedding-3-large",
        "dimensions": 1536
    },
    # Vector store
    index=True,
    store="qdrant",
    collection="docs",
    store_options={
        "url": "http://localhost:6333"
    }
)
```

## 12.3 The `ask()` Function

### 12.3.1 Simple Q&A

```python
from ragbench.pipeline import ask

answer = ask(
    query="What is quantum computing?",
    collection="docs",
    store="sqlite",
    embedder="openai",
    generation_method="openai"
)

print(answer.answer)
```

### 12.3.2 With Reranking

```python
answer = ask(
    query="Explain machine learning",
    collection="docs",
    store="qdrant",
    embedder="openai",
    k=20,  # Retrieve 20
    rerank=True,
    rerank_method="cohere",
    rerank_k=5,  # Keep 5 after reranking
    generation_method="openai"
)
```

### 12.3.3 Return Intermediate Results

```python
result = ask(
    query="What is quantum entanglement?",
    collection="docs",
    store="sqlite",
    embedder="openai",
    k=10,
    rerank=True,
    rerank_method="bm25",
    generation_method="ollama",
    include_retrieval=True,  # Return retrieval results
    include_reranking=True   # Return reranking results
)

retrieval_results = result["retrieval"]
reranking_results = result["reranking"]
answer = result["answer"]

print(f"Retrieved: {len(retrieval_results)} chunks")
print(f"Reranked to: {len(reranking_results)} chunks")
print(f"Answer: {answer.answer}")
```

### 12.3.4 Streaming

```python
# Stream tokens in real-time
for token in ask(
    query="Explain quantum computing",
    collection="docs",
    store="sqlite",
    embedder="openai",
    generation_method="ollama",
    streaming=True
):
    print(token, end="", flush=True)
```

### 12.3.5 Multi-Turn Conversation

```python
from ragbench.generation import Conversation

conversation = Conversation()

# Turn 1
answer1 = ask(
    "What is supervised learning?",
    collection="ml_docs",
    conversation=conversation
)

conversation.add_user("What is supervised learning?")
conversation.add_assistant(answer1.answer)

# Turn 2
answer2 = ask(
    "Give me an example",  # Context-aware
    collection="ml_docs",
    conversation=conversation
)
```

### 12.3.6 Advanced Options

```python
answer = ask(
    query="Complex question about quantum physics",
    collection="physics_docs",
    store="qdrant",
    embedder="openai",
    # Retrieval
    retrieval_method="multi_query",
    k=30,
    filter={"year": {"$gte": 2020}},
    # Reranking
    rerank=True,
    rerank_method="cohere",
    rerank_k=10,
    # Generation
    generation_method="openai",
    generation_options={
        "model": "gpt-4",
        "temperature": 0.3,
        "max_tokens": 500
    },
    # Custom template
    template=my_custom_template
)
```

## 12.4 The `OmniPipeline` Class

### 12.4.1 Create Pipeline

```python
from ragbench.pipeline import OmniPipeline

pipeline = OmniPipeline(
    embedder="openai",
    store="qdrant",
    store_config={"url": "http://localhost:6333"},
    generation_method="openai",
    rerank_method="cohere"
)
```

### 12.4.2 Index Documents

```python
# Index a document
result = pipeline.index(
    source="document.pdf",
    collection="docs",
    chunk_size=800
)

# Index directory
result = pipeline.index(
    source="path/to/documents/",
    collection="docs"
)

# Index with custom options
result = pipeline.index(
    source="document.pdf",
    collection="docs",
    chunking_method="Semantic Chunking",
    chunk_options={"similarity_threshold": 0.7}
)
```

### 12.4.3 Query

```python
# Simple query
answer = pipeline.query(
    query="What is quantum computing?",
    collection="docs"
)

# With options
answer = pipeline.query(
    query="What is quantum computing?",
    collection="docs",
    k=20,
    rerank=True,
    rerank_k=5
)
```

### 12.4.4 Batch Processing

```python
# Index multiple documents
sources = ["doc1.pdf", "doc2.pdf", "doc3.pdf"]

for source in sources:
    pipeline.index(source, collection="docs")
    print(f"Indexed {source}")

# Batch queries
queries = [
    "What is quantum computing?",
    "Explain machine learning",
    "What is deep learning?"
]

answers = [pipeline.query(q, collection="docs") for q in queries]
```

### 12.4.5 Update Collection

```python
# Add new documents to existing collection
pipeline.index(
    source="new_document.pdf",
    collection="docs"  # Adds to existing collection
)

# Delete from collection
pipeline.delete(
    collection="docs",
    filter={"year": {"$lt": 2020}}  # Delete old documents
)
```

## 12.5 Advanced Pipeline Patterns

### 12.5.1 Multi-Collection RAG

```python
# Index different document types
pipeline.index("research_papers/", collection="papers")
pipeline.index("textbooks/", collection="books")
pipeline.index("articles/", collection="articles")

# Query all collections
answer = ask(
    query="What is quantum computing?",
    collections=["papers", "books", "articles"],
    store="qdrant",
    embedder="openai",
    generation_method="openai"
)
```

### 12.5.2 Incremental Indexing

```python
# Initial index
pipeline.index("documents/2020/", collection="docs")

# Add more later (incremental)
pipeline.index("documents/2021/", collection="docs")
pipeline.index("documents/2022/", collection="docs")

# Query searches all
answer = pipeline.query("recent developments", collection="docs")
```

### 12.5.3 Domain-Specific Pipelines

```python
# Medical RAG pipeline
medical_pipeline = OmniPipeline(
    embedder="biomedical",  # Domain-specific embedder
    store="qdrant",
    generation_method="openai",
    generation_options={
        "model": "gpt-4",
        "system_message": "You are a medical expert assistant."
    }
)

# Legal RAG pipeline
legal_pipeline = OmniPipeline(
    embedder="legal",
    store="qdrant",
    generation_method="anthropic",
    generation_options={
        "model": "claude-3-opus-20240229",
        "system_message": "You are a legal research assistant."
    }
)
```

### 12.5.4 Multi-Stage Retrieval

```python
# Stage 1: Broad retrieval
results_stage1 = pipeline.retrieve(
    query="quantum computing",
    collection="docs",
    method="hybrid_rrf",
    k=100
)

# Stage 2: Rerank
results_stage2 = pipeline.rerank(
    query="quantum computing",
    results=results_stage1,
    method="cohere",
    k=20
)

# Stage 3: Apply diversity
from ragbench.retrieval import apply_diversity
results_stage3 = apply_diversity(
    results_stage2,
    method="mmr",
    lambda_param=0.6,
    k=10
)

# Stage 4: Generate
answer = pipeline.generate(
    query="quantum computing",
    context=results_stage3
)
```

## 12.6 Pipeline Configuration

### 12.6.1 Environment Variables

```bash
# Set defaults
export OMNILOAD_EMBEDDER=openai
export OMNILOAD_VECTORSTORE=qdrant
export OMNILOAD_RETRIEVER=hybrid_rrf
export OMNILOAD_COLLECTION=default

# Pipeline uses these automatically
```

```python
# Uses environment defaults
pipeline = OmniPipeline()

# Override for specific pipeline
pipeline = OmniPipeline(
    embedder="cohere",  # Override default
    store="pinecone"
)
```

### 12.6.2 Configuration File

```yaml
# pipeline_config.yaml
embedding:
  method: openai
  model: text-embedding-3-small
  
vector_store:
  method: qdrant
  url: http://localhost:6333
  
retrieval:
  method: hybrid_rrf
  k: 20
  
reranking:
  method: cohere
  k: 5
  
generation:
  method: openai
  model: gpt-4o-mini
  temperature: 0.7
```

```python
import yaml

with open("pipeline_config.yaml") as f:
    config = yaml.safe_load(f)

pipeline = OmniPipeline(**config)
```

## 12.7 Error Handling

### 12.7.1 Graceful Fallbacks

```python
from ragbench.pipeline import ask
from ragbench.errors import EmbeddingError, GenerationError

try:
    answer = ask(
        query="What is quantum computing?",
        collection="docs",
        embedder="openai",
        generation_method="openai"
    )
except EmbeddingError:
    # Fallback to local embedder
    answer = ask(
        query="What is quantum computing?",
        collection="docs",
        embedder="bge",  # Local fallback
        generation_method="ollama"
    )
except GenerationError:
    # Fallback to different LLM
    answer = ask(
        query="What is quantum computing?",
        collection="docs",
        embedder="openai",
        generation_method="anthropic"
    )
```

### 12.7.2 Retry Logic

```python
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=4, max=10)
)
def resilient_ask(query, collection):
    return ask(
        query=query,
        collection=collection,
        embedder="openai",
        generation_method="openai"
    )

# Automatically retries on failure
answer = resilient_ask("What is quantum computing?", "docs")
```

### 12.7.3 Validation

```python
# Validate before processing
def validate_input(query, collection):
    if not query or len(query) < 3:
        raise ValueError("Query too short")
    
    if not collection:
        raise ValueError("Collection name required")
    
    return True

# Use validation
if validate_input(user_query, user_collection):
    answer = ask(user_query, collection=user_collection)
```

---

# 13. Complete RAG Workflows

## 13.1 End-to-End Examples

### 13.1.1 Document QA System

```python
from ragbench.pipeline import auto, ask

# === SETUP PHASE ===

# Index your documents
print("Indexing documents...")
result = auto(
    "path/to/documents/",
    embed=True,
    embedder="openai",
    index=True,
    store="sqlite",
    collection="company_docs"
)
print(f"✓ Indexed {len(result.ids)} chunks")

# === QUERY PHASE ===

# Interactive Q&A
while True:
    query = input("\nAsk a question (or 'quit'): ")
    if query.lower() == 'quit':
        break
    
    answer = ask(
        query=query,
        collection="company_docs",
        store="sqlite",
        embedder="openai",
        k=10,
        rerank=True,
        rerank_method="bm25",
        generation_method="ollama"
    )
    
    print(f"\nAnswer: {answer.answer}\n")
    
    if answer.citations:
        print("Sources:")
        for i, citation in enumerate(answer.citations, 1):
            print(f"  [{i}] {citation.document_id}")
```

### 13.1.2 Research Assistant

```python
from ragbench.pipeline import OmniPipeline
from ragbench.generation import Conversation

# Create pipeline
pipeline = OmniPipeline(
    embedder="openai",
    store="qdrant",
    generation_method="anthropic"
)

# Index research papers
print("Indexing research papers...")
pipeline.index(
    "research_papers/",
    collection="papers",
    chunking_method="Hierarchical Chunking",
    chunk_options={"parent_size": 2000, "child_size": 500}
)

# Multi-turn research session
conversation = Conversation()

queries = [
    "What are the latest developments in transformer models?",
    "How do they compare to RNNs?",
    "What are the main limitations?",
    "What future research directions are suggested?"
]

for query in queries:
    print(f"\n>>> {query}")
    
    answer = pipeline.query(
        query=query,
        collection="papers",
        k=15,
        rerank=True,
        rerank_k=5,
        conversation=conversation
    )
    
    print(f"\n{answer.answer}\n")
    print(f"Citations: {len(answer.citations)}")
    
    # Add to conversation history
    conversation.add_user(query)
    conversation.add_assistant(answer.answer)
```

### 13.1.3 Customer Support Bot

```python
from ragbench.pipeline import OmniPipeline
import logging

# Setup
pipeline = OmniPipeline(
    embedder="openai",
    store="sqlite",
    generation_method="openai",
    generation_options={
        "model": "gpt-4o-mini",
        "system_message": "You are a helpful customer support assistant. Be concise and friendly."
    }
)

# Index support docs
pipeline.index(
    ["faq.pdf", "user_manual.pdf", "troubleshooting.pdf"],
    collection="support_docs"
)

def handle_support_query(customer_query):
    """Process customer support query."""
    
    # Log query
    logging.info(f"Query: {customer_query}")
    
    # Get answer
    answer = pipeline.query(
        query=customer_query,
        collection="support_docs",
        k=5,
        rerank=True,
        rerank_method="bm25"
    )
    
    # Log result
    logging.info(f"Answer: {answer.answer[:100]}...")
    
    return {
        "answer": answer.answer,
        "sources": [c.document_id for c in answer.citations],
        "confidence": answer.citations[0].score if answer.citations else 0
    }

# Example usage
result = handle_support_query("How do I reset my password?")
print(result["answer"])
```

### 13.1.4 Code Search System

```python
from ragbench.pipeline import auto, ask

# Index codebase
print("Indexing codebase...")
result = auto(
    "src/",  # Source code directory
    embed=True,
    embedder="code_local",  # Code-specific embedder
    index=True,
    store="sqlite",
    collection="codebase",
    chunking_method="Code Chunking",
    chunk_options={
        "language": "python",
        "chunk_by": "function"
    }
)

# Search code
def search_code(query):
    """Search for code snippets."""
    
    results = ask(
        query=query,
        collection="codebase",
        store="sqlite",
        embedder="code_local",
        retrieval_method="sparse",  # Better for code
        k=5,
        generation_method="ollama",
        generation_options={
            "model": "codellama",
            "system_message": "You are a code explanation assistant."
        }
    )
    
    return results

# Example
result = search_code("How to implement authentication?")
print(result.answer)
```

### 13.1.5 Multi-Source Knowledge Base

```python
from ragbench.pipeline import OmniPipeline

pipeline = OmniPipeline(
    embedder="openai",
    store="qdrant",
    generation_method="openai"
)

# Index multiple sources
sources = {
    "confluence": {
        "connector": "confluence",
        "config": {"url": "...", "token": "..."},
        "collection": "wiki"
    },
    "notion": {
        "connector": "notion",
        "config": {"token": "...", "database_id": "..."},
        "collection": "notes"
    },
    "github": {
        "connector": "github",
        "config": {"repo": "...", "token": "..."},
        "collection": "code"
    }
}

for name, source in sources.items():
    print(f"Indexing {name}...")
    pipeline.index(
        source=source["connector"],
        connector_config=source["config"],
        collection=source["collection"]
    )

# Query across all sources
answer = pipeline.query(
    query="How does authentication work?",
    collections=["wiki", "notes", "code"],
    k=20,
    rerank=True,
    rerank_k=10
)

print(answer.answer)
```

## 13.2 Production Patterns

### 13.2.1 Asynchronous Processing

```python
import asyncio
from ragbench.pipeline import OmniPipeline

pipeline = OmniPipeline(
    embedder="openai",
    store="qdrant",
    generation_method="openai"
)

async def process_query(query):
    """Async query processing."""
    loop = asyncio.get_event_loop()
    answer = await loop.run_in_executor(
        None,
        pipeline.query,
        query,
        "docs"
    )
    return answer

async def process_multiple_queries(queries):
    """Process multiple queries concurrently."""
    tasks = [process_query(q) for q in queries]
    results = await asyncio.gather(*tasks)
    return results

# Usage
queries = ["query 1", "query 2", "query 3"]
results = asyncio.run(process_multiple_queries(queries))
```

### 13.2.2 Caching Layer

```python
from functools import lru_cache
from ragbench.pipeline import ask
import hashlib

@lru_cache(maxsize=1000)
def cached_ask(query_hash, collection):
    """Cached query function."""
    # Note: Can't cache query directly (unhashable)
    # So we cache by hash
    return ask(
        query=query_hash,
        collection=collection,
        embedder="openai",
        generation_method="openai"
    )

def ask_with_cache(query, collection):
    """Ask with caching."""
    # Create hash of query
    query_hash = hashlib.md5(query.encode()).hexdigest()
    
    # Check cache first
    return cached_ask(query_hash, collection)

# Usage
answer1 = ask_with_cache("What is quantum computing?", "docs")  # Cache miss
answer2 = ask_with_cache("What is quantum computing?", "docs")  # Cache hit
```

### 13.2.3 Rate Limiting

```python
from ratelimit import limits, sleep_and_retry
from ragbench.pipeline import ask

@sleep_and_retry
@limits(calls=10, period=60)  # 10 calls per minute
def rate_limited_ask(query, collection):
    """Rate-limited query function."""
    return ask(
        query=query,
        collection=collection,
        embedder="openai",
        generation_method="openai"
    )

# Usage
answer = rate_limited_ask("What is quantum computing?", "docs")
```

### 13.2.4 Monitoring & Logging

```python
import logging
import time
from ragbench.pipeline import ask

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def monitored_ask(query, collection):
    """Ask with monitoring."""
    
    start_time = time.time()
    
    logger.info(f"Query: {query}")
    logger.info(f"Collection: {collection}")
    
    try:
        answer = ask(
            query=query,
            collection=collection,
            embedder="openai",
            generation_method="openai"
        )
        
        duration = time.time() - start_time
        
        logger.info(f"Success in {duration:.2f}s")
        logger.info(f"Answer length: {len(answer.answer)} chars")
        logger.info(f"Citations: {len(answer.citations)}")
        
        return answer
        
    except Exception as e:
        duration = time.time() - start_time
        logger.error(f"Failed after {duration:.2f}s: {e}")
        raise
```

### 13.2.5 API Endpoint

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from ragbench.pipeline import OmniPipeline

app = FastAPI()

# Initialize pipeline
pipeline = OmniPipeline(
    embedder="openai",
    store="qdrant",
    generation_method="openai"
)

class Query(BaseModel):
    question: str
    collection: str = "docs"
    k: int = 10

class Answer(BaseModel):
    answer: str
    sources: list
    duration: float

@app.post("/ask", response_model=Answer)
async def ask_endpoint(query: Query):
    """RAG endpoint."""
    
    start = time.time()
    
    try:
        result = pipeline.query(
            query=query.question,
            collection=query.collection,
            k=query.k
        )
        
        return Answer(
            answer=result.answer,
            sources=[c.document_id for c in result.citations],
            duration=time.time() - start
        )
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.post("/index")
async def index_endpoint(source: str, collection: str):
    """Indexing endpoint."""
    
    try:
        result = pipeline.index(source, collection)
        return {"status": "success", "chunks": len(result.ids)}
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

## 13.3 Specialized Workflows

### 13.3.1 Summarization

```python
from ragbench.pipeline import OmniPipeline

pipeline = OmniPipeline(
    embedder="openai",
    store="memory",  # In-memory for temp storage
    generation_method="openai"
)

def summarize_document(document_path):
    """Summarize a long document."""
    
    # Index document
    pipeline.index(document_path, collection="temp")
    
    # Generate summary
    summary = pipeline.query(
        query="Provide a comprehensive summary of the main points",
        collection="temp",
        k=50,  # Retrieve many chunks
        generation_options={
            "max_tokens": 500,
            "temperature": 0.3
        }
    )
    
    return summary.answer

# Usage
summary = summarize_document("long_report.pdf")
print(summary)
```

### 13.3.2 Comparison Analysis

```python
def compare_documents(doc1, doc2, aspect):
    """Compare two documents on a specific aspect."""
    
    pipeline = OmniPipeline(
        embedder="openai",
        store="memory",
        generation_method="openai"
    )
    
    # Index both documents
    pipeline.index(doc1, collection="doc1")
    pipeline.index(doc2, collection="doc2")
    
    # Query both
    results1 = pipeline.query(
        query=f"What does this document say about {aspect}?",
        collection="doc1",
        k=10
    )
    
    results2 = pipeline.query(
        query=f"What does this document say about {aspect}?",
        collection="doc2",
        k=10
    )
    
    # Generate comparison
    comparison_prompt = f"""
    Document 1 says: {results1.answer}
    
    Document 2 says: {results2.answer}
    
    Compare and contrast these perspectives on {aspect}.
    """
    
    comparison = pipeline.generate(
        query=comparison_prompt,
        context=[]
    )
    
    return comparison.answer

# Usage
comparison = compare_documents(
    "proposal_a.pdf",
    "proposal_b.pdf",
    "cost-benefit analysis"
)
```

### 13.3.3 Citation Extraction

```python
def extract_citations(query, collection):
    """Get answer with detailed citations."""
    
    answer = ask(
        query=query,
        collection=collection,
        embedder="openai",
        k=10,
        generation_method="openai",
        include_retrieval=True
    )
    
    # Format with citations
    formatted = f"**Answer:**\n{answer['answer'].answer}\n\n"
    formatted += "**Sources:**\n"
    
    for i, citation in enumerate(answer['answer'].citations, 1):
        formatted += f"\n[{i}] {citation.document_id}\n"
        formatted += f"    Relevance: {citation.score:.2f}\n"
        formatted += f"    Content: {citation.content[:200]}...\n"
    
    return formatted
```

---

*The guide continues with 7 more major sections covering Advanced Topics, Configuration, Troubleshooting, Best Practices, Contributing, API Reference, and Appendices. Due to length constraints, would you like me to continue with specific sections?*
