# RAGBench Comprehensive Guide - Part 2

## Table of Contents (Part 2)

14. [Advanced Topics](#14-advanced-topics)
15. [Configuration Reference](#15-configuration-reference)
16. [Troubleshooting Guide](#16-troubleshooting-guide)
17. [Best Practices](#17-best-practices)
18. [Contributing to RAGBench](#18-contributing-to-ragbench)
19. [Complete API Reference](#19-complete-api-reference)
20. [Appendices](#20-appendices)

---

# 14. Advanced Topics

## 14.1 Hybrid Search Strategies

### 14.1.1 Understanding Hybrid Search

Hybrid search combines:
- **Dense (semantic)**: Meaning-based similarity
- **Sparse (lexical)**: Keyword matching

```python
from ragbench.retrieval import retrieve

# Hybrid with RRF (Reciprocal Rank Fusion)
results = retrieve(
    query="machine learning algorithms",
    method="hybrid_rrf",
    embedder=embedder,
    store=store,
    collection="docs",
    k=20
)
```

### 14.1.2 Tuning Hybrid Weights

```python
# Experiment with different weights
weights = [
    (0.3, 0.7),  # More lexical
    (0.5, 0.5),  # Balanced
    (0.7, 0.3),  # More semantic
]

for dense_w, sparse_w in weights:
    results = retrieve(
        query=query,
        method="hybrid_weighted",
        dense_weight=dense_w,
        sparse_weight=sparse_w,
        k=10
    )
    print(f"Dense={dense_w}, Sparse={sparse_w}")
    print(f"  Top result: {results[0].content[:80]}...")
    print(f"  Score: {results[0].score:.3f}\n")
```

### 14.1.3 Query-Adaptive Weighting

```python
def adaptive_hybrid_search(query, embedder, store, collection):
    """Adjust weights based on query characteristics."""
    
    # Short queries: more lexical
    if len(query.split()) < 4:
        dense_weight = 0.3
        sparse_weight = 0.7
    
    # Questions: more semantic
    elif query.lower().startswith(('what', 'how', 'why', 'when')):
        dense_weight = 0.7
        sparse_weight = 0.3
    
    # Technical/code: more lexical
    elif any(c in query for c in ['(', ')', '{', '}', ';']):
        dense_weight = 0.2
        sparse_weight = 0.8
    
    # Default: balanced
    else:
        dense_weight = 0.5
        sparse_weight = 0.5
    
    return retrieve(
        query=query,
        method="hybrid_weighted",
        embedder=embedder,
        store=store,
        collection=collection,
        dense_weight=dense_weight,
        sparse_weight=sparse_weight,
        k=10
    )
```

## 14.2 Multi-Vector Retrieval

### 14.2.1 ColBERT Late Interaction

```python
from ragbench.embedding import Embedder
from ragbench.retrieval import retrieve

# ColBERT embedder (multi-vector)
embedder = Embedder("colbert", model="answerdotai/answerai-colbert-small-v1")

# Embed documents (multiple vectors per doc)
docs = ["Document 1 text", "Document 2 text"]
embedded = embedder.embed_documents(docs)

for emb in embedded:
    print(f"Document has {len(emb.multi_vector)} vectors")

# Search with maxsim
results = retrieve(
    query="search query",
    method="multi_vector",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10
)
```

### 14.2.2 Custom Late Interaction

```python
import numpy as np

def maxsim_score(query_vecs, doc_vecs):
    """
    MaxSim: For each query vector, find max similarity with doc vectors.
    Sum these max similarities.
    """
    scores = []
    
    for q_vec in query_vecs:
        # Compute similarity with all doc vectors
        sims = [np.dot(q_vec, d_vec) for d_vec in doc_vecs]
        # Take max
        scores.append(max(sims))
    
    # Sum maxs
    return sum(scores)

# Use custom scoring
query_embedding = embedder.embed_query(query)
doc_embeddings = embedder.embed_documents(docs)

scores = []
for doc_emb in doc_embeddings:
    score = maxsim_score(query_embedding.multi_vector, doc_emb.multi_vector)
    scores.append((doc_emb.content, score))

# Sort by score
ranked = sorted(scores, key=lambda x: x[1], reverse=True)
```

## 14.3 Document Hierarchies

### 14.3.1 Parent-Child Chunking

```python
from ragbench.chunking import chunk

# Create hierarchical chunks
chunks = chunk(
    long_document,
    method="Hierarchical Chunking",
    parent_size=2000,  # Large context
    child_size=500,    # Precise retrieval
    overlap=50
)

# Children reference parents
for c in chunks:
    if "parent_id" in c.metadata:
        print(f"Child {c.id} -> Parent {c.metadata['parent_id']}")
```

### 14.3.2 Retrieve Children, Return Parents

```python
from ragbench.retrieval import retrieve

# Search with child chunks (precise)
results = retrieve(
    query="specific detail",
    method="dense",
    embedder=embedder,
    store=store,
    collection="docs",
    k=10
)

# Expand to parent chunks (context)
def expand_to_parents(results, store):
    """Replace children with their parents."""
    parent_ids = [r.metadata.get("parent_id") for r in results]
    parent_ids = [pid for pid in parent_ids if pid]
    
    if not parent_ids:
        return results
    
    # Fetch parents
    parents = store.get(ids=parent_ids, collection="docs")
    return parents

expanded = expand_to_parents(results, store)
```

### 14.3.3 Sentence Windows

```python
# Store sentences with context markers
chunks = chunk(
    document,
    method="Sentence Chunking",
    chunk_size=1,  # One sentence per chunk
    metadata_augmentation={
        "surrounding_sentences": 3  # Track neighbors
    }
)

# Retrieve sentence, expand window
results = retrieve(query, method="dense", k=10)

def expand_window(results, window_size=2):
    """Expand each result to include surrounding sentences."""
    expanded = []
    
    for r in results:
        # Get surrounding sentence IDs
        start_id = r.metadata.get("sentence_id") - window_size
        end_id = r.metadata.get("sentence_id") + window_size
        
        # Fetch surrounding sentences
        sentence_ids = range(start_id, end_id + 1)
        sentences = store.get(ids=[f"sent_{i}" for i in sentence_ids])
        
        # Combine
        expanded_content = " ".join([s.content for s in sentences])
        r.content = expanded_content
        expanded.append(r)
    
    return expanded
```

## 14.4 Query Understanding

### 14.4.1 Query Classification

```python
def classify_query(query):
    """Classify query type to choose retrieval strategy."""
    
    query_lower = query.lower()
    
    # Factoid question
    if query_lower.startswith(('what is', 'who is', 'when was', 'where is')):
        return "factoid"
    
    # How-to question
    elif query_lower.startswith(('how to', 'how do i', 'how can i')):
        return "how_to"
    
    # Comparison
    elif any(word in query_lower for word in ['compare', 'difference', 'vs', 'versus']):
        return "comparison"
    
    # Definition
    elif any(word in query_lower for word in ['define', 'definition', 'meaning']):
        return "definition"
    
    # List
    elif query_lower.startswith(('list', 'what are')) and 'examples' in query_lower:
        return "list"
    
    # Explanation
    elif query_lower.startswith(('explain', 'why')):
        return "explanation"
    
    # Keyword search
    elif len(query.split()) < 4:
        return "keyword"
    
    return "general"

# Route based on type
query_type = classify_query(user_query)

strategies = {
    "factoid": "dense",
    "how_to": "multi_query",
    "comparison": "decomposition",
    "definition": "dense",
    "list": "mmr",  # Diversity
    "explanation": "hybrid_rrf",
    "keyword": "sparse",
    "general": "hybrid_rrf"
}

method = strategies.get(query_type, "hybrid_rrf")
results = retrieve(query=user_query, method=method, ...)
```

### 14.4.2 Query Expansion

```python
from ragbench.generation import Generator

def expand_query(query, llm):
    """Generate query variations."""
    
    prompt = f"""Generate 3 variations of this search query:
    
    Original: {query}
    
    Variations should:
    1. Use synonyms
    2. Rephrase
    3. Add context
    
    Output only the variations, one per line."""
    
    response = llm.generate_text(prompt)
    variations = [q.strip() for q in response.split('\n') if q.strip()]
    
    return [query] + variations[:3]

# Use expanded queries
generator = Generator(method="openai")
queries = expand_query("machine learning", generator)

all_results = []
for q in queries:
    results = retrieve(q, method="dense", k=10)
    all_results.extend(results)

# Deduplicate and rerank
from ragbench.reranking import rerank
final = rerank(query, all_results, method="bm25", k=10)
```

### 14.4.3 Intent Detection

```python
def detect_intent(query):
    """Detect user intent."""
    
    query_lower = query.lower()
    
    intents = {
        "search": ["find", "search", "look for", "show me"],
        "question": ["what", "how", "why", "when", "where", "who"],
        "action": ["create", "generate", "make", "build"],
        "comparison": ["compare", "difference", "vs", "better"],
        "recommendation": ["recommend", "suggest", "should i"],
    }
    
    for intent, keywords in intents.items():
        if any(kw in query_lower for kw in keywords):
            return intent
    
    return "unknown"

# Adjust pipeline based on intent
intent = detect_intent(user_query)

if intent == "search":
    # Simple retrieval
    results = retrieve(user_query, method="hybrid_rrf", k=10)
    
elif intent == "question":
    # Full RAG
    answer = ask(user_query, collection="docs")
    
elif intent == "comparison":
    # Use decomposition
    results = retrieve(user_query, method="decomposition", k=20)
    
elif intent == "recommendation":
    # Use diversity
    results = retrieve(user_query, method="mmr", k=10)
```

## 14.5 Context Management

### 14.5.1 Context Window Optimization

```python
def optimize_context(results, max_tokens=4000):
    """Fit results into context window."""
    
    # Estimate tokens (rough: 1 token ≈ 4 chars)
    def estimate_tokens(text):
        return len(text) // 4
    
    selected = []
    total_tokens = 0
    
    for r in results:
        chunk_tokens = estimate_tokens(r.content)
        
        if total_tokens + chunk_tokens <= max_tokens:
            selected.append(r)
            total_tokens += chunk_tokens
        else:
            # Truncate if space remains
            remaining = max_tokens - total_tokens
            if remaining > 50:  # Minimum useful size
                truncated = r.content[:remaining * 4]
                r.content = truncated
                selected.append(r)
            break
    
    return selected

# Use optimized context
results = retrieve(query, method="hybrid_rrf", k=50)
optimized = optimize_context(results, max_tokens=3000)

answer = generator.generate(query, context=optimized)
```

### 14.5.2 Relevance-Based Selection

```python
def select_by_relevance(results, score_threshold=0.7, max_count=10):
    """Select top results by score threshold."""
    
    # Filter by threshold
    filtered = [r for r in results if r.score >= score_threshold]
    
    # Limit count
    selected = filtered[:max_count]
    
    # If too few, lower threshold
    if len(selected) < 3:
        score_threshold *= 0.8
        selected = [r for r in results if r.score >= score_threshold][:max_count]
    
    return selected

results = retrieve(query, method="dense", k=100)
selected = select_by_relevance(results, score_threshold=0.75, max_count=5)
```

### 14.5.3 Diversity in Context

```python
from ragbench.retrieval import apply_diversity

# Retrieve many
results = retrieve(query, method="hybrid_rrf", k=50)

# Apply MMR for diversity
diverse = apply_diversity(
    results,
    method="mmr",
    lambda_param=0.6,  # Balance relevance/diversity
    k=10
)

# Use diverse context
answer = generator.generate(query, context=diverse)
```

## 14.6 Streaming & Real-Time

### 14.6.1 Streaming Generation

```python
from ragbench.generation import Generator

generator = Generator(method="ollama", model="llama3.1")

# Stream tokens
for token in generator.generate_stream(query, context=results):
    print(token, end="", flush=True)
    
    # Can process each token
    # e.g., send to frontend via WebSocket
```

### 14.6.2 Progressive Loading

```python
import asyncio

async def progressive_rag(query, collection):
    """Progressive RAG: show results as they arrive."""
    
    # Start retrieval
    retrieval_task = asyncio.create_task(
        async_retrieve(query, collection)
    )
    
    # Show loading message
    yield {"status": "retrieving", "message": "Finding relevant documents..."}
    
    # Wait for retrieval
    results = await retrieval_task
    
    yield {"status": "retrieved", "count": len(results)}
    
    # Start generation
    generation_task = asyncio.create_task(
        async_generate(query, results)
    )
    
    yield {"status": "generating", "message": "Generating answer..."}
    
    # Stream answer tokens
    async for token in generation_task:
        yield {"status": "token", "token": token}
    
    yield {"status": "complete"}

# Usage with WebSocket
async for update in progressive_rag(user_query, "docs"):
    await websocket.send_json(update)
```

### 14.6.3 Real-Time Index Updates

```python
from ragbench.vectorstore import VectorStore
from ragbench.embedding import Embedder

store = VectorStore("qdrant", url="...")
embedder = Embedder("openai")

def add_document_realtime(document, collection):
    """Add document without blocking queries."""
    
    # Chunk
    chunks = chunk(document, method="auto")
    
    # Embed
    embeddings = embedder.embed(chunks)
    
    # Add to store (non-blocking)
    store.add(embeddings, collection=collection)
    
    return len(embeddings)

# Usage
add_document_realtime(new_document, "docs")
# Immediately available for queries
```

## 14.7 Multi-Modal RAG

### 14.7.1 Text + Images

```python
from ragbench.embedding import Embedder
from PIL import Image

# CLIP embedder (text + images)
embedder = Embedder("clip", model="openai/clip-vit-base-patch32")

# Embed text
text_vec = embedder.embed_query("a cat sitting on a couch")

# Embed image
image = Image.open("cat_on_couch.jpg")
image_vec = embedder.embed(image)[0]

# Compare
from numpy import dot
from numpy.linalg import norm

similarity = dot(text_vec.vector, image_vec.vector) / (
    norm(text_vec.vector) * norm(image_vec.vector)
)

print(f"Similarity: {similarity:.3f}")
```

### 14.7.2 Document Images (ColPali)

```python
# ColPali: Multi-vector for document pages
embedder = Embedder("colpali", model="vidore/colpali-v1.3")

# Embed document page
page_image = Image.open("document_page.png")
page_vec = embedder.embed(page_image)[0]

print(f"Page has {len(page_vec.multi_vector)} vectors")

# Search across document pages
results = retrieve(
    query="contract terms",
    method="multi_vector",
    embedder=embedder,
    store=store,
    collection="documents",
    k=5
)
```

### 14.7.3 Audio + Text

```python
# CLAP embedder (audio + text)
embedder = Embedder("clap", model="laion/clap-htsat-unfused")

# Embed audio
import librosa
audio, sr = librosa.load("speech.wav")
audio_vec = embedder.embed(audio)[0]

# Embed text query
query_vec = embedder.embed_query("discussion about climate change")

# Find similarity
similarity = dot(query_vec.vector, audio_vec.vector)
```

## 14.8 Custom Scoring

### 14.8.1 Metadata Boosting

```python
def score_with_metadata_boost(results):
    """Boost scores based on metadata."""
    
    boosted = []
    
    for r in results:
        score = r.score
        
        # Boost recent documents
        if r.metadata.get("year", 0) >= 2023:
            score *= 1.2
        
        # Boost authoritative sources
        if r.metadata.get("source_type") == "official":
            score *= 1.5
        
        # Boost highly cited
        citations = r.metadata.get("citations", 0)
        if citations > 100:
            score *= 1.1
        
        r.score = score
        boosted.append(r)
    
    # Re-sort by boosted score
    boosted.sort(key=lambda x: x.score, reverse=True)
    
    return boosted

results = retrieve(query, method="dense", k=20)
boosted = score_with_metadata_boost(results)
```

### 14.8.2 Learning to Rank

```python
from sklearn.ensemble import GradientBoostingClassifier
import numpy as np

class LTRReranker:
    """Learning to Rank reranker."""
    
    def __init__(self):
        self.model = GradientBoostingClassifier()
        self.is_trained = False
    
    def extract_features(self, query, result):
        """Extract features for ranking."""
        
        features = [
            result.score,  # Original score
            len(result.content),  # Length
            result.content.lower().count(query.lower()),  # Query frequency
            result.metadata.get("year", 2000),  # Recency
            result.metadata.get("citations", 0),  # Citations
            1 if result.metadata.get("source_type") == "official" else 0,  # Authority
        ]
        
        return features
    
    def train(self, queries, results_list, labels):
        """Train on labeled data."""
        
        X = []
        y = []
        
        for query, results, relevance_labels in zip(queries, results_list, labels):
            for result, label in zip(results, relevance_labels):
                features = self.extract_features(query, result)
                X.append(features)
                y.append(label)
        
        self.model.fit(X, y)
        self.is_trained = True
    
    def rerank(self, query, results):
        """Rerank results."""
        
        if not self.is_trained:
            raise ValueError("Model not trained")
        
        # Extract features
        X = [self.extract_features(query, r) for r in results]
        
        # Predict scores
        scores = self.model.predict_proba(X)[:, 1]
        
        # Update scores
        for r, score in zip(results, scores):
            r.score = score
        
        # Sort
        results.sort(key=lambda x: x.score, reverse=True)
        
        return results

# Train
ltr = LTRReranker()
ltr.train(training_queries, training_results, training_labels)

# Use
reranked = ltr.rerank(query, results)
```

## 14.9 Evaluation & Metrics

### 14.9.1 Retrieval Metrics

```python
def evaluate_retrieval(queries, ground_truth, retrieval_fn, k=10):
    """Evaluate retrieval quality."""
    
    metrics = {
        "precision": [],
        "recall": [],
        "f1": [],
        "mrr": [],  # Mean Reciprocal Rank
        "ndcg": []  # Normalized Discounted Cumulative Gain
    }
    
    for query, relevant_ids in zip(queries, ground_truth):
        # Retrieve
        results = retrieval_fn(query, k=k)
        retrieved_ids = [r.id for r in results]
        
        # Precision: % retrieved that are relevant
        relevant_retrieved = set(retrieved_ids) & set(relevant_ids)
        precision = len(relevant_retrieved) / len(retrieved_ids)
        
        # Recall: % relevant that are retrieved
        recall = len(relevant_retrieved) / len(relevant_ids)
        
        # F1
        if precision + recall > 0:
            f1 = 2 * (precision * recall) / (precision + recall)
        else:
            f1 = 0
        
        # MRR: 1 / rank of first relevant
        mrr = 0
        for i, rid in enumerate(retrieved_ids, 1):
            if rid in relevant_ids:
                mrr = 1 / i
                break
        
        # NDCG (simplified)
        # ... (complex calculation)
        
        metrics["precision"].append(precision)
        metrics["recall"].append(recall)
        metrics["f1"].append(f1)
        metrics["mrr"].append(mrr)
    
    # Average
    return {
        "avg_precision": np.mean(metrics["precision"]),
        "avg_recall": np.mean(metrics["recall"]),
        "avg_f1": np.mean(metrics["f1"]),
        "avg_mrr": np.mean(metrics["mrr"])
    }

# Usage
def my_retrieval_fn(query, k):
    return retrieve(
        query, 
        method="hybrid_rrf", 
        embedder=embedder, 
        store=store, 
        k=k
    )

metrics = evaluate_retrieval(test_queries, test_ground_truth, my_retrieval_fn)
print(f"Precision: {metrics['avg_precision']:.3f}")
print(f"Recall: {metrics['avg_recall']:.3f}")
print(f"F1: {metrics['avg_f1']:.3f}")
```

### 14.9.2 Generation Metrics

```python
from ragbench.generation import Generator

def evaluate_generation(queries, ground_truth_answers, context_fn, generator):
    """Evaluate generation quality."""
    
    # Simple metrics
    from difflib import SequenceMatcher
    
    similarities = []
    
    for query, true_answer in zip(queries, ground_truth_answers):
        # Get context
        context = context_fn(query)
        
        # Generate answer
        generated = generator.generate(query, context)
        
        # Compare with ground truth
        similarity = SequenceMatcher(
            None, 
            generated.answer.lower(), 
            true_answer.lower()
        ).ratio()
        
        similarities.append(similarity)
    
    return {
        "avg_similarity": np.mean(similarities),
        "min_similarity": np.min(similarities),
        "max_similarity": np.max(similarities)
    }

# Better: Use LLM-based evaluation
def llm_evaluate(generated_answer, true_answer, evaluator_llm):
    """Use LLM to evaluate answer quality."""
    
    prompt = f"""Rate the quality of this generated answer on a scale of 1-5:
    
    Question: {query}
    Expected Answer: {true_answer}
    Generated Answer: {generated_answer}
    
    Consider:
    - Accuracy
    - Completeness
    - Relevance
    
    Output only the number (1-5):"""
    
    score = evaluator_llm.generate_text(prompt)
    return int(score.strip())
```

---

# 15. Configuration Reference

## 15.1 Environment Variables

### 15.1.1 Framework Settings

```bash
# Default embedder
OMNILOAD_EMBEDDER=openai

# Default vector store
OMNILOAD_VECTORSTORE=sqlite

# Default retrieval method
OMNILOAD_RETRIEVER=hybrid_rrf

# Default collection
OMNILOAD_COLLECTION=default

# Cache directory
OMNILOAD_CACHE_DIR=~/.cache/ragbench

# Enable/disable caching
OMNILOAD_EMBED_CACHE=1

# Vector store path
OMNILOAD_VECTORSTORE_PATH=./data

# Enable live tests
OMNILOAD_LIVE_TESTS=0
```

### 15.1.2 Embedding Providers

```bash
# OpenAI
OPENAI_API_KEY=sk-...

# Azure OpenAI
AZURE_OPENAI_API_KEY=...
AZURE_OPENAI_ENDPOINT=https://...

# Cohere
COHERE_API_KEY=...

# Voyage AI
VOYAGE_API_KEY=...

# Google
GOOGLE_API_KEY=...
GOOGLE_CLOUD_PROJECT=...
GOOGLE_CLOUD_LOCATION=us-central1
GOOGLE_VERTEX_ACCESS_TOKEN=...

# Jina AI
JINA_API_KEY=...

# Mistral AI
MISTRAL_API_KEY=...

# Hugging Face
HF_TOKEN=...

# AWS Bedrock
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=us-east-1
AWS_PROFILE=default

# Self-hosted
OLLAMA_HOST=http://localhost:11434
LLAMACPP_URL=http://localhost:8080
VLLM_BASE_URL=http://localhost:8000
VLLM_API_KEY=...
TEI_URL=http://localhost:8080
TEI_API_KEY=...
```

### 15.1.3 Vector Stores

```bash
# Pinecone
PINECONE_API_KEY=...
PINECONE_INDEX_HOST=...

# Qdrant
QDRANT_URL=http://localhost:6333
QDRANT_API_KEY=...

# Weaviate
WEAVIATE_URL=http://localhost:8080
WEAVIATE_API_KEY=...

# Milvus
MILVUS_URI=http://localhost:19530
MILVUS_TOKEN=...

# PostgreSQL
PGVECTOR_DSN=postgresql://user:pass@localhost/db

# Redis
REDIS_URL=redis://localhost:6379

# MongoDB
MONGODB_URI=mongodb://localhost:27017

# Elasticsearch
ELASTICSEARCH_URL=http://localhost:9200
ELASTICSEARCH_API_KEY=...

# OpenSearch
OPENSEARCH_URL=http://localhost:9200
OPENSEARCH_USER=admin
OPENSEARCH_PASSWORD=...
```

### 15.1.4 Generation Providers

```bash
# Ollama
OMNILOAD_GENERATION_OLLAMA_BASE_URL=http://localhost:11434
OMNILOAD_GENERATION_OLLAMA_MODEL=llama3.1

# OpenAI
OMNILOAD_GENERATION_OPENAI_API_KEY=sk-...
OMNILOAD_GENERATION_OPENAI_MODEL=gpt-4o-mini

# Anthropic
OMNILOAD_GENERATION_ANTHROPIC_API_KEY=sk-ant-...
OMNILOAD_GENERATION_ANTHROPIC_MODEL=claude-3-sonnet-20240229

# Google Gemini
OMNILOAD_GENERATION_GEMINI_API_KEY=...
OMNILOAD_GENERATION_GEMINI_MODEL=gemini-pro

# Cohere
OMNILOAD_GENERATION_COHERE_API_KEY=...
OMNILOAD_GENERATION_COHERE_MODEL=command-r-plus

# Mistral
OMNILOAD_GENERATION_MISTRAL_API_KEY=...
OMNILOAD_GENERATION_MISTRAL_MODEL=mistral-large-latest

# Groq
OMNILOAD_GENERATION_GROQ_API_KEY=gsk-...
OMNILOAD_GENERATION_GROQ_MODEL=llama-3.1-70b-versatile

# Together AI
OMNILOAD_GENERATION_TOGETHER_API_KEY=...
OMNILOAD_GENERATION_TOGETHER_MODEL=meta-llama/Meta-Llama-3.1-70B-Instruct-Turbo

# OpenRouter
OMNILOAD_GENERATION_OPENROUTER_API_KEY=sk-or-...
OMNILOAD_GENERATION_OPENROUTER_MODEL=anthropic/claude-3-opus

# General settings
OMNILOAD_GENERATION_TIMEOUT=60
OMNILOAD_GENERATION_TEMPERATURE=0.7
OMNILOAD_GENERATION_MAX_TOKENS=
OMNILOAD_GENERATION_STREAMING=true
```

### 15.1.5 PDF/OCR Settings

```bash
# Tesseract path
OMNILOAD_TESSERACT_CMD=/usr/bin/tesseract

# Poppler path (Windows)
OMNILOAD_POPPLER_PATH=C:\Program Files\poppler\bin
```

## 15.2 Configuration Files

### 15.2.1 YAML Configuration

**config.yaml:**
```yaml
# RAGBench Configuration

framework:
  embedder: openai
  vector_store: qdrant
  retriever: hybrid_rrf
  collection: default
  cache_dir: ~/.cache/ragbench

embedding:
  method: openai
  model: text-embedding-3-small
  dimensions: 1536
  batch_size: 100
  normalize: true

vector_store:
  method: qdrant
  url: http://localhost:6333
  collection_name: documents
  dimension: 1536
  distance: cosine

retrieval:
  method: hybrid_rrf
  k: 20
  dense_weight: 0.5
  sparse_weight: 0.5
  filter: null
  min_score: 0.0

reranking:
  enabled: true
  method: cohere
  model: rerank-english-v3.0
  k: 5

generation:
  method: openai
  model: gpt-4o-mini
  temperature: 0.7
  max_tokens: 1000
  timeout: 60
  streaming: false

chunking:
  method: Recursive Character Chunking
  chunk_size: 800
  overlap: 80
  respect_boundaries: true
```

**Load configuration:**
```python
import yaml
from ragbench.pipeline import OmniPipeline

with open("config.yaml") as f:
    config = yaml.safe_load(f)

pipeline = OmniPipeline(
    embedder=config["embedding"]["method"],
    embedder_config=config["embedding"],
    store=config["vector_store"]["method"],
    store_config=config["vector_store"],
    generation_method=config["generation"]["method"],
    generation_config=config["generation"]
)
```

### 15.2.2 JSON Configuration

**config.json:**
```json
{
  "framework": {
    "embedder": "openai",
    "vector_store": "qdrant",
    "retriever": "hybrid_rrf"
  },
  "embedding": {
    "method": "openai",
    "model": "text-embedding-3-small"
  },
  "generation": {
    "method": "openai",
    "model": "gpt-4o-mini",
    "temperature": 0.7
  }
}
```

## 15.3 Programmatic Configuration

### 15.3.1 Pipeline Configuration

```python
from ragbench.pipeline import OmniPipeline

pipeline = OmniPipeline(
    # Embedding
    embedder="openai",
    embedder_config={
        "model": "text-embedding-3-small",
        "dimensions": 1536,
        "batch_size": 100
    },
    
    # Vector store
    store="qdrant",
    store_config={
        "url": "http://localhost:6333",
        "dimension": 1536,
        "distance": "cosine"
    },
    
    # Generation
    generation_method="openai",
    generation_config={
        "model": "gpt-4o-mini",
        "temperature": 0.7,
        "max_tokens": 1000
    },
    
    # Reranking
    rerank_method="cohere",
    rerank_config={
        "model": "rerank-english-v3.0"
    }
)
```

### 15.3.2 Per-Operation Configuration

```python
from ragbench.pipeline import ask

# Override defaults per call
answer = ask(
    query="What is quantum computing?",
    collection="docs",
    
    # Override embedder for this call
    embedder="cohere",
    embed_options={
        "model": "embed-v4.0",
        "input_type": "search_query"
    },
    
    # Override retrieval
    retrieval_method="multi_query",
    k=30,
    
    # Override reranking
    rerank=True,
    rerank_method="bge_reranker",
    rerank_k=5,
    
    # Override generation
    generation_method="anthropic",
    generation_options={
        "model": "claude-3-opus-20240229",
        "temperature": 0.5
    }
)
```

## 15.4 Default Values

### 15.4.1 Embedding Defaults

```python
DEFAULT_EMBEDDING_CONFIG = {
    "method": "hash_baseline",  # Non-semantic offline default
    "batch_size": 100,
    "cache": True,
    "normalize": False,
    "truncation": True,
    "max_length": 8192,
    "device": "cpu"
}
```

### 15.4.2 Retrieval Defaults

```python
DEFAULT_RETRIEVAL_CONFIG = {
    "method": "auto",  # Auto-routing
    "k": 10,
    "min_score": 0.0,
    "filter": None,
    "namespace": None,
    "dense_weight": 0.5,
    "sparse_weight": 0.5
}
```

### 15.4.3 Generation Defaults

```python
DEFAULT_GENERATION_CONFIG = {
    "method": "ollama",  # Free local default
    "model": "llama3.1",
    "temperature": 0.7,
    "max_tokens": None,
    "timeout": 60,
    "streaming": False
}
```

---

# 16. Troubleshooting Guide

## 16.1 Installation Issues

### 16.1.1 "pip install fails"

**Problem:** Package installation errors

**Solutions:**
```bash
# Update pip
python -m pip install --upgrade pip

# Clear cache
pip cache purge

# Install with --no-cache-dir
pip install --no-cache-dir ragbench-ai

# Use virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install ragbench-ai
```

### 16.1.2 "torch not found"

**Problem:** PyTorch not installed

**Solution:**
```bash
# Install PyTorch (CPU)
pip install torch torchvision

# Install PyTorch (GPU - CUDA 11.8)
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118

# Or install with local embedders
pip install "ragbench-ai[embed-local]"
```

### 16.1.3 "ModuleNotFoundError: No module named 'ragbench'"

**Problem:** Package not in Python path

**Solution:**
```python
# Check installation
pip show ragbench-ai

# Verify import
python -c "import ragbench; print(ragbench.__version__)"

# Check Python path
import sys
print(sys.path)

# Install in editable mode (for development)
pip install -e .
```

## 16.2 API Key Issues

### 16.2.1 "Missing API key"

**Problem:** API key not found

**Solution:**
```bash
# Check environment variable
echo $OPENAI_API_KEY  # Linux/Mac
echo %OPENAI_API_KEY%  # Windows CMD
echo $env:OPENAI_API_KEY  # Windows PowerShell

# Set temporarily
export OPENAI_API_KEY=sk-...  # Linux/Mac
set OPENAI_API_KEY=sk-...  # Windows CMD
$env:OPENAI_API_KEY="sk-..."  # Windows PowerShell

# Set in .env file
echo "OPENAI_API_KEY=sk-..." >> .env

# Set programmatically
import os
os.environ["OPENAI_API_KEY"] = "sk-..."
```

### 16.2.2 "Invalid API key"

**Problem:** API key rejected

**Solution:**
```python
# Verify key format
# OpenAI: starts with sk-
# Cohere: alphanumeric
# Anthropic: starts with sk-ant-

# Test API key
import requests

# OpenAI
response = requests.get(
    "https://api.openai.com/v1/models",
    headers={"Authorization": f"Bearer {api_key}"}
)
print(response.status_code)  # Should be 200

# Check account status
# Visit provider's dashboard
```

## 16.3 Embedding Issues

### 16.3.1 "Dimension mismatch"

**Problem:** Vector dimensions don't match

**Solution:**
```python
# Check collection dimension
info = store.collection_info("docs")
print(f"Collection dimension: {info.dimension}")

# Ensure embedder matches
embedder = Embedder("openai", dimensions=1536)  # Match collection

# Or recreate collection
store.delete_collection("docs")
store.create_collection("docs", dimension=1536)
```

### 16.3.2 "Embedding timeout"

**Problem:** Embedding takes too long

**Solution:**
```python
# Increase timeout
embedder = Embedder("openai", timeout=120)  # 2 minutes

# Reduce batch size
embedder.embed(texts, batch_size=10)  # Smaller batches

# Use faster embedder
embedder = Embedder("minilm")  # Local, fast

# Process in parallel
from concurrent.futures import ThreadPoolExecutor

def embed_one(text):
    return embedder.embed([text])[0]

with ThreadPoolExecutor(max_workers=5) as executor:
    results = list(executor.map(embed_one, texts))
```

### 16.3.3 "CUDA out of memory"

**Problem:** GPU memory exhausted

**Solution:**
```python
# Use CPU
embedder = Embedder("bge", device="cpu")

# Use smaller model
embedder = Embedder("minilm")  # Instead of bge-large

# Reduce batch size
embedder.embed(texts, batch_size=8)  # Instead of 32

# Clear GPU cache
import torch
torch.cuda.empty_cache()
```

## 16.4 Vector Store Issues

### 16.4.1 "Connection refused"

**Problem:** Cannot connect to vector store

**Solution:**
```bash
# Check if service is running
# Qdrant
docker ps | grep qdrant

# Start if not running
docker run -p 6333:6333 qdrant/qdrant

# Test connection
curl http://localhost:6333/health

# Check firewall
# Ensure port is open
```

### 16.4.2 "Collection not found"

**Problem:** Collection doesn't exist

**Solution:**
```python
# List collections
collections = store.list_collections()
print(f"Available: {collections}")

# Create collection
store.create_collection(
    name="docs",
    dimension=1536,
    distance="cosine"
)

# Or index documents first
auto(
    "document.pdf",
    embed=True,
    index=True,
    collection="docs"  # Creates collection
)
```

### 16.4.3 "Slow search"

**Problem:** Queries are slow

**Solution:**
```python
# Use approximate search (not Flat)
store = VectorStore("faiss", index_type="HNSW32")

# Reduce k
results = store.search(vec, k=10)  # Not k=1000

# Add indexes (for metadata filters)
# PostgreSQL
conn.execute("""
CREATE INDEX idx_year ON vectors ((metadata->>'year'))
""")

# Use better hardware
# - More RAM
# - SSD storage
# - GPU acceleration

# Cache queries
from functools import lru_cache

@lru_cache(maxsize=100)
def cached_search(query_hash, k):
    return store.search(get_vector(query_hash), k=k)
```

## 16.5 Generation Issues

### 16.5.1 "Ollama connection error"

**Problem:** Cannot connect to Ollama

**Solution:**
```bash
# Check if Ollama is running
curl http://localhost:11434/api/tags

# Start Ollama
ollama serve

# Pull model if missing
ollama pull llama3.1

# Test model
ollama run llama3.1 "Hello"

# Check port
# Default: 11434
# Override: OLLAMA_HOST=http://localhost:8080
```

### 16.5.2 "Model not found"

**Problem:** LLM model doesn't exist

**Solution:**
```python
# List available models
from ragbench.generation import Generator

generator = Generator(method="ollama")
models = generator.list_models()
print(f"Available: {models}")

# For Ollama
# ollama list

# For OpenAI
# Check: https://platform.openai.com/docs/models

# Update deprecated models
# gpt-3.5-turbo → gpt-4o-mini
# text-davinci-003 → gpt-3.5-turbo

generator = Generator(
    method="openai",
    model="gpt-4o-mini"  # Current model
)
```

### 16.5.3 "Context length exceeded"

**Problem:** Input too long for model

**Solution:**
```python
# Reduce context
from ragbench.generation import optimize_context

optimized = optimize_context(
    results,
    max_tokens=4000  # Model limit
)

answer = generator.generate(query, context=optimized)

# Use model with larger context
generator = Generator(
    method="anthropic",
    model="claude-3-sonnet-20240229"  # 200k tokens
)

# Chunk and summarize
# For very long documents
summaries = []
for chunk in large_chunks:
    summary = generator.generate(
        query="Summarize this",
        context=[chunk]
    )
    summaries.append(summary.answer)

# Final answer from summaries
final = generator.generate(
    query=original_query,
    context=summaries
)
```

## 16.6 Pipeline Issues

### 16.6.1 "No results found"

**Problem:** Query returns empty results

**Solution:**
```python
# Check if collection has data
info = store.collection_info("docs")
print(f"Vector count: {info.count}")

# If empty, index documents
auto(
    "documents/",
    embed=True,
    index=True,
    collection="docs"
)

# Lower score threshold
results = retrieve(
    query,
    min_score=0.0,  # Accept all
    k=10
)

# Try different method
results = retrieve(
    query,
    method="bm25",  # Keyword search
    corpus=chunks
)

# Check query/collection mismatch
# Ensure using same embedder for index and query
```

### 16.6.2 "Poor quality answers"

**Problem:** Generated answers are bad

**Solution:**
```python
# Retrieve more context
results = retrieve(query, k=20)  # More chunks

# Enable reranking
answer = ask(
    query,
    collection="docs",
    k=50,  # Over-retrieve
    rerank=True,
    rerank_method="cohere",
    rerank_k=10
)

# Use better embedder
embedder = Embedder(
    "openai",
    model="text-embedding-3-large"  # Better than small
)

# Use better LLM
generator = Generator(
    method="openai",
    model="gpt-4"  # Better than gpt-3.5
)

# Improve chunks
# - Better chunking strategy
# - Larger chunks for more context
# - Hierarchical for nested info

# Add system message
generator = Generator(
    method="openai",
    system_message="You are an expert assistant. Provide detailed, accurate answers."
)
```

## 16.7 Performance Issues

### 16.7.1 "Slow indexing"

**Problem:** Indexing takes too long

**Solution:**
```python
# Increase batch size
auto(
    "documents/",
    embed=True,
    index=True,
    embed_options={"batch_size": 200}  # Larger batches
)

# Use faster embedder
embedder = Embedder("hash_baseline")  # Non-semantic but fast

# Parallel processing
from concurrent.futures import ThreadPoolExecutor

def index_one(file):
    auto(file, embed=True, index=True, collection="docs")

with ThreadPoolExecutor(max_workers=4) as executor:
    executor.map(index_one, files)

# Use GPU
embedder = Embedder("bge", device="cuda")
```

### 16.7.2 "High memory usage"

**Problem:** Process uses too much RAM

**Solution:**
```python
# Stream processing (don't load all in memory)
def process_stream(files):
    for file in files:
        # Process one at a time
        auto(file, embed=True, index=True)
        # Memory freed after each

# Use smaller models
embedder = Embedder("minilm")  # 80MB vs 1.5GB

# Reduce batch size
embedder.embed(texts, batch_size=10)  # Smaller batches

# Use quantization
embedder = Embedder("quantize_int8", base="bge")
```

---

# 17. Best Practices

## 17.1 Data Preparation

### 17.1.1 Clean Documents

```python
def clean_document(text):
    """Clean document before processing."""
    
    # Remove extra whitespace
    text = " ".join(text.split())
    
    # Remove page numbers
    text = re.sub(r'Page \d+', '', text)
    
    # Remove headers/footers
    # ... (domain-specific)
    
    # Fix encoding issues
    text = text.encode('utf-8', errors='ignore').decode('utf-8')
    
    return text

# Use before chunking
cleaned = clean_document(raw_text)
chunks = chunk(cleaned, method="auto")
```

### 17.1.2 Metadata Enrichment

```python
def enrich_metadata(document):
    """Add useful metadata."""
    
    metadata = document.metadata.copy()
    
    # Extract date
    date_match = re.search(r'\d{4}-\d{2}-\d{2}', document.content)
    if date_match:
        metadata["date"] = date_match.group()
    
    # Detect language
    from langdetect import detect
    metadata["language"] = detect(document.content[:1000])
    
    # Calculate statistics
    metadata["word_count"] = len(document.content.split())
    metadata["char_count"] = len(document.content)
    
    # Add source type
    if document.metadata.get("source", "").endswith(".pdf"):
        metadata["source_type"] = "pdf"
    
    document.metadata = metadata
    return document
```

### 17.1.3 Deduplication

```python
from ragbench.chunking import chunk
import hashlib

def deduplicate_chunks(chunks):
    """Remove duplicate chunks."""
    
    seen = set()
    unique = []
    
    for c in chunks:
        # Hash content
        content_hash = hashlib.md5(c.content.encode()).hexdigest()
        
        if content_hash not in seen:
            seen.add(content_hash)
            unique.append(c)
    
    print(f"Removed {len(chunks) - len(unique)} duplicates")
    return unique

chunks = chunk(document, method="auto")
chunks = deduplicate_chunks(chunks)
```

## 17.2 Chunking Strategies

### 17.2.1 Choose Right Chunk Size

```python
# Guidelines by use case
use_case_chunk_size = {
    "qa_short_answers": 300,       # Small, precise
    "qa_detailed": 800,            # Medium, balanced
    "summarization": 1500,         # Large, context
    "code_search": "function",     # Semantic unit
    "legal_documents": 1000,       # Paragraph-level
    "research_papers": 1200,       # Section-level
}

chunk_size = use_case_chunk_size.get(my_use_case, 800)
```

### 17.2.2 Overlap Configuration

```python
# Rule of thumb: 10-20% overlap
chunk_size = 800
overlap = chunk_size // 8  # 12.5%

chunks = chunk(
    document,
    method="Recursive Character Chunking",
    chunk_size=chunk_size,
    overlap=overlap
)
```

### 17.2.3 Content-Aware Chunking

```python
def smart_chunk(document):
    """Choose chunking method based on content."""
    
    content = document.content
    
    # Code
    if any(ext in document.metadata.get("source", "") 
           for ext in [".py", ".js", ".java"]):
        return chunk(content, method="Code Chunking")
    
    # Markdown
    elif content.startswith("#") or "##" in content:
        return chunk(content, method="Markdown Chunking")
    
    # JSON/YAML
    elif content.strip().startswith(("{", "[")):
        return chunk(content, method="JSON Chunking")
    
    # HTML
    elif "<html" in content.lower():
        return chunk(content, method="HTML Chunking")
    
    # Long form
    elif len(content) > 10000:
        return chunk(content, method="Hierarchical Chunking")
    
    # Default
    else:
        return chunk(content, method="Recursive Character Chunking")
```

## 17.3 Embedding Best Practices

### 17.3.1 Model Selection

```python
# Development: Fast and free
embedder = Embedder("hash_baseline")

# Production (local): Free, semantic
embedder = Embedder("bge", model="BAAI/bge-base-en-v1.5")

# Production (API): Best quality
embedder = Embedder("openai", model="text-embedding-3-large")

# Multilingual
embedder = Embedder("multilingual_e5")

# Domain-specific
embedder = Embedder("biomedical")  # Medical
embedder = Embedder("finance")     # Financial
embedder = Embedder("legal")       # Legal
```

### 17.3.2 Caching Strategy

```python
# Enable caching for production
embedder = Embedder("openai", cache=True)

# Custom cache directory
import os
os.environ["OMNILOAD_CACHE_DIR"] = "/data/cache"

# Warm cache
# Pre-compute embeddings for common queries
common_queries = [
    "What is machine learning?",
    "How does RAG work?",
    "Explain transformers"
]

for query in common_queries:
    embedder.embed_query(query)
```

### 17.3.3 Batch Processing

```python
# Efficient batch embedding
large_text_list = [...]  # 10,000 texts

# Don't do this (slow)
embeddings = [embedder.embed([t])[0] for t in large_text_list]

# Do this (fast)
embeddings = embedder.embed(
    large_text_list,
    batch_size=100,  # Process in batches
    show_progress=True
)
```

## 17.4 Retrieval Optimization

### 17.4.1 Over-Retrieval + Reranking

```python
# Best practice: Retrieve 3-5x, then rerank
k_final = 10
k_retrieval = k_final * 4  # 40

results = retrieve(query, k=k_retrieval)
reranked = rerank(query, results, k=k_final)
```

### 17.4.2 Metadata Filtering

```python
# Filter early (at retrieval)
results = retrieve(
    query,
    method="dense",
    filter={
        "year": {"$gte": 2020},  # Recent only
        "source_type": "official"  # Authoritative
    },
    k=10
)

# Don't filter late (wasteful)
# Bad:
all_results = retrieve(query, k=1000)
filtered = [r for r in all_results if r.metadata.get("year") >= 2020]
```

### 17.4.3 Query Preprocessing

```python
def preprocess_query(query):
    """Clean and enhance query."""
    
    # Lowercase
    query = query.lower()
    
    # Remove special chars
    query = re.sub(r'[^\w\s]', '', query)
    
    # Remove stop words (optional, can hurt semantic search)
    # stopwords = set(['the', 'a', 'an', ...])
    # query = ' '.join([w for w in query.split() if w not in stopwords])
    
    # Expand abbreviations
    expansions = {
        "ml": "machine learning",
        "ai": "artificial intelligence",
        "nlp": "natural language processing"
    }
    for abbr, full in expansions.items():
        query = query.replace(abbr, full)
    
    return query

# Use preprocessed query
cleaned_query = preprocess_query(user_query)
results = retrieve(cleaned_query, ...)
```

## 17.5 Generation Quality

### 17.5.1 Prompt Engineering

```python
from ragbench.generation import PromptTemplate

# Good prompt
good_template = PromptTemplate(
    template="""You are a helpful assistant that answers questions based on provided context.

Context:
{context}

Question: {question}

Instructions:
- Answer based only on the context above
- If the context doesn't contain the answer, say "I don't have enough information"
- Cite sources using [1], [2], etc.
- Be concise and accurate

Answer:""",
    variables=["context", "question"]
)

# Bad prompt (too vague)
bad_template = PromptTemplate(
    template="Context: {context}\n\nQuestion: {question}\n\nAnswer:",
    variables=["context", "question"]
)
```

### 17.5.2 Temperature Tuning

```python
# Factual Q&A: Low temperature
generator = Generator(
    method="openai",
    temperature=0.2  # More deterministic
)

# Creative writing: High temperature
generator = Generator(
    method="openai",
    temperature=0.9  # More varied
)

# Balanced: Medium temperature
generator = Generator(
    method="openai",
    temperature=0.7  # Default
)
```

### 17.5.3 Citation Validation

```python
def validate_citations(answer, context):
    """Check if citations are valid."""
    
    import re
    
    # Extract citation numbers
    citations = re.findall(r'\[(\d+)\]', answer.answer)
    
    valid = []
    invalid = []
    
    for cit in citations:
        cit_num = int(cit)
        
        # Check if citation exists
        if cit_num <= len(answer.citations):
            valid.append(cit_num)
        else:
            invalid.append(cit_num)
    
    if invalid:
        print(f"Warning: Invalid citations: {invalid}")
    
    return len(invalid) == 0

# Usage
answer = generator.generate(query, context=context)
is_valid = validate_citations(answer, context)
```

## 17.6 Monitoring & Logging

### 17.6.1 Request Logging

```python
import logging
import time
import json

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

def log_request(query, result, duration):
    """Log RAG request details."""
    
    log_data = {
        "query": query,
        "duration_s": duration,
        "retrieval_count": len(result.get("retrieval", [])),
        "reranking_count": len(result.get("reranking", [])),
        "answer_length": len(result["answer"].answer),
        "citations": len(result["answer"].citations),
        "model": result["answer"].model,
        "tokens_used": result["answer"].tokens_used
    }
    
    logger.info(f"RAG Request: {json.dumps(log_data)}")

# Usage
start = time.time()
result = ask(query, collection="docs", include_retrieval=True)
duration = time.time() - start

log_request(query, result, duration)
```

### 17.6.2 Performance Metrics

```python
class RAGMetrics:
    """Track RAG performance metrics."""
    
    def __init__(self):
        self.queries = []
        self.durations = []
        self.token_counts = []
    
    def record(self, query, duration, tokens):
        """Record a query."""
        self.queries.append(query)
        self.durations.append(duration)
        self.token_counts.append(tokens)
    
    def summary(self):
        """Get summary statistics."""
        import numpy as np
        
        return {
            "total_queries": len(self.queries),
            "avg_duration": np.mean(self.durations),
            "p95_duration": np.percentile(self.durations, 95),
            "avg_tokens": np.mean(self.token_counts),
            "total_tokens": sum(self.token_counts)
        }

# Usage
metrics = RAGMetrics()

for query in user_queries:
    start = time.time()
    answer = ask(query, collection="docs")
    duration = time.time() - start
    
    metrics.record(query, duration, answer.tokens_used)

print(metrics.summary())
```

## 17.7 Error Handling

### 17.7.1 Graceful Degradation

```python
from ragbench.errors import EmbeddingError, GenerationError

def robust_ask(query, collection):
    """Ask with fallbacks."""
    
    # Try primary configuration
    try:
        return ask(
            query,
            collection=collection,
            embedder="openai",
            generation_method="openai"
        )
    
    # Fallback 1: Local embedder
    except EmbeddingError:
        logger.warning("OpenAI embedder failed, using local")
        try:
            return ask(
                query,
                collection=collection,
                embedder="bge",
                generation_method="openai"
            )
        except:
            pass
    
    # Fallback 2: Local LLM
    except GenerationError:
        logger.warning("OpenAI generation failed, using Ollama")
        return ask(
            query,
            collection=collection,
            embedder="openai",
            generation_method="ollama"
        )
    
    # Fallback 3: All local
    except Exception as e:
        logger.error(f"All primary methods failed: {e}")
        return ask(
            query,
            collection=collection,
            embedder="hash_baseline",
            generation_method="ollama"
        )
```

### 17.7.2 Retry Logic

```python
from tenacity import (
    retry,
    stop_after_attempt,
    wait_exponential,
    retry_if_exception_type
)
from ragbench.errors import TransientError

@retry(
    retry=retry_if_exception_type(TransientError),
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=4, max=10)
)
def retry_ask(query, collection):
    """Ask with automatic retries."""
    return ask(query, collection=collection)

# Usage
answer = retry_ask(user_query, "docs")
```

## 17.8 Security

### 17.8.1 Input Validation

```python
def validate_query(query):
    """Validate user query."""
    
    if not query or not isinstance(query, str):
        raise ValueError("Query must be non-empty string")
    
    if len(query) > 10000:
        raise ValueError("Query too long")
    
    # Check for injection attempts
    suspicious_patterns = [
        r"<script",
        r"javascript:",
        r"onerror=",
        r"';DROP TABLE"
    ]
    
    for pattern in suspicious_patterns:
        if re.search(pattern, query, re.IGNORECASE):
            raise ValueError(f"Suspicious pattern detected: {pattern}")
    
    return query

# Usage
try:
    clean_query = validate_query(user_input)
    answer = ask(clean_query, collection="docs")
except ValueError as e:
    print(f"Invalid query: {e}")
```

### 17.8.2 Rate Limiting

```python
from collections import defaultdict
from datetime import datetime, timedelta

class RateLimiter:
    """Simple rate limiter."""
    
    def __init__(self, max_requests=10, window_seconds=60):
        self.max_requests = max_requests
        self.window = timedelta(seconds=window_seconds)
        self.requests = defaultdict(list)
    
    def allow(self, user_id):
        """Check if request is allowed."""
        now = datetime.now()
        
        # Clean old requests
        self.requests[user_id] = [
            req_time for req_time in self.requests[user_id]
            if now - req_time < self.window
        ]
        
        # Check limit
        if len(self.requests[user_id]) >= self.max_requests:
            return False
        
        # Record request
        self.requests[user_id].append(now)
        return True

# Usage
limiter = RateLimiter(max_requests=10, window_seconds=60)

if limiter.allow(user_id):
    answer = ask(query, collection="docs")
else:
    print("Rate limit exceeded. Try again later.")
```

### 17.8.3 Output Sanitization

```python
def sanitize_answer(answer):
    """Sanitize generated answer."""
    
    text = answer.answer
    
    # Remove potential PII
    # (This is a simple example - use proper PII detection)
    text = re.sub(r'\b\d{3}-\d{2}-\d{4}\b', '[SSN]', text)  # SSN
    text = re.sub(r'\b\d{3}-\d{3}-\d{4}\b', '[PHONE]', text)  # Phone
    text = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', '[EMAIL]', text)  # Email
    
    # Remove malicious content
    text = re.sub(r'<script.*?</script>', '', text, flags=re.DOTALL)
    
    answer.answer = text
    return answer

# Usage
answer = ask(query, collection="docs")
sanitized = sanitize_answer(answer)
```

---

# 18. Contributing to RAGBench

## 18.1 Getting Started

### 18.1.1 Development Setup

```bash
# Fork and clone
git clone https://github.com/YOUR_USERNAME/ragbench.git
cd ragbench

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install in editable mode with dev dependencies
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install
```

### 18.1.2 Code Style

RAGBench follows PEP 8 with some modifications:

```python
# Use Black for formatting
black src/ tests/

# Use isort for imports
isort src/ tests/

# Run linter
ruff check src/ tests/

# Type checking (optional)
mypy src/
```

### 18.1.3 Testing

```bash
# Run all tests
python -B -m unittest discover -s tests

# Run specific test file
python -B -m unittest tests/test_embedding_methods.py

# Run with coverage
pytest --cov=ragbench tests/

# Live tests (requires API keys)
OMNILOAD_LIVE_TESTS=1 python -B -m unittest discover -s tests
```

## 18.2 Adding Methods

### 18.2.1 Add Embedding Method

**1. Create directory:**
```
embedding/methods/my_embedder/
├── __init__.py
├── metadata.json
├── embedder.py
├── requirements.txt
```

**2. metadata.json:**
```json
{
  "name": "My Embedder",
  "slug": "my_embedder",
  "aliases": ["my-embed", "myembed"],
  "category": "api",
  "modality": ["text"],
  "supports": {
    "dense": true,
    "sparse": false,
    "multi_vector": false
  },
  "environment": "cloud",
  "entry_point": "embedder:Method",
  "description": "My custom embedding model",
  "requires_env": ["MY_API_KEY"],
  "requires_packages": ["requests"],
  "default_model": "my-model-v1",
  "dimension": 1024,
  "config_schema": {
    "model": {"type": "string"},
    "api_key": {"type": "string"}
  }
}
```

**3. embedder.py:**
```python
from ragbench.embedding.methods._http import HTTPEmbedder

class Method(HTTPEmbedder):
    """My custom embedder."""
    
    def __init__(self, **config):
        super().__init__(**config)
        self.api_key = self.require_env("MY_API_KEY")
        self.model = config.get("model", "my-model-v1")
        self.base_url = "https://api.myservice.com/v1"
    
    def build_body(self, texts, kind):
        """Build API request body."""
        return {
            "model": self.model,
            "input": texts,
            "encoding_format": "float"
        }
    
    def parse_response(self, response):
        """Parse API response."""
        data = response.json()
        return [item["embedding"] for item in data["data"]]
    
    def _get_headers(self):
        """Get request headers."""
        return {
            "Authorization": f"Bearer {self.api_key}",
            "Content-Type": "application/json"
        }
    
    def _get_endpoint(self):
        """Get API endpoint."""
        return f"{self.base_url}/embeddings"
```

**4. requirements.txt:**
```
requests>=2.28.0
```

**5. __init__.py:**
```python
from .embedder import Method

__all__ = ["Method"]
```

**6. Add test:**
```python
# tests/test_embedding_methods.py

CASES = [
    # ... existing cases
    {
        "name": "my_embedder",
        "backend": "fake_api",  # or "fake_local"
        "mock": lambda: mock_api_embedder("my_embedder"),
        "texts": ["test"],
        "expected_dim": 1024
    }
]
```

### 18.2.2 Add Chunking Method

**1. Create file:**
```python
# chunking/methods/my_chunking_strategy.py

from ragbench.chunking.strategies import ChunkingStrategy
from ragbench.chunking.models import Chunk

class Strategy(ChunkingStrategy):
    """My custom chunking strategy."""
    
    name = "My Chunking Strategy"
    group = "custom"  # or "semantic", "hierarchical", etc.
    
    def __init__(self, chunk_size=500, **options):
        self.chunk_size = chunk_size
        self.options = options
    
    def chunk(self, document):
        """Chunk the document."""
        text = self.get_text(document)
        
        # Your chunking logic
        parts = self._split(text)
        
        chunks = []
        for i, part in enumerate(parts):
            chunk = Chunk(
                id=f"chunk_{i}",
                document_id=self.get_document_id(document),
                content=part,
                metadata={
                    "method": self.name,
                    "index": i,
                    "chunk_size": self.chunk_size
                }
            )
            chunks.append(chunk)
        
        return chunks
    
    def _split(self, text):
        """Split text into parts."""
        # Implement your splitting logic
        pass
```

**2. Auto-discovered (no registration needed)**

**3. Add test:**
```python
# tests/test_chunking_methods.py

def test_my_chunking_strategy(self):
    text = "Test document..."
    chunks = chunk(text, method="My Chunking Strategy", chunk_size=100)
    self.assertGreater(len(chunks), 0)
```

### 18.2.3 Add Retrieval Method

**1. Create directory:**
```
retrieval/methods/my_retriever/
├── __init__.py
├── metadata.json
├── retriever.py
├── requirements.txt
```

**2. metadata.json:**
```json
{
  "name": "My Retriever",
  "slug": "my_retriever",
  "aliases": ["my-retrieve"],
  "group": "custom",
  "category": "experimental",
  "capabilities": {
    "dense": true,
    "sparse": false,
    "hybrid": false,
    "filters": true
  },
  "config_schema": {
    "param1": {"type": "number", "default": 0.5}
  }
}
```

**3. retriever.py:**
```python
from ragbench.retrieval.methods._helpers import DenseRetriever

class Method(DenseRetriever):
    """My custom retriever."""
    
    def _run(self, query, ctx):
        """Run retrieval."""
        
        # Get query embedding
        query_vec = ctx.embedder.embed_query(query.text)
        
        # Search
        results = ctx.store.search(
            vector=query_vec.vector,
            k=query.k,
            collection=query.collections[0],
            filter=query.filter
        )
        
        # Custom processing
        # ...
        
        return results
```

## 18.3 Pull Request Guidelines

### 18.3.1 Before Submitting

```bash
# Format code
black src/ tests/
isort src/ tests/

# Run linter
ruff check src/ tests/

# Run tests
python -B -m unittest discover -s tests

# Check types (optional)
mypy src/
```

### 18.3.2 PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Tests added/updated
- [ ] All tests pass
- [ ] Manual testing done

## Checklist
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] No breaking changes (or documented)
- [ ] Tests pass
```

### 18.3.3 Commit Messages

```bash
# Format: <type>: <description>

# Types:
# feat: New feature
# fix: Bug fix
# docs: Documentation
# style: Formatting
# refactor: Code restructuring
# test: Tests
# chore: Maintenance

# Examples:
git commit -m "feat: Add Cohere reranking support"
git commit -m "fix: Handle empty query in retrieval"
git commit -m "docs: Update embedding configuration guide"
```

## 18.4 Documentation

### 18.4.1 Docstrings

```python
def my_function(param1: str, param2: int = 10) -> List[str]:
    """
    Short description of function.
    
    Longer description with more details about what it does,
    how it works, and any important notes.
    
    Args:
        param1: Description of param1
        param2: Description of param2. Defaults to 10.
    
    Returns:
        Description of return value.
    
    Raises:
        ValueError: When param1 is empty
        TypeError: When param2 is not an integer
    
    Example:
        >>> result = my_function("hello", 5)
        >>> print(result)
        ['h', 'e', 'l', 'l', 'o']
    """
    pass
```

### 18.4.2 README Updates

When adding a new method, update relevant README:

```markdown
## Available Methods

| Method | Category | Description |
|--------|----------|-------------|
| ... | ... | ... |
| my_method | custom | My new method |
```

---

# 19. Complete API Reference

## 19.1 Core Classes

### 19.1.1 Document

```python
@dataclass
class Document:
    id: str
    content: str
    metadata: Dict[str, Any]
    modality: str = "text"
    language: Optional[str] = None
    embedding: Optional[List[float]] = None
```

### 19.1.2 Chunk

```python
@dataclass
class Chunk:
    id: str
    document_id: str
    content: str
    metadata: Dict[str, Any]
    position: Optional[int] = None
    parent_id: Optional[str] = None
```

### 19.1.3 EmbeddedChunk

```python
@dataclass
class EmbeddedChunk:
    id: str
    chunk_id: str
    document_id: str
    content: str
    vector: Optional[List[float]] = None
    sparse: Optional[Dict[int, float]] = None
    multi_vector: Optional[List[List[float]]] = None
    model: str
    dim: int
    modality: str
    metadata: Dict[str, Any]
```

## 19.2 Pipeline Functions

### 19.2.1 auto()

```python
def auto(
    source: Union[str, List[str]],
    as_text: bool = False,
    method: Optional[str] = None,
    chunk_size: Optional[int] = None,
    overlap: Optional[int] = None,
    embed: bool = False,
    embedder: Optional[str] = None,
    embed_options: Optional[Dict] = None,
    index: bool = False,
    store: Optional[str] = None,
    collection: Optional[str] = None,
    store_options: Optional[Dict] = None,
    include_documents: bool = False,
    include_chunks: bool = False,
    **kwargs
) -> Union[List[Chunk], Dict]:
    """
    Automatic pipeline: ingest → chunk → embed → index.
    
    Args:
        source: File path, directory, or text
        as_text: Treat source as raw text
        method: Chunking method
        chunk_size: Chunk size in characters
        overlap: Overlap between chunks
        embed: Enable embedding
        embedder: Embedding method
        embed_options: Embedder configuration
        index: Enable indexing
        store: Vector store method
        collection: Collection name
        store_options: Store configuration
        include_documents: Return documents in result
        include_chunks: Return chunks in result
        **kwargs: Additional options
    
    Returns:
        Chunks, embeddings, or dict with multiple outputs
    """
```

### 19.2.2 ask()

```python
def ask(
    query: str,
    collection: str,
    store: Optional[str] = None,
    embedder: Optional[str] = None,
    retrieval_method: Optional[str] = None,
    k: int = 10,
    filter: Optional[Dict] = None,
    rerank: bool = False,
    rerank_method: Optional[str] = None,
    rerank_k: Optional[int] = None,
    generation_method: Optional[str] = None,
    generation_options: Optional[Dict] = None,
    streaming: bool = False,
    conversation: Optional[Conversation] = None,
    template: Optional[PromptTemplate] = None,
    include_retrieval: bool = False,
    include_reranking: bool = False,
    **kwargs
) -> Union[GeneratedAnswer, Dict, Iterator[str]]:
    """
    Complete RAG: retrieve → rerank → generate.
    
    Args:
        query: User question
        collection: Collection to search
        store: Vector store method
        embedder: Embedding method
        retrieval_method: Retrieval strategy
        k: Number of results to retrieve
        filter: Metadata filters
        rerank: Enable reranking
        rerank_method: Reranking method
        rerank_k: Results after reranking
        generation_method: LLM provider
        generation_options: LLM configuration
        streaming: Stream tokens
        conversation: Conversation history
        template: Custom prompt template
        include_retrieval: Return retrieval results
        include_reranking: Return reranking results
        **kwargs: Additional options
    
    Returns:
        Generated answer, or dict with intermediate results,
        or token iterator if streaming
    """
```

## 19.3 Layer APIs

### 19.3.1 Embedding

```python
class Embedder:
    def __init__(self, method: str, **config):
        """Initialize embedder."""
    
    def embed(self, texts: List[str], kind: str = "document") -> List[EmbeddedChunk]:
        """Embed texts."""
    
    def embed_query(self, text: str) -> EmbeddedChunk:
        """Embed query."""
    
    def embed_documents(self, texts: List[str]) -> List[EmbeddedChunk]:
        """Embed documents."""
    
    def is_available(self) -> bool:
        """Check if embedder is available."""
    
    def as_callback(self):
        """Return callback for chunking."""
```

### 19.3.2 Vector Store

```python
class VectorStore:
    def __init__(self, method: str, **config):
        """Initialize vector store."""
    
    def add(self, embeddings: List[EmbeddedChunk], collection: str) -> IndexReport:
        """Add vectors."""
    
    def search(
        self, 
        vector: List[float],
        k: int,
        collection: str,
        filter: Optional[Dict] = None
    ) -> List[SearchResult]:
        """Search vectors."""
    
    def delete(self, ids: List[str], collection: str):
        """Delete vectors."""
    
    def list_collections(self) -> List[str]:
        """List collections."""
```

### 19.3.3 Retrieval

```python
class Retriever:
    def __init__(
        self,
        method: str,
        embedder: Optional[Embedder] = None,
        store: Optional[VectorStore] = None,
        **config
    ):
        """Initialize retriever."""
    
    def retrieve(
        self,
        query: str,
        collection: str,
        k: int = 10,
        filter: Optional[Dict] = None,
        **kwargs
    ) -> List[RetrievalResult]:
        """Retrieve results."""
    
    def retrieve_report(
        self,
        query: str,
        collection: str,
        k: int = 10,
        **kwargs
    ) -> RetrievalReport:
        """Retrieve with detailed report."""
```

### 19.3.4 Generation

```python
class Generator:
    def __init__(self, method: str, **config):
        """Initialize generator."""
    
    def generate(
        self,
        query: str,
        context: List[Dict],
        conversation: Optional[Conversation] = None,
        template: Optional[PromptTemplate] = None,
        **kwargs
    ) -> GeneratedAnswer:
        """Generate answer."""
    
    def generate_stream(
        self,
        query: str,
        context: List[Dict],
        **kwargs
    ) -> Iterator[str]:
        """Generate streaming answer."""
    
    def is_available(self) -> bool:
        """Check if generator is available."""
```

---

# 20. Appendices

## 20.1 Glossary

**RAG**: Retrieval Augmented Generation - technique combining retrieval and generation

**Embedding**: Numerical vector representation of text

**Dense Vector**: Fixed-length continuous vector (e.g., [0.2, 0.8, 0.1, ...])

**Sparse Vector**: Dictionary of term → weight (e.g., {125: 0.45, 389: 0.82})

**Multi-Vector**: Multiple vectors per text (e.g., ColBERT)

**Chunk**: Small piece of document for processing

**Collection**: Named group of vectors in a store

**Namespace**: Logical partition within a collection

**Reranking**: Refining retrieval results with deeper analysis

**Cross-Encoder**: Model that scores query-document pairs directly

**Bi-Encoder**: Separate encoders for queries and documents

**Late Interaction**: Computing fine-grained similarity (e.g., ColBERT)

**MMR**: Maximal Marginal Relevance - diversification algorithm

**HyDE**: Hypothetical Document Embeddings

**RRF**: Reciprocal Rank Fusion

**ANN**: Approximate Nearest Neighbors

**HNSW**: Hierarchical Navigable Small World graph

**IVF**: Inverted File index

**PQ**: Product Quantization

## 20.2 Resources

### Official Links
- **GitHub**: https://github.com/Omkar-S-Kulkarni/ragbench
- **PyPI**: https://pypi.org/project/ragbench-ai/
- **Documentation**: https://github.com/Omkar-S-Kulkarni/ragbench#readme

### Community
- **Issues**: Report bugs and request features
- **Discussions**: Ask questions and share ideas
- **Contributing**: See CONTRIBUTING.md

### Related Projects
- **LangChain**: https://github.com/langchain-ai/langchain
- **LlamaIndex**: https://github.com/run-llama/llama_index
- **Haystack**: https://github.com/deepset-ai/haystack

## 20.3 Changelog

See `CHANGELOG.md` for version history and release notes.

## 20.4 License

RAGBench is licensed under Apache 2.0. See `LICENSE` file.

## 20.5 Citation

If you use RAGBench in research, please cite:

```bibtex
@software{ragbench2024,
  title = {RAGBench: Layered Auto-RAG Framework},
  author = {RAGBench Contributors},
  year = {2024},
  url = {https://github.com/Omkar-S-Kulkarni/ragbench}
}
```

---

**End of Comprehensive Guide**

*Total: 50,000+ lines of detailed documentation*

*Last Updated: 2024*

For the latest updates, visit: https://github.com/Omkar-S-Kulkarni/ragbench
