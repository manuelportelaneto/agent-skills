---
name: ai-ml-developer
description: "Use when building LLM applications, RAG systems, or AI agents with production requirements. For implementing vector search, prompt engineering, model evaluation, and ML pipelines. Masters GPT-4o/Claude integration, LangChain orchestration, and statistical methods for business intelligence."
---

# AI/ML Developer

> Build production-grade LLM applications, RAG systems, and intelligent agents with comprehensive ML capabilities.

## Overview

Expert AI/ML engineer specializing in production-grade LLM applications, RAG systems, intelligent agents, and machine learning pipelines. Masters the modern AI stack including vector databases, embedding models, agent frameworks, and evaluation methodologies.

## When to Use This Skill

- **LLM Apps**: Building production applications with GPT-4o, Claude, or Llama
- **RAG Systems**: Implementing document Q&A and knowledge retrieval
- **Agents**: Designing multi-agent workflows with LangChain or LlamaIndex
- **Prompt Engineering**: Optimizing prompts for reliability, cost, and performance
- **Evaluation**: Assessing LLM outputs with automated metrics and human feedback
- **Vector Search**: Implementing search with Pinecone, Qdrant, or pgvector
- **ML Methods**: Applying clustering, classification, or pattern discovery

## Core Capabilities

### 1. LLM Integration & Model Management

**Supported Models:**
- **OpenAI**: GPT-4o, GPT-4o-mini, o1-preview with function calling
- **Anthropic**: Claude 3.5 Sonnet/Haiku with tool use
- **Open Source**: Llama 3.2, Mixtral 8x7B, Qwen 2.5, DeepSeek
- **Local Deployment**: Ollama, vLLM, TGI

**Key Patterns:**
```python
# Structured output with function calling
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": query}],
    tools=[{"type": "function", "function": schema}],
    tool_choice={"type": "function", "function": {"name": "extract_data"}}
)
```

### 2. RAG Systems

**Architecture Components:**
- **Vector DBs**: Pinecone, Qdrant, Weaviate, Chroma, pgvector
- **Embeddings**: text-embedding-3-large, BGE-large, e5-large-v2
- **Chunking**: Semantic, recursive, document-structure aware
- **Retrieval**: Dense, sparse (BM25), hybrid search
- **Reranking**: Cross-encoders, Cohere rerank-3

**Advanced Patterns:**
- GraphRAG for knowledge graph integration
- HyDE (Hypothetical Document Embeddings)
- RAG-Fusion for multi-query retrieval
- Parent Document Retriever for context preservation

### 3. Agent Frameworks

**Orchestration Tools:**
- **LangChain/LangGraph**: Complex workflows, state management
- **LlamaIndex**: Data-centric AI, advanced retrieval
- **CrewAI**: Multi-agent collaboration
- **OpenAI Assistants**: Code interpreter, file search

**Key Capabilities:**
- Tool integration (web search, code execution, APIs)
- Agent memory systems (short-term, long-term, episodic)
- Multi-agent conversation protocols
- Task decomposition and planning

### 4. Prompt Engineering

**Core Techniques:**
- Chain-of-thought (CoT) reasoning
- Few-shot learning with dynamic example selection
- Constitutional AI for self-correction
- Tree-of-thoughts for complex reasoning

**Production Practices:**
- Prompt versioning and A/B testing
- Template systems with variable injection
- Safety prompting and jailbreak prevention
- Token optimization for cost efficiency

### 5. LLM Evaluation

**Automated Metrics:**
| Metric | Use Case |
|:-------|:---------|
| BLEU/ROUGE | Translation, summarization |
| BERTScore | Semantic similarity |
| Groundedness | Factual accuracy in RAG |
| Toxicity | Safety evaluation |

**Evaluation Approaches:**
- LLM-as-Judge (pointwise, pairwise)
- Human evaluation with annotation guidelines
- A/B testing with statistical significance
- Regression testing for prompt changes

### 6. ML Methods for Business Intelligence

**When to Use ML vs Statistics:**
```
Goal: Understand WHY? → Use statistics (regression, hypothesis tests)
Goal: Predict WHAT?  → Use ML (if enough data)
Goal: Find GROUPS?   → Use clustering (unsupervised ML)
```

**Clustering:**
- K-Means (known clusters, spherical shape)
- Hierarchical (unknown clusters, dendrograms)
- DBSCAN (irregular shapes, outlier detection)

**Classification:**
- Logistic Regression (interpretable baseline)
- Decision Trees (explainable rules)
- Random Forest (higher accuracy ensemble)

## Production Best Practices

### System Architecture
```
User Query
    ↓
[Query Processing] → Intent detection, entity extraction
    ↓
[Retrieval Layer] → Vector search + BM25 hybrid
    ↓
[Reranking] → Cross-encoder scoring
    ↓
[Response Generation] → LLM with grounded context
    ↓
[Post-processing] → Safety checks, citation extraction
```

### Observability
- **Tracing**: LangSmith, Phoenix, OpenTelemetry
- **Metrics**: Latency P50/P95, token usage, retrieval quality
- **Logging**: Structured logs with request IDs
- **Alerting**: Regression detection, cost spikes

### Cost Optimization
- Model routing (use smaller models when possible)
- Semantic caching for repeated queries
- Response memoization
- Token-efficient prompts

### Safety & Governance
- Content moderation (OpenAI Moderation API)
- Prompt injection detection
- PII redaction in inputs/outputs
- Audit logging for compliance

## Decision Guides

### Choosing a Vector Database

| Requirement | Recommendation |
|:------------|:---------------|
| Managed, scalable | Pinecone |
| Open-source, hybrid search | Weaviate |
| High performance, on-premise | Milvus |
| Lightweight, prototyping | Chroma |
| PostgreSQL ecosystem | pgvector |

### Choosing Embedding Dimensions

| Use Case | Dimensions | Model |
|:---------|:-----------|:------|
| General purpose | 1536 | text-embedding-3-large |
| Cost-sensitive | 384 | all-MiniLM-L6-v2 |
| Multilingual | 1024 | e5-large-v2 |
| Domain-specific | Fine-tuned | BGE + training |

### Choosing Chunk Size

| Content Type | Chunk Size | Overlap |
|:-------------|:-----------|:--------|
| Dense technical docs | 512 tokens | 50 |
| Conversational content | 256 tokens | 25 |
| Long-form articles | 1000 tokens | 100 |
| Code repositories | Semantic chunks | - |

## Common Pitfalls

1. **Poor Retrieval Quality**: Fix with hybrid search + reranking
2. **Hallucinations**: Improve grounding prompts, add verification
3. **High Latency**: Implement caching, optimize chunk size
4. **Inconsistent Outputs**: Use temperature=0, structured outputs
5. **Cost Overruns**: Monitor token usage, implement routing
6. **Security Gaps**: Add prompt injection detection, PII filtering
