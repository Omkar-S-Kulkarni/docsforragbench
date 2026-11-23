# RAGBench Complete Installation Guide

**Version:** 0.4.0  
**Last Updated:** 2024

This guide provides a complete list of everything you need to install to get started with RAGBench, organized by layer and use case.

---

## Table of Contents

1. [Quick Start - Minimal Installation](#1-quick-start-minimal-installation)
2. [Base Installation](#2-base-installation)
3. [Layer-by-Layer Installation](#3-layer-by-layer-installation)
4. [Use Case Based Installation](#4-use-case-based-installation)
5. [Platform-Specific Requirements](#5-platform-specific-requirements)
6. [External Services Setup](#6-external-services-setup)
7. [Verification & Testing](#7-verification-testing)
8. [Troubleshooting Installation Issues](#8-troubleshooting-installation-issues)

---

# 1. Quick Start - Minimal Installation

## 1.1 Absolute Minimum (No API Keys, Offline)

This gets you started with zero external dependencies:

```bash
# Python 3.9 or higher
python --version  # Should be 3.9+

# Install base package
pip install ragbench-ai

# That's it! You can now use:
# - hash_baseline embedder (offline, non-semantic)
# - BM25 retrieval (offline)
# - Memory/SQLite vector store (local)
# - Ollama LLM (requires separate install)
```

**What you can do:**
- ✅ Ingest documents (82 connectors)
- ✅ Chunk text (106 methods)
- ✅ Basic embedding (hash_baseline)
- ✅ Store vectors locally
- ✅ Keyword search (BM25)
- ⚠️ No semantic search (use API or install local models)
- ⚠️ No LLM generation (need Ollama or API)

## 1.2 Recommended Starter Setup

For a fully functional local RAG system:

```bash
# 1. Install RAGBench with local models
pip install "ragbench-ai[embed-local,generate-ollama]"

# 2. Install Ollama (local LLM)
# macOS/Linux:
curl -fsSL https://ollama.ai/install.sh | sh

# Windows: Download from https://ollama.ai

# 3. Pull a model
ollama pull llama3.1

# 4. Start Ollama
ollama serve

# Now you have a complete local RAG system!
```

**What you can do:**
- ✅ Everything from minimal +
- ✅ Semantic embeddings (BGE, E5, etc.)
- ✅ Local LLM generation (Llama 3.1, Mistral, etc.)
- ✅ Complete RAG pipeline offline
- ✅ No API costs

---

# 2. Base Installation

## 2.1 System Requirements

### Python Version
```bash
# Minimum: Python 3.9
# Recommended: Python 3.10 or 3.11
# Supported: 3.9, 3.10, 3.11, 3.12, 3.13

python --version
# or
python3 --version
```

### Operating System
- ✅ Linux (Ubuntu, Debian, CentOS, Fedora, etc.)
- ✅ macOS (Intel and Apple Silicon)
- ✅ Windows 10/11

### Hardware Requirements

**Minimum (API-based):**
- RAM: 4GB
- Storage: 2GB free
- CPU: Any modern CPU

**Recommended (Local models):**
- RAM: 16GB (8GB minimum)
- Storage: 20GB free (for models)
- CPU: Multi-core (4+ cores)
- GPU: Optional but recommended (NVIDIA with CUDA)

**For Large-Scale Production:**
- RAM: 32GB+
- Storage: 100GB+ SSD
- CPU: 16+ cores
- GPU: NVIDIA GPU with 16GB+ VRAM

## 2.2 Core Package Installation

### Method 1: pip (Recommended)

```bash
# Base installation (standard library only)
pip install ragbench-ai

# With specific extras
pip install "ragbench-ai[embed-local]"
pip install "ragbench-ai[embed-local,generate-ollama]"

# Everything (warning: downloads 5GB+)
pip install "ragbench-ai[all]"
```

### Method 2: From Source

```bash
# Clone repository
git clone https://github.com/Omkar-S-Kulkarni/ragbench.git
cd ragbench

# Install in editable mode
pip install -e .

# With extras
pip install -e ".[embed-local,generate-ollama]"
```

### Method 3: Virtual Environment (Recommended)

```bash
# Create virtual environment
python -m venv ragbench-env

# Activate
# Linux/Mac:
source ragbench-env/bin/activate
# Windows CMD:
ragbench-env\Scripts\activate.bat
# Windows PowerShell:
ragbench-env\Scripts\Activate.ps1

# Install
pip install ragbench-ai
```

### Method 4: Conda

```bash
# Create conda environment
conda create -n ragbench python=3.11
conda activate ragbench

# Install
pip install ragbench-ai
```

---

# 3. Layer-by-Layer Installation

## 3.1 Layer 1: Document Ingestion

### Core (Always Installed)

```bash
# Included with base installation
pip install ragbench-ai
```

**Includes:**
- Text files (txt, md, rst)
- Basic PDF extraction
- CSV files
- JSON/YAML files

### PDF Processing (Enhanced)

```bash
# For scanned PDFs (OCR)
pip install pytesseract pdf2image pillow

# System dependencies:
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt-get update
sudo apt-get install -y tesseract-ocr poppler-utils
```

**macOS:**
```bash
brew install tesseract poppler
```

**Windows:**
1. Download Tesseract: https://github.com/UB-Mannheim/tesseract/wiki
2. Download Poppler: https://github.com/oschwartz10612/poppler-windows/releases
3. Add to PATH or set environment variables:
```bash
set TESSERACT_CMD=C:\Program Files\Tesseract-OCR\tesseract.exe
set POPPLER_PATH=C:\poppler\bin
```

### Microsoft Office Documents

```bash
# Word, Excel, PowerPoint
pip install python-docx openpyxl python-pptx

# Alternative (more comprehensive)
pip install unstructured
```

### Database Connectors

```bash
# PostgreSQL
pip install psycopg2-binary

# MySQL
pip install mysql-connector-python

# SQLite (included with Python)

# MongoDB
pip install pymongo

# Redis
pip install redis

# Elasticsearch
pip install elasticsearch

# Oracle
pip install cx_Oracle
```

### Cloud Storage Connectors

```bash
# AWS S3
pip install boto3

# Google Cloud Storage
pip install google-cloud-storage

# Azure Blob Storage
pip install azure-storage-blob

# Dropbox
pip install dropbox

# Google Drive
pip install google-api-python-client google-auth
```

### Web & API Connectors

```bash
# Web scraping
pip install beautifulsoup4 requests lxml

# RSS/Atom feeds
pip install feedparser

# GitHub
pip install PyGithub

# GitLab
pip install python-gitlab

# Confluence
pip install atlassian-python-api

# Notion
pip install notion-client

# Slack
pip install slack-sdk

# Jira
pip install jira
```

### Complete Layer 1 Installation

```bash
# Everything for ingestion
pip install ragbench-ai \
    pytesseract pdf2image pillow \
    python-docx openpyxl python-pptx \
    psycopg2-binary pymongo redis elasticsearch \
    boto3 google-cloud-storage azure-storage-blob \
    beautifulsoup4 feedparser PyGithub notion-client
```

## 3.2 Layer 2: Chunking

### Core Chunking (Always Included)

```bash
# Included with base installation
pip install ragbench-ai
```

**Includes all 106 chunking methods:**
- Fixed size (character, token, sentence)
- Recursive chunking
- Semantic chunking
- Structure-aware (Markdown, HTML, JSON, Code)
- Hierarchical chunking
- Sliding window

### Optional: Enhanced Tokenization

```bash
# For token-based chunking
pip install tiktoken

# For advanced NLP
pip install spacy
python -m spacy download en_core_web_sm

# For multilingual support
python -m spacy download xx_ent_wiki_sm
```

### Complete Layer 2 Installation

```bash
# Enhanced chunking
pip install ragbench-ai tiktoken spacy
python -m spacy download en_core_web_sm
```

## 3.3 Layer 3: Embedding

This is where most dependencies come in!

### 3.3.1 Baseline (No Installation Needed)

```bash
# Already included
pip install ragbench-ai
```

**Includes:**
- `hash_baseline` - Deterministic hash (non-semantic)
- `bm25` - Sparse keyword matching
- `tfidf` - Term frequency weighting

### 3.3.2 Local Dense Embeddings

```bash
# Base local embedders
pip install "ragbench-ai[embed-local]"

# Or manually:
pip install sentence-transformers transformers torch
```

**What this installs:**
- sentence-transformers (~500MB)
- transformers (~500MB)
- torch (~800MB for CPU, ~2GB for GPU)

**Includes embedders:**
- `bge` - BAAI BGE models
- `e5` - Microsoft E5
- `gte` - Alibaba GTE
- `minilm` - Fast small model
- `sentence_transformers` - Any HF model
- `arctic` - Snowflake Arctic
- `nomic` - Nomic Embed

**For GPU support (NVIDIA):**

```bash
# CUDA 11.8
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118

# CUDA 12.1
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121

# Check installation
python -c "import torch; print(torch.cuda.is_available())"
```

**For Apple Silicon (M1/M2/M3):**

```bash
# PyTorch with MPS support
pip install torch torchvision

# Verify
python -c "import torch; print(torch.backends.mps.is_available())"
```

### 3.3.3 Multimodal Embeddings

```bash
# Images, audio, video
pip install "ragbench-ai[embed-multimodal]"

# Or manually:
pip install sentence-transformers pillow opencv-python-headless librosa torch transformers
```

**Includes:**
- `clip` - Text + images
- `siglip` - Text + images (Google)
- `clap` - Text + audio
- Image embedders
- Audio embedders
- Video frame embedders

### 3.3.4 Multi-Vector Embeddings

```bash
# ColBERT, ColPali
pip install "ragbench-ai[embed-multivector]"

# Or manually:
pip install colpali-engine pylate torch pillow
```

**Includes:**
- `colbert` - Late interaction
- `colpali` - Multi-vector for documents

### 3.3.5 Classical Embeddings

```bash
# Word2Vec, FastText, GloVe
pip install "ragbench-ai[embed-classical]"

# Or manually:
pip install gensim fasttext-wheel
```

**Includes:**
- `word2vec`
- `fasttext`
- `doc2vec`
- `glove`

### 3.3.6 API-Based Embeddings

**No pip installation needed!** Just set API keys:

```bash
# OpenAI
export OPENAI_API_KEY="sk-..."

# Cohere
export COHERE_API_KEY="..."

# Voyage AI
export VOYAGE_API_KEY="..."

# Jina AI
export JINA_API_KEY="..."

# Google
export GOOGLE_API_KEY="..."

# Mistral AI
export MISTRAL_API_KEY="..."
```

**Providers:**
- `openai` - text-embedding-3-small/large
- `cohere` - embed-v4.0
- `voyage` - voyage-3
- `jina` - jina-embeddings-v3
- `mistral` - mistral-embed
- `google_gemini` - gemini-embedding-001
- `azure_openai`
- `google_vertex`

### 3.3.7 AWS Bedrock

```bash
pip install "ragbench-ai[embed-bedrock]"

# Or manually:
pip install boto3

# Configure AWS credentials
aws configure
# Or set environment variables:
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
export AWS_REGION="us-east-1"
```

### 3.3.8 Self-Hosted Servers

**Ollama:**
```bash
# macOS/Linux:
curl -fsSL https://ollama.ai/install.sh | sh

# Windows: Download from https://ollama.ai

# Pull embedding model
ollama pull nomic-embed-text
ollama pull mxbai-embed-large

# Start server
ollama serve

# Set URL (optional, default is localhost:11434)
export OLLAMA_HOST="http://localhost:11434"
```

**llama.cpp server:**
```bash
# Clone and build
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make

# Run server
./server -m models/model.gguf -c 2048 --port 8080

# Set URL
export LLAMACPP_URL="http://localhost:8080"
```

**vLLM:**
```bash
# Install vLLM
pip install vllm

# Run server
python -m vllm.entrypoints.openai.api_server \
    --model BAAI/bge-small-en-v1.5 \
    --port 8000

# Set URL
export VLLM_BASE_URL="http://localhost:8000"
```

**TEI (Text Embeddings Inference):**
```bash
# Using Docker
docker run -p 8080:80 \
    -v $PWD/data:/data \
    ghcr.io/huggingface/text-embeddings-inference:latest \
    --model-id BAAI/bge-small-en-v1.5

# Set URL
export TEI_URL="http://localhost:8080"
```

### Complete Layer 3 Installation

**All local embedders:**
```bash
pip install "ragbench-ai[embed-all]"
```

**Includes:**
- All local dense embedders
- Multimodal embedders
- Multi-vector embedders
- Classical embedders
- AWS Bedrock support

**Size:** ~5-7 GB download

## 3.4 Layer 4: Vector Stores

### 3.4.1 Local Stores (No Server)

```bash
# In-memory (always included)
pip install ragbench-ai

# SQLite with vector extension
pip install "ragbench-ai[store-sqlite]"
# Or manually:
pip install sqlite-vec

# DuckDB
pip install "ragbench-ai[store-duckdb]"
# Or manually:
pip install duckdb

# FAISS (Facebook)
pip install "ragbench-ai[store-local]"
# Or manually:
pip install faiss-cpu
# For GPU:
pip install faiss-gpu

# ChromaDB
pip install chromadb

# HNSW
pip install hnswlib

# Annoy
pip install annoy

# usearch
pip install usearch

# LanceDB
pip install lancedb
```

### 3.4.2 Managed Vector Databases

**Pinecone:**
```bash
pip install "ragbench-ai[store-pinecone]"
# Or manually:
pip install pinecone-client

# Set API key
export PINECONE_API_KEY="..."
```

**Qdrant:**
```bash
pip install "ragbench-ai[store-qdrant]"
# Or manually:
pip install qdrant-client

# Local Qdrant (Docker):
docker run -p 6333:6333 qdrant/qdrant

# Or cloud:
export QDRANT_URL="https://xyz.cloud.qdrant.io"
export QDRANT_API_KEY="..."
```

**Weaviate:**
```bash
pip install "ragbench-ai[store-weaviate]"
# Or manually:
pip install weaviate-client

# Local Weaviate (Docker):
docker run -p 8080:8080 semitechnologies/weaviate:latest

# Or cloud:
export WEAVIATE_URL="https://xyz.weaviate.network"
export WEAVIATE_API_KEY="..."
```

**Milvus:**
```bash
pip install "ragbench-ai[store-milvus]"
# Or manually:
pip install pymilvus

# Local Milvus (Docker):
wget https://github.com/milvus-io/milvus/releases/download/v2.3.0/milvus-standalone-docker-compose.yml -O docker-compose.yml
docker-compose up -d

# Or cloud:
export MILVUS_URI="https://xyz.milvus.io"
export MILVUS_TOKEN="..."
```

### 3.4.3 Database Extensions

**PostgreSQL (pgvector):**
```bash
pip install "ragbench-ai[store-postgres]"
# Or manually:
pip install psycopg2-binary pgvector

# PostgreSQL setup:
# 1. Install PostgreSQL
# 2. Install pgvector extension
sudo apt install postgresql-16-pgvector  # Ubuntu

# 3. Enable in database
psql -d mydb -c "CREATE EXTENSION vector;"

# Set connection
export PGVECTOR_DSN="postgresql://user:pass@localhost/dbname"
```

**Redis:**
```bash
pip install "ragbench-ai[store-redis]"
# Or manually:
pip install redis

# Install Redis
# Ubuntu:
sudo apt install redis-server

# macOS:
brew install redis

# Start Redis
redis-server

# Or Docker:
docker run -p 6379:6379 redis:latest

# Set URL
export REDIS_URL="redis://localhost:6379"
```

**MongoDB:**
```bash
pip install pymongo

# Install MongoDB
# Ubuntu:
sudo apt install mongodb

# macOS:
brew install mongodb-community

# Start MongoDB
mongod

# Or Docker:
docker run -p 27017:27017 mongo:latest

# Set URI
export MONGODB_URI="mongodb://localhost:27017"
```

### 3.4.4 Search Engines

**Elasticsearch:**
```bash
pip install "ragbench-ai[store-elastic]"
# Or manually:
pip install elasticsearch

# Install Elasticsearch (Docker):
docker run -p 9200:9200 -e "discovery.type=single-node" elasticsearch:8.11.0

# Set URL
export ELASTICSEARCH_URL="http://localhost:9200"
export ELASTICSEARCH_API_KEY="..."
```

**OpenSearch:**
```bash
pip install "ragbench-ai[store-opensearch]"
# Or manually:
pip install opensearch-py

# Install OpenSearch (Docker):
docker run -p 9200:9200 -p 9600:9600 \
    -e "discovery.type=single-node" \
    opensearchproject/opensearch:latest

# Set URL
export OPENSEARCH_URL="http://localhost:9200"
export OPENSEARCH_USER="admin"
export OPENSEARCH_PASSWORD="admin"
```

### Complete Layer 4 Installation

```bash
# All vector stores
pip install "ragbench-ai[store-all]"
```

**Includes:**
- Local stores (FAISS, ChromaDB, etc.)
- SQLite and DuckDB
- Pinecone, Qdrant, Weaviate, Milvus clients
- PostgreSQL pgvector
- Redis, MongoDB
- Elasticsearch, OpenSearch

## 3.5 Layer 5: Retrieval

### Core Retrieval (Always Included)

```bash
pip install ragbench-ai
```

**Includes all 28 retrieval methods:**
- Dense search
- Sparse search (BM25)
- Hybrid search
- Multi-query
- HyDE
- Query rewrite
- Decomposition
- MMR diversity
- Parent-child
- Sentence window
- Self-query
- Multi-collection

**No additional installation needed!**

### Optional: Enhanced NLP

```bash
# For better query processing
pip install spacy nltk

# Download models
python -m spacy download en_core_web_sm
python -m nltk.downloader punkt stopwords
```

## 3.6 Layer 6: Reranking

### 3.6.1 Local Rerankers

```bash
# Local cross-encoders
pip install "ragbench-ai[rerank-local]"

# Or manually:
pip install sentence-transformers transformers torch FlagEmbedding
```

**Includes:**
- `bge_reranker` - BAAI reranker
- `minilm` - Fast cross-encoder
- `colbert_reranker` - ColBERT reranking

### 3.6.2 API Rerankers

**No pip installation needed!** Just set API keys:

```bash
# Cohere
export COHERE_API_KEY="..."

# Voyage AI
export VOYAGE_API_KEY="..."

# Jina AI
export JINA_API_KEY="..."
```

**Providers:**
- `cohere` - rerank-english-v3.0
- `voyage` - rerank-2
- `jina` - jina-reranker-v2

### Complete Layer 6 Installation

```bash
# Local rerankers
pip install "ragbench-ai[rerank-local]"

# API rerankers (just set API keys)
export COHERE_API_KEY="..."
```

## 3.7 Layer 7: Generation

### 3.7.1 Local LLMs (Ollama)

```bash
# Install Ollama
# macOS/Linux:
curl -fsSL https://ollama.ai/install.sh | sh

# Windows: Download from https://ollama.ai

# Install Python package
pip install "ragbench-ai[generate-ollama]"
# Or manually:
pip install requests

# Pull models
ollama pull llama3.1          # 8B model (~4.7GB)
ollama pull llama3.1:70b      # 70B model (~40GB, needs 48GB RAM)
ollama pull mistral           # 7B model (~4.1GB)
ollama pull qwen2:1.5b        # 1.5B model (~934MB, very fast)
ollama pull codellama         # Code model (~3.8GB)

# Start server
ollama serve

# List installed models
ollama list
```

### 3.7.2 API Providers

**OpenAI:**
```bash
pip install "ragbench-ai[generate-openai]"
# Or manually:
pip install openai

# Set API key
export OPENAI_API_KEY="sk-..."
```

**Anthropic (Claude):**
```bash
pip install "ragbench-ai[generate-anthropic]"
# Or manually:
pip install anthropic

# Set API key
export ANTHROPIC_API_KEY="sk-ant-..."
```

**Google Gemini:**
```bash
pip install "ragbench-ai[generate-gemini]"
# Or manually:
pip install google-generativeai

# Set API key
export GOOGLE_API_KEY="..."
```

**Cohere:**
```bash
pip install cohere

export COHERE_API_KEY="..."
```

**Mistral AI:**
```bash
pip install mistralai

export MISTRAL_API_KEY="..."
```

**Groq:**
```bash
# No separate package needed
export GROQ_API_KEY="gsk-..."
```

**Together AI:**
```bash
# No separate package needed
export TOGETHER_API_KEY="..."
```

**OpenRouter:**
```bash
# No separate package needed
export OPENROUTER_API_KEY="sk-or-..."
```

### Complete Layer 7 Installation

```bash
# All generation providers
pip install "ragbench-ai[generate-all]"

# Includes:
# - Ollama support (requests)
# - OpenAI (openai)
# - Anthropic (anthropic)
# - Gemini (google-generativeai)
```

---

# 4. Use Case Based Installation

## 4.1 Local RAG System (No API Costs)

```bash
# 1. Install RAGBench with local models
pip install "ragbench-ai[embed-local,generate-ollama]"

# 2. Install Ollama
curl -fsSL https://ollama.ai/install.sh | sh  # Linux/macOS
# Windows: https://ollama.ai

# 3. Pull models
ollama pull llama3.1
ollama pull nomic-embed-text

# 4. Start Ollama
ollama serve

# Total download: ~7GB
# No API costs!
```

## 4.2 Cloud-Based RAG (Best Quality)

```bash
# Install base + API dependencies
pip install "ragbench-ai[generate-openai]"

# Set API keys
export OPENAI_API_KEY="sk-..."
export COHERE_API_KEY="..."  # For reranking (optional)

# That's it! No models to download
# Pay per use
```

## 4.3 Hybrid (Local + Cloud)

```bash
# Local embeddings, cloud LLMs
pip install "ragbench-ai[embed-local,generate-openai]"

# Or cloud embeddings, local LLMs
pip install "ragbench-ai[generate-ollama]"
ollama pull llama3.1

export OPENAI_API_KEY="sk-..."  # For embeddings
```

## 4.4 Document Processing Heavy

```bash
# Enhanced PDF, Office, OCR support
pip install ragbench-ai \
    pytesseract pdf2image pillow \
    python-docx openpyxl python-pptx \
    unstructured

# System dependencies (Linux):
sudo apt-get install tesseract-ocr poppler-utils

# macOS:
brew install tesseract poppler
```

## 4.5 Code Search System

```bash
# Base + code-specific embeddings
pip install "ragbench-ai[embed-local]"

# Download code embedder
# Will be downloaded on first use: jinaai/jina-embeddings-v2-base-code
```

## 4.6 Multi-Modal RAG (Images, Audio, Video)

```bash
# Full multimodal support
pip install "ragbench-ai[embed-multimodal,embed-multivector]"

# Includes:
# - CLIP (text + images)
# - CLAP (text + audio)
# - ColPali (document images)
# - Video frame processing

# Total download: ~8GB
```

## 4.7 Production System (High Scale)

```bash
# Full installation
pip install "ragbench-ai[all]"

# External services:
# - Qdrant/Pinecone (vector store)
# - OpenAI/Anthropic (embeddings + LLM)
# - Cohere (reranking)
# - PostgreSQL (metadata)
# - Redis (caching)

# Set all API keys
export OPENAI_API_KEY="..."
export COHERE_API_KEY="..."
export PINECONE_API_KEY="..."
# etc.
```

## 4.8 Research/Academic Use

```bash
# Local models + evaluation tools
pip install "ragbench-ai[embed-local,generate-ollama,eval-all]"

ollama pull llama3.1

# For analysis
pip install jupyter matplotlib pandas scikit-learn
```

## 4.9 Minimal Development (Testing)

```bash
# Bare minimum for testing
pip install ragbench-ai

# Use hash_baseline + BM25 + memory store
# No external dependencies
# Perfect for CI/CD
```

---

# 5. Platform-Specific Requirements

## 5.1 Linux (Ubuntu/Debian)

### System Packages

```bash
# Update package list
sudo apt-get update

# Python and dev tools
sudo apt-get install -y python3 python3-pip python3-venv

# For PDF/OCR
sudo apt-get install -y tesseract-ocr poppler-utils

# For compilation (some Python packages)
sudo apt-get install -y build-essential python3-dev

# For PostgreSQL
sudo apt-get install -y postgresql postgresql-contrib

# For Redis
sudo apt-get install -y redis-server

# For MongoDB
sudo apt-get install -y mongodb
```

### GPU Support (NVIDIA)

```bash
# Check if NVIDIA GPU is available
lspci | grep -i nvidia

# Install NVIDIA drivers
sudo apt-get install -y nvidia-driver-535

# Install CUDA toolkit
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda

# Install cuDNN (for deep learning)
# Download from https://developer.nvidia.com/cudnn

# Verify
nvidia-smi
nvcc --version

# Install PyTorch with CUDA
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
```

## 5.2 macOS

### Homebrew (Package Manager)

```bash
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install dependencies
brew install python@3.11
brew install tesseract poppler
brew install postgresql redis mongodb-community

# For Apple Silicon (M1/M2/M3)
# PyTorch with MPS support
pip install torch torchvision
```

### Apple Silicon Specific

```bash
# Check if on Apple Silicon
uname -m  # Should show "arm64"

# Install Rosetta (if needed for compatibility)
softwareupdate --install-rosetta

# Some packages may need architecture specification
arch -arm64 brew install package_name
```

## 5.3 Windows

### Python Installation

```powershell
# Download Python from python.org
# Or use winget:
winget install Python.Python.3.11

# Verify
python --version
```

### Tesseract & Poppler

```powershell
# Download Tesseract installer
# https://github.com/UB-Mannheim/tesseract/wiki

# Download Poppler
# https://github.com/oschwartz10612/poppler-windows/releases

# Add to PATH or set environment variables
$env:TESSERACT_CMD = "C:\Program Files\Tesseract-OCR\tesseract.exe"
$env:POPPLER_PATH = "C:\poppler\bin"

# Or add to system PATH permanently via System Properties > Environment Variables
```

### Visual C++ Build Tools

```powershell
# Some packages need C++ compiler
# Download from: https://visualstudio.microsoft.com/visual-cpp-build-tools/

# Or install via Visual Studio installer
# Select "Desktop development with C++"
```

### WSL2 (Optional, for Linux compatibility)

```powershell
# Enable WSL2
wsl --install

# Install Ubuntu
wsl --install -d Ubuntu

# Then follow Linux instructions inside WSL
```

## 5.4 Docker (All Platforms)

### RAGBench in Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim

# Install system dependencies
RUN apt-get update && apt-get install -y \
    tesseract-ocr \
    poppler-utils \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Install RAGBench
RUN pip install --no-cache-dir "ragbench-ai[embed-local,generate-ollama]"

# Set working directory
WORKDIR /app

# Copy your code
COPY . .

# Run
CMD ["python", "your_script.py"]
```

```bash
# Build
docker build -t ragbench .

# Run
docker run -v $(pwd):/app \
    -e OPENAI_API_KEY=$OPENAI_API_KEY \
    ragbench
```

### Docker Compose Setup

```yaml
# docker-compose.yml
version: '3.8'

services:
  ragbench:
    build: .
    volumes:
      - ./data:/app/data
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    depends_on:
      - qdrant
      - redis

  qdrant:
    image: qdrant/qdrant:latest
    ports:
      - "6333:6333"
    volumes:
      - qdrant_data:/qdrant/storage

  redis:
    image: redis:latest
    ports:
      - "6379:6379"

volumes:
  qdrant_data:
```

```bash
# Start all services
docker-compose up -d
```

---

# 6. External Services Setup

## 6.1 Ollama (Local LLM)

### Installation

**macOS/Linux:**
```bash
curl -fsSL https://ollama.ai/install.sh | sh
```

**Windows:**
- Download from https://ollama.ai

### Configuration

```bash
# Start server
ollama serve

# Default settings
# Host: http://localhost:11434
# Models stored in: ~/.ollama/models

# Custom host
OLLAMA_HOST=0.0.0.0:11434 ollama serve

# Custom model directory
OLLAMA_MODELS=/path/to/models ollama serve
```

### Pull Models

```bash
# List available models
ollama list

# Pull models
ollama pull llama3.1          # 8B, 4.7GB
ollama pull llama3.1:70b      # 70B, 40GB
ollama pull mistral           # 7B, 4.1GB
ollama pull qwen2:1.5b        # 1.5B, 934MB
ollama pull codellama         # 7B, 3.8GB
ollama pull nomic-embed-text  # Embedding model

# Delete model
ollama rm model_name

# Show model info
ollama show llama3.1
```

### Test

```bash
# Test chat
ollama run llama3.1 "Hello, how are you?"

# Test via API
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1",
  "prompt": "Why is the sky blue?"
}'
```

## 6.2 Vector Database Setup

### Qdrant (Local)

```bash
# Docker
docker run -p 6333:6333 \
    -v $(pwd)/qdrant_data:/qdrant/storage \
    qdrant/qdrant:latest

# Or install directly (Linux)
wget https://github.com/qdrant/qdrant/releases/download/v1.7.0/qdrant-x86_64-unknown-linux-gnu.tar.gz
tar -xzf qdrant-x86_64-unknown-linux-gnu.tar.gz
./qdrant

# Web UI: http://localhost:6333/dashboard
```

### Qdrant Cloud

```bash
# Sign up at https://cloud.qdrant.io

# Get cluster URL and API key
export QDRANT_URL="https://xyz-123.cloud.qdrant.io"
export QDRANT_API_KEY="your_api_key"
```

### Pinecone

```bash
# Sign up at https://www.pinecone.io

# Get API key from dashboard
export PINECONE_API_KEY="your_api_key"

# Create index via dashboard or API
```

### Weaviate

```bash
# Docker
docker run -p 8080:8080 \
    -e AUTHENTICATION_ANONYMOUS_ACCESS_ENABLED=true \
    -e PERSISTENCE_DATA_PATH=/var/lib/weaviate \
    semitechnologies/weaviate:latest

# Or Weaviate Cloud
# Sign up at https://console.weaviate.cloud
export WEAVIATE_URL="https://xyz.weaviate.network"
export WEAVIATE_API_KEY="your_api_key"
```

## 6.3 Database Setup

### PostgreSQL with pgvector

```bash
# Install PostgreSQL
# Ubuntu:
sudo apt-get install postgresql postgresql-contrib

# macOS:
brew install postgresql@16

# Start PostgreSQL
sudo service postgresql start  # Linux
brew services start postgresql@16  # macOS

# Install pgvector extension
# Ubuntu:
sudo apt install postgresql-16-pgvector

# macOS:
brew install pgvector

# Create database and enable extension
psql -U postgres
CREATE DATABASE ragbench;
\c ragbench
CREATE EXTENSION vector;

# Set connection
export PGVECTOR_DSN="postgresql://postgres:password@localhost/ragbench"
```

### Redis

```bash
# Install
# Ubuntu:
sudo apt-get install redis-server

# macOS:
brew install redis

# Start
sudo service redis-server start  # Linux
brew services start redis  # macOS

# Test
redis-cli ping  # Should return PONG

# Set URL
export REDIS_URL="redis://localhost:6379"
```

## 6.4 Cloud Storage Setup

### AWS S3

```bash
# Install AWS CLI
pip install awscli

# Configure
aws configure
# AWS Access Key ID: [your_key]
# AWS Secret Access Key: [your_secret]
# Default region name: us-east-1
# Default output format: json

# Or set environment variables
export AWS_ACCESS_KEY_ID="your_key"
export AWS_SECRET_ACCESS_KEY="your_secret"
export AWS_REGION="us-east-1"

# Test
aws s3 ls
```

### Google Cloud Storage

```bash
# Install gcloud CLI
# Follow: https://cloud.google.com/sdk/docs/install

# Authenticate
gcloud auth application-default login

# Or use service account
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account-key.json"

# Test
gcloud storage ls
```

---

# 7. Verification & Testing

## 7.1 Verify Installation

```python
# test_installation.py

import sys

def test_ragbench():
    """Test RAGBench installation."""
    print("Testing RAGBench installation...")
    
    try:
        import ragbench
        print(f"✓ RAGBench version: {ragbench.__version__}")
    except ImportError as e:
        print(f"✗ RAGBench not installed: {e}")
        return False
    
    return True

def test_embedders():
    """Test available embedders."""
    print("\nTesting embedders...")
    
    try:
        from ragbench.embedding import REGISTRY
        
        embedders = REGISTRY.list_names()
        print(f"✓ Found {len(embedders)} embedders")
        
        # Test hash_baseline (always available)
        from ragbench.embedding import Embedder
        embedder = Embedder("hash_baseline")
        vec = embedder.embed(["test"])[0]
        print(f"✓ Hash baseline works: {len(vec.vector)} dimensions")
        
        # Test local embedders if available
        try:
            embedder = Embedder("bge")
            if embedder.is_available():
                print("✓ Local embedders (BGE) available")
            else:
                print("⚠ Local embedders not available (install with [embed-local])")
        except:
            print("⚠ Local embedders not installed")
        
    except Exception as e:
        print(f"✗ Embedder test failed: {e}")
        return False
    
    return True

def test_vector_stores():
    """Test available vector stores."""
    print("\nTesting vector stores...")
    
    try:
        from ragbench.vectorstore import VectorStore
        
        # Test memory store (always available)
        store = VectorStore("memory")
        print("✓ Memory store available")
        
        # Test SQLite
        try:
            store = VectorStore("sqlite", path=":memory:")
            print("✓ SQLite store available")
        except:
            print("⚠ SQLite store not available")
        
    except Exception as e:
        print(f"✗ Vector store test failed: {e}")
        return False
    
    return True

def test_generation():
    """Test generation layer."""
    print("\nTesting generation...")
    
    try:
        from ragbench.generation import Generator
        
        # Test Ollama
        try:
            gen = Generator(method="ollama")
            if gen.is_available():
                print("✓ Ollama available")
            else:
                print("⚠ Ollama not running (start with 'ollama serve')")
        except:
            print("⚠ Ollama not installed")
        
    except Exception as e:
        print(f"✗ Generation test failed: {e}")
        return False
    
    return True

def test_pipeline():
    """Test pipeline functions."""
    print("\nTesting pipeline...")
    
    try:
        from ragbench.pipeline import auto, ask
        
        # Test chunking
        chunks = auto("Hello world test", as_text=True)
        print(f"✓ Pipeline auto() works: {len(chunks)} chunks")
        
    except Exception as e:
        print(f"✗ Pipeline test failed: {e}")
        return False
    
    return True

def main():
    print("=" * 60)
    print("RAGBench Installation Verification")
    print("=" * 60)
    
    results = []
    results.append(("RAGBench", test_ragbench()))
    results.append(("Embedders", test_embedders()))
    results.append(("Vector Stores", test_vector_stores()))
    results.append(("Generation", test_generation()))
    results.append(("Pipeline", test_pipeline()))
    
    print("\n" + "=" * 60)
    print("Summary:")
    print("=" * 60)
    
    for name, passed in results:
        status = "✓ PASS" if passed else "✗ FAIL"
        print(f"{name}: {status}")
    
    all_passed = all(r[1] for r in results)
    
    if all_passed:
        print("\n✓ All tests passed! RAGBench is ready to use.")
    else:
        print("\n⚠ Some tests failed. Check output above for details.")
    
    return 0 if all_passed else 1

if __name__ == "__main__":
    sys.exit(main())
```

**Run verification:**

```bash
python test_installation.py
```

## 7.2 Quick Functionality Test

```python
# quick_test.py

from ragbench.pipeline import auto, ask

# Test 1: Index text
print("Test 1: Indexing...")
result = auto(
    "Machine learning is a subset of artificial intelligence.",
    as_text=True,
    embed=True,
    embedder="hash_baseline",
    index=True,
    store="memory",
    collection="test"
)
print(f"✓ Indexed {len(result.ids)} chunks")

# Test 2: Query (requires Ollama)
print("\nTest 2: Querying...")
try:
    answer = ask(
        "What is machine learning?",
        collection="test",
        store="memory",
        embedder="hash_baseline",
        generation_method="ollama"
    )
    print(f"✓ Answer: {answer.answer[:100]}...")
except Exception as e:
    print(f"⚠ Query failed: {e}")
    print("   (Ollama might not be running)")

print("\n✓ Quick test complete!")
```

## 7.3 Check System Resources

```python
# check_resources.py

import psutil
import torch

def check_resources():
    print("System Resources:")
    print("=" * 50)
    
    # CPU
    cpu_count = psutil.cpu_count()
    cpu_percent = psutil.cpu_percent(interval=1)
    print(f"CPU: {cpu_count} cores, {cpu_percent}% used")
    
    # RAM
    ram = psutil.virtual_memory()
    print(f"RAM: {ram.total / (1024**3):.1f} GB total, {ram.percent}% used")
    
    # Disk
    disk = psutil.disk_usage('/')
    print(f"Disk: {disk.total / (1024**3):.1f} GB total, {disk.percent}% used")
    
    # GPU
    if torch.cuda.is_available():
        print(f"\nGPU: {torch.cuda.get_device_name(0)}")
        print(f"CUDA version: {torch.version.cuda}")
        print(f"GPU memory: {torch.cuda.get_device_properties(0).total_memory / (1024**3):.1f} GB")
    else:
        print("\nGPU: Not available")
    
    # Apple Silicon
    if torch.backends.mps.is_available():
        print("\nApple Silicon (MPS): Available")
    
    print("\nRecommendations:")
    if ram.total < 8 * (1024**3):
        print("⚠ Consider using API-based embeddings (low RAM)")
    if not torch.cuda.is_available():
        print("ℹ GPU not available - using CPU (slower for local models)")

if __name__ == "__main__":
    check_resources()
```

---

# 8. Troubleshooting Installation Issues

## 8.1 Common Issues

### "pip install fails"

```bash
# Update pip
python -m pip install --upgrade pip

# Clear cache
pip cache purge

# Install with --no-cache-dir
pip install --no-cache-dir ragbench-ai

# Try different Python version
python3.11 -m pip install ragbench-ai
```

### "torch not found"

```bash
# Install PyTorch separately
pip install torch torchvision

# For GPU (CUDA 11.8)
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118

# Verify
python -c "import torch; print(torch.__version__)"
```

### "Microsoft Visual C++ required" (Windows)

```powershell
# Download and install Visual C++ Build Tools
# https://visualstudio.microsoft.com/visual-cpp-build-tools/

# Or use pre-built wheels
pip install --only-binary :all: package_name
```

### "Tesseract not found"

```bash
# Linux
sudo apt-get install tesseract-ocr

# macOS
brew install tesseract

# Windows
# Download installer from:
# https://github.com/UB-Mannheim/tesseract/wiki

# Set path
export TESSERACT_CMD=/usr/bin/tesseract  # Linux/Mac
set TESSERACT_CMD=C:\Program Files\Tesseract-OCR\tesseract.exe  # Windows
```

### "CUDA out of memory"

```python
# Use CPU
embedder = Embedder("bge", device="cpu")

# Or use smaller model
embedder = Embedder("minilm")  # Instead of bge-large

# Or reduce batch size
embedder.embed(texts, batch_size=8)
```

### "Package conflict"

```bash
# Create fresh virtual environment
python -m venv fresh_env
source fresh_env/bin/activate  # Linux/Mac
fresh_env\Scripts\activate  # Windows

# Install from scratch
pip install ragbench-ai
```

## 8.2 Getting Help

### Check Version

```bash
pip show ragbench-ai
python -c "import ragbench; print(ragbench.__version__)"
```

### Check Dependencies

```bash
pip list | grep -E "torch|transformers|sentence"
```

### Report Issue

When reporting issues, include:

```bash
# System info
python --version
pip --version
uname -a  # Linux/Mac
systeminfo  # Windows

# Package versions
pip freeze > requirements_frozen.txt

# Error traceback
python your_script.py 2>&1 | tee error_log.txt
```

---

## Summary: Installation Checklists

### ✅ Minimal Setup (5 minutes)
- [ ] Python 3.9+
- [ ] `pip install ragbench-ai`
- [ ] Test with hash_baseline embedder

### ✅ Local RAG Setup (20 minutes)
- [ ] Python 3.9+
- [ ] `pip install "ragbench-ai[embed-local,generate-ollama]"`
- [ ] Install Ollama
- [ ] `ollama pull llama3.1`
- [ ] `ollama pull nomic-embed-text`
- [ ] Test full pipeline

### ✅ Cloud RAG Setup (5 minutes)
- [ ] Python 3.9+
- [ ] `pip install "ragbench-ai[generate-openai]"`
- [ ] Set `OPENAI_API_KEY`
- [ ] Set `COHERE_API_KEY` (optional)
- [ ] Test with API providers

### ✅ Production Setup (1-2 hours)
- [ ] Python 3.11+
- [ ] `pip install "ragbench-ai[all]"`
- [ ] Setup vector store (Qdrant/Pinecone)
- [ ] Setup PostgreSQL with pgvector
- [ ] Setup Redis for caching
- [ ] Configure all API keys
- [ ] Install monitoring tools
- [ ] Test at scale

---

**Need help?** Check the [Troubleshooting Guide](#8-troubleshooting-installation-issues) or open an issue on GitHub.

**Ready to start?** Check out the [Quick Start Guide](../getting-started/quickstart.md) or [Comprehensive Guide](COMPREHENSIVE_GUIDE.md)!
