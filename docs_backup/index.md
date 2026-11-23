# RAGBench Documentation

<div align="center">
  <h1>🚀 RAGBench</h1>
  <p><strong>Layered, Independent, Auto-Chainable RAG Framework</strong></p>
  <p>
    <a href="https://pypi.org/project/ragbench-ai/"><img src="https://img.shields.io/pypi/v/ragbench-ai.svg" alt="PyPI version"></a>
    <a href="https://github.com/Omkar-S-Kulkarni/ragbench/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="License"></a>
    <a href="https://github.com/Omkar-S-Kulkarni/ragbench"><img src="https://img.shields.io/github/stars/Omkar-S-Kulkarni/ragbench?style=social" alt="GitHub stars"></a>
  </p>
</div>

---

## What is RAGBench?

RAGBench is a **complete, production-ready RAG framework** with 340+ methods across 7 independent layers:

```
Ingestion → Chunking → Embedding → Vector Store → Retrieval → Reranking → Generation
  82         106         62           41            28          21           9
```

### Key Features

✅ **Layer Independence** - Use any layer standalone  
✅ **340+ Methods** - Comprehensive method coverage  
✅ **Smart Auto-Routing** - Automatic method selection  
✅ **Local & Cloud** - Works offline or with APIs  
✅ **Production Ready** - Robust error handling & monitoring

---

## Quick Start

### Install

```bash
pip install ragbench-ai
```

### Complete RAG in 10 Lines

```python
from ragbench.pipeline import auto, ask

# Index documents
auto("documents/", embed=True, index=True, 
     embedder="openai", collection="docs")

# Ask questions
answer = ask("What is quantum computing?", 
             collection="docs",
             generation_method="openai")

print(answer.answer)
```

[See Full Quick Start →](getting-started/quickstart.md)

---

## Documentation

### 📚 Core Guides

**🚀 Getting Started**
- [Installation Guide](guides/INSTALLATION_GUIDE.md)
- [Quick Start (5 min)](getting-started/quickstart.md)
- [Architecture](getting-started/architecture.md)

**📖 Learn**
- [Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md)
- [Tutorials](tutorials/index.md)
- [Use Cases](use-cases/index.md)

**🔧 Reference**
- [API Reference](api/index.md)
- [Layer Docs](layers/index.md)
- [Configuration](configuration/index.md)

### 🎓 Tutorials

**New to RAGBench?** Follow these tutorials:

1. Building Your First RAG System (Coming soon)
2. Local RAG with Ollama (Coming soon)
3. Production RAG (Coming soon)
4. Multi-Modal RAG (Coming soon)

### 📊 Layer Documentation

Deep dive into each layer:

| Layer | Methods | Documentation |
|-------|---------|--------------|
| 1. Ingestion | 82 connectors | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#5-layer-1-document-ingestion) |
| 2. Chunking | 106 methods | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#6-layer-2-chunking) |
| 3. Embedding | 62 methods | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#7-layer-3-embedding) |
| 4. Vector Stores | 41 stores | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#8-layer-4-vector-stores) |
| 5. Retrieval | 28 methods | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#9-layer-5-retrieval) |
| 6. Reranking | 21 methods | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#10-layer-6-reranking) |
| 7. Generation | 9 providers | [→ See Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md#11-layer-7-generation) |

---

## Popular Pages

- [Installation Guide](guides/INSTALLATION_GUIDE.md) - Complete setup for all layers
- [Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md) - 50,000+ lines of detailed docs
- [API Reference](api/index.md) - Complete API documentation
- [Troubleshooting](troubleshooting/index.md) - Solutions to common issues
- [Best Practices](guides/COMPREHENSIVE_GUIDE_PART2.md#17-best-practices) - Production patterns

---

## Examples

### Local RAG (No API Keys)

```python
from ragbench.pipeline import auto, ask

# Index with local models
auto("document.pdf", embed=True, index=True,
     embedder="bge", store="sqlite", collection="docs")

# Ask with Ollama (local LLM)
answer = ask("What's in this document?", 
             collection="docs",
             embedder="bge",
             generation_method="ollama")
```

### Production RAG (Cloud APIs)

```python
# High-quality embeddings + reranking + GPT-4
answer = ask(
    "Complex question here",
    collection="docs",
    embedder="openai",
    k=50,
    rerank=True,
    rerank_method="cohere",
    rerank_k=5,
    generation_method="openai",
    generation_options={"model": "gpt-4"}
)
```

[More Examples →](guides/COMPREHENSIVE_GUIDE.md#13-complete-rag-workflows)

---

## Community

- **GitHub**: [github.com/Omkar-S-Kulkarni/ragbench](https://github.com/Omkar-S-Kulkarni/ragbench)
- **Issues**: [Report bugs or request features](https://github.com/Omkar-S-Kulkarni/ragbench/issues)
- **Discussions**: [Ask questions](https://github.com/Omkar-S-Kulkarni/ragbench/discussions)
- **PyPI**: [pypi.org/project/ragbench-ai](https://pypi.org/project/ragbench-ai/)

---

## License

Apache 2.0 - See LICENSE file in the repository

---

## Citation

```bibtex
@software{ragbench2024,
  title = {RAGBench: Layered Auto-RAG Framework},
  author = {RAGBench Contributors},
  year = {2024},
  url = {https://github.com/Omkar-S-Kulkarni/ragbench}
}
```
