# RAGBench Documentation

**Welcome to the RAGBench documentation!** This documentation provides comprehensive guides, tutorials, and API references for building production-ready RAG (Retrieval Augmented Generation) systems.

---

## 📚 Documentation Structure

### 🚀 Getting Started
Start here if you're new to RAGBench:

1. **[Installation Guide](guides/INSTALLATION_GUIDE.md)** - Complete installation instructions for all layers
2. **[Quick Start Guide](../QUICKSTART.md)** - Get up and running in 5 minutes
3. **[Architecture Overview](../ARCHITECTURE.md)** - Understand the system design

### 📖 Comprehensive Guides

**Main Documentation:**
- **[Comprehensive Guide - Part 1](guides/COMPREHENSIVE_GUIDE.md)**
  - Introduction & Core Concepts
  - Layers 1-7 (Ingestion → Generation)
  - Pipeline & Auto Mode
  - Complete RAG Workflows

- **[Comprehensive Guide - Part 2](guides/COMPREHENSIVE_GUIDE_PART2.md)**
  - Advanced Topics
  - Configuration Reference
  - Troubleshooting
  - Best Practices
  - Contributing Guide
  - Complete API Reference

### 🎓 Tutorials

**Step-by-Step Tutorials:**
- [Building Your First RAG System](tutorials/01-first-rag-system.md)
- [Document Q&A with Local Models](tutorials/02-local-rag.md)
- [Production RAG with Cloud APIs](tutorials/03-production-rag.md)
- [Multi-Modal RAG (Images + Text)](tutorials/04-multimodal-rag.md)
- [Custom Embedders and Retrievers](tutorials/05-custom-components.md)

### 🔧 Layer Documentation

**Detailed Layer Guides:**
- [Layer 1: Document Ingestion](layers/01-ingestion.md) - 82 connectors
- [Layer 2: Chunking](layers/02-chunking.md) - 106 methods
- [Layer 3: Embedding](layers/03-embedding.md) - 62 methods
- [Layer 4: Vector Stores](layers/04-vector-stores.md) - 41 stores
- [Layer 5: Retrieval](layers/05-retrieval.md) - 28 methods
- [Layer 6: Reranking](layers/06-reranking.md) - 21 methods
- [Layer 7: Generation](layers/07-generation.md) - 9 LLM providers

### 🎯 Use Case Guides

**Task-Specific Guides:**
- [Question Answering Systems](use-cases/question-answering.md)
- [Document Search & Discovery](use-cases/document-search.md)
- [Code Search & Analysis](use-cases/code-search.md)
- [Research Assistant](use-cases/research-assistant.md)
- [Customer Support Bot](use-cases/customer-support.md)
- [Content Generation](use-cases/content-generation.md)

### 📊 API Reference

**Complete API Documentation:**
- [Core API](api/core.md) - Document, Chunk, EmbeddedChunk
- [Pipeline API](api/pipeline.md) - auto(), ask(), OmniPipeline
- [Embedding API](api/embedding.md) - Embedder class
- [Vector Store API](api/vectorstore.md) - VectorStore class
- [Retrieval API](api/retrieval.md) - Retriever class
- [Reranking API](api/reranking.md) - Reranker class
- [Generation API](api/generation.md) - Generator class

### 🛠️ Advanced Topics

**Deep Dives:**
- [Hybrid Search Strategies](advanced/hybrid-search.md)
- [Multi-Vector Retrieval](advanced/multi-vector.md)
- [Query Understanding](advanced/query-understanding.md)
- [Context Management](advanced/context-management.md)
- [Streaming & Real-Time](advanced/streaming.md)
- [Custom Scoring](advanced/custom-scoring.md)
- [Evaluation Metrics](advanced/evaluation.md)

### ⚙️ Configuration

**Configuration Guides:**
- [Environment Variables](configuration/environment-variables.md)
- [Configuration Files](configuration/config-files.md)
- [API Keys Setup](configuration/api-keys.md)
- [External Services](configuration/external-services.md)

### 🐛 Troubleshooting

**Problem Solving:**
- [Installation Issues](troubleshooting/installation.md)
- [API Key Problems](troubleshooting/api-keys.md)
- [Performance Issues](troubleshooting/performance.md)
- [Common Errors](troubleshooting/common-errors.md)
- [FAQ](troubleshooting/faq.md)

### 🤝 Contributing

**Developer Guides:**
- [Contributing Guide](../CONTRIBUTING.md)
- [Development Setup](contributing/development-setup.md)
- [Adding New Methods](contributing/adding-methods.md)
- [Testing Guidelines](contributing/testing.md)
- [Code Style Guide](contributing/code-style.md)

---

