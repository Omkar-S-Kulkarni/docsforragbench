# Quick Start

Get started with OmniLoad in 5 minutes.

## Installation

```bash
pip install omniload
```

## Your First RAG Pipeline

### 1. Set up API key

```bash
export OPENAI_API_KEY=sk-...
```

### 2. Index documents

```python
from pipeline import auto

# Index documents
auto(
    "documents/*.pdf",
    embedder="openai_text_embedding_3_small",
    store="sqlite",
    collection="my_docs"
)
```

### 3. Ask questions

```python
from pipeline import ask

answer = ask(
    "What is machine learning?",
    collection="my_docs",
    embedder="openai_text_embedding_3_small",
    llm_provider="openai",
    llm_model="gpt-4o-mini"
)

print(answer['answer'])
```

## Using the CLI

### Ingest

```bash
omniload ingest documents/*.pdf --collection my_docs
```

### Ask

```bash
omniload ask "What is machine learning?" --collection my_docs
```

### Check System

```bash
omniload doctor --check-all
```

## Next Steps

- [Installation Guide](../guides/INSTALLATION_GUIDE.md) - All installation options
- [Architecture](architecture.md) - How it works
- [Comprehensive Guide](../guides/COMPREHENSIVE_GUIDE.md) - Complete documentation
- [API Usage Examples](../guides/COMPREHENSIVE_GUIDE.md#4-quick-start-guide) - More code examples

## Common Patterns

### Local-only (no API)

```python
from pipeline import auto, ask

# Use hash-based embeddings (free, instant)
auto("docs/*.txt", embedder="hash_baseline", collection="local")

# Use Ollama for generation (runs locally)
answer = ask(
    "What is this about?",
    collection="local",
    embedder="hash_baseline",
    llm_provider="ollama",
    llm_model="llama3.1"
)
```

### Multi-turn Conversation

```python
from pipeline import OmniPipeline

pipeline = OmniPipeline(
    embedder="openai_text_embedding_3_small",
    llm_provider="openai"
)

pipeline.index(documents)

# Ask multiple questions
answer1 = pipeline.ask("What is RAG?")
answer2 = pipeline.ask("How does it work?")  # Remembers context
answer3 = pipeline.ask("Give me an example")
```

### With Reranking

```python
from pipeline import ask

answer = ask(
    query="complex question",
    collection="docs",
    embedder="openai_text_embedding_3_small",
    reranker="cohere",  # Add reranking
    reranker_model="rerank-v3.5",
    llm_provider="anthropic",
    llm_model="claude-3-5-sonnet-20241022"
)
```

### Streaming

```python
from pipeline import OmniPipeline

pipeline = OmniPipeline(
    embedder="openai_text_embedding_3_small",
    llm_provider="openai"
)

# Stream answer token by token
for chunk in pipeline.ask_stream("What is machine learning?"):
    print(chunk, end='', flush=True)
```

## Troubleshooting

### ModuleNotFoundError

**Problem:** `ModuleNotFoundError: No module named 'openai'`

**Solution:** Install the generation extra:

```bash
pip install omniload[generate-openai]
```

### API Key Error

**Problem:** `Error: OPENAI_API_KEY not set`

**Solution:** Set environment variable:

```bash
export OPENAI_API_KEY=sk-...
```

Or use `.env` file:

```bash
# .env
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
```

### Collection Not Found

**Problem:** `CollectionNotFoundError: 'my_docs' not found`

**Solution:** Index documents first:

```python
auto("documents/*.pdf", collection="my_docs")
```

## Get Help

- [GitHub Issues](https://github.com/Omkar-S-Kulkarni/ragbench/issues)
- [Discussions](https://github.com/Omkar-S-Kulkarni/ragbench/discussions)
- [Comprehensive Guide](../guides/COMPREHENSIVE_GUIDE.md) - Complete documentation