## 🎯 Documentation by User Type

### For Beginners
1. [Installation Guide](guides/INSTALLATION_GUIDE.md)
2. [Quick Start](../QUICKSTART.md)
3. [First RAG System Tutorial](tutorials/01-first-rag-system.md)
4. [Core Concepts](guides/COMPREHENSIVE_GUIDE.md#1-introduction)

### For Developers
1. [Architecture Overview](../ARCHITECTURE.md)
2. [Comprehensive Guide](guides/COMPREHENSIVE_GUIDE.md)
3. [API Reference](api/)
4. [Contributing Guide](../CONTRIBUTING.md)

### For Data Scientists
1. [Embedding Methods](layers/03-embedding.md)
2. [Retrieval Strategies](layers/05-retrieval.md)
3. [Evaluation Metrics](advanced/evaluation.md)
4. [Custom Components](tutorials/05-custom-components.md)

### For DevOps/SRE
1. [Production Deployment](use-cases/production-deployment.md)
2. [Configuration Guide](configuration/)
3. [Monitoring & Logging](advanced/monitoring.md)
4. [Troubleshooting](troubleshooting/)

---

## 📖 Reading Path by Goal

### Goal: Build a Local RAG System (Free)
1. [Installation - Local Setup](guides/INSTALLATION_GUIDE.md#41-local-rag-system-no-api-costs)
2. [Local RAG Tutorial](tutorials/02-local-rag.md)
3. [Ollama Setup](guides/INSTALLATION_GUIDE.md#61-ollama-local-llm)
4. [Best Practices](guides/COMPREHENSIVE_GUIDE_PART2.md#17-best-practices)

### Goal: Production RAG System
1. [Installation - Production](guides/INSTALLATION_GUIDE.md#47-production-system-high-scale)
2. [Architecture Overview](../ARCHITECTURE.md)
3. [Production Tutorial](tutorials/03-production-rag.md)
4. [Configuration](configuration/)
5. [Monitoring](advanced/monitoring.md)

### Goal: Understand RAG Deeply
1. [What is RAG?](guides/COMPREHENSIVE_GUIDE.md#11-what-is-ragbench)
2. [All Layers in Detail](guides/COMPREHENSIVE_GUIDE.md#5-layer-1-document-ingestion)
3. [Advanced Topics](guides/COMPREHENSIVE_GUIDE_PART2.md#14-advanced-topics)
4. [Evaluation](advanced/evaluation.md)

### Goal: Contribute to RAGBench
1. [Contributing Guide](../CONTRIBUTING.md)
2. [Development Setup](contributing/development-setup.md)
3. [Adding Methods](contributing/adding-methods.md)
4. [Code Style](contributing/code-style.md)

---

## 🔍 Quick Reference

### Most Used Pages
- [Installation Guide](guides/INSTALLATION_GUIDE.md) - Setup instructions
- [Quick Start](../QUICKSTART.md) - 5-minute tutorial
- [API Reference](api/) - Complete API docs
- [Troubleshooting](troubleshooting/) - Problem solving

### Key Concepts
- **Document**: Raw content from ingestion
- **Chunk**: Split piece of document
- **Embedding**: Vector representation of text
- **Collection**: Named group of vectors
- **Retrieval**: Finding relevant chunks
- **Reranking**: Refining retrieval results
- **Generation**: Creating answers with LLMs

### Quick Links
- [GitHub Repository](https://github.com/Omkar-S-Kulkarni/ragbench)
- [PyPI Package](https://pypi.org/project/ragbench-ai/)
- [Issue Tracker](https://github.com/Omkar-S-Kulkarni/ragbench/issues)
- [Discussions](https://github.com/Omkar-S-Kulkarni/ragbench/discussions)

---

## 📊 Documentation Stats

- **Total Pages**: 50+
- **Code Examples**: 500+
- **Tutorials**: 10+
- **API Methods**: 340+
- **Lines of Documentation**: 60,000+

---

## 🆘 Need Help?

Can't find what you're looking for?

1. **Search**: Use Ctrl+F or the search function
2. **FAQ**: Check [Frequently Asked Questions](troubleshooting/faq.md)
3. **GitHub Issues**: [Open an issue](https://github.com/Omkar-S-Kulkarni/ragbench/issues)
4. **Discussions**: [Ask the community](https://github.com/Omkar-S-Kulkarni/ragbench/discussions)

---

## 📝 Documentation License

This documentation is part of RAGBench and is licensed under the Apache 2.0 License.

---

## 🔄 Last Updated

**Version**: 0.4.0  
**Date**: November 2026

For the latest updates, visit: https://github.com/Omkar-S-Kulkarni/ragbench
