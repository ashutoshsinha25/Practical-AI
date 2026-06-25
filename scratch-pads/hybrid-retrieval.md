# BIER Benchmark

The **BIER Benchmark** is an evaluation framework for information retrieval models. It provides a standardized collection of retrieval datasets, common data formats, and evaluation metrics to enable fair comparison of different retrieval approaches across diverse domains and tasks.

## Benchmark Structure

The benchmark is organized as a collection of independent retrieval datasets. Each dataset follows the same standardized format:

* **Corpus (`corpus.jsonl`)** – Collection of searchable documents.
* **Queries (`queries.jsonl`)** – User search queries or questions.
* **Qrels (`qrels/*.tsv`)** – Ground-truth relevance judgments mapping queries to relevant documents.

This standardized structure allows the same retrieval pipeline to be evaluated across all datasets without changing the data format.

## Evaluation Workflow

```text
Dataset
 ├── corpus.jsonl
 ├── queries.jsonl
 └── qrels/*.tsv
        │
        ▼
 Retrieval Model
        │
        ▼
 Ranked Documents
        │
        ▼
 Compare with Qrels
        │
        ▼
 Evaluation Metrics
 (nDCG, Recall@k, MAP, MRR, Precision@k)
```

The retrieval model searches the **corpus** for each **query**, produces a ranked list of documents, and the rankings are compared against the **qrels** to compute retrieval performance metrics.



# BEIR Dataset Components

## 1. Corpus (`corpus.jsonl`)

The **corpus** is the collection of documents that the retrieval system searches over. Each document has a unique identifier and contains the text (and optionally a title).

**Example Structure**

```json
{
  "_id": "doc1",
  "title": "Neural Information Retrieval",
  "text": "Information retrieval is the process of obtaining relevant information..."
}
```

---

## 2. Queries (`queries.jsonl`)

The **queries** represent the user search requests or questions. Each query has a unique identifier and the query text.

**Example Structure**

```json
{
  "_id": "q1",
  "text": "What is information retrieval?"
}
```

---

## 3. Qrels / Relations (`qrels/*.tsv`)

The **qrels (query relevance judgments)** define the ground-truth relationship between queries and corpus documents by indicating which documents are relevant to each query.

**Example Structure**

```tsv
query-id    corpus-id    score
q1          doc1         1
q1          doc8         2
q2          doc5         1
```

* **query-id**: References a query in `queries.jsonl`
* **corpus-id**: References a document in `corpus.jsonl`
* **score**: Relevance label (higher values indicate greater relevance)

---

# How They Are Connected

```
queries.jsonl
      │
      ▼
Search over
      │
corpus.jsonl
      │
Retrieved Documents
      │
Compared against
      ▼
qrels/*.tsv
```

* The **query** is used to search the **corpus**.
* The retrieval system returns a ranked list of documents from the corpus.
* The **qrels** provide the ground-truth mapping of which corpus documents are relevant for each query and are used to evaluate retrieval performance.



# BM25 (Sparse Retrieval)

**BM25** is a lexical retrieval algorithm that ranks documents based on keyword matching between the query and the document. It considers term frequency, inverse document frequency (IDF), and document length normalization to compute a relevance score.

## Ranking

For a given query:

1. Tokenize the query and documents.
2. Compute the BM25 score for each document.
3. Rank documents in descending order of their BM25 scores.
4. Return the top-*k* highest-scoring documents.

```text
Query
   │
   ▼
Token Matching
   │
   ▼
BM25 Score
   │
   ▼
Rank Documents (Highest Score → Lowest Score)
```

---

# Dense Embedding Retrieval

**Dense retrieval** represents both queries and documents as dense vector embeddings using a neural embedding model. Retrieval is performed by measuring semantic similarity between the query embedding and document embeddings.

## Ranking

For a given query:

1. Generate the query embedding.
2. Compare it with all document embeddings using a similarity metric (e.g., cosine similarity or dot product).
3. Compute a similarity score for each document.
4. Rank documents in descending order of similarity.

```text
Query
   │
   ▼
Embedding Model
   │
   ▼
Query Embedding
   │
   ▼
Similarity with Document Embeddings
   │
   ▼
Rank Documents (Highest Similarity → Lowest Similarity)
```

---

# Reciprocal Rank Fusion (RRF)

**Reciprocal Rank Fusion (RRF)** is a rank fusion technique that combines the ranked outputs of multiple retrieval methods (e.g., BM25 and Dense Retrieval) into a single ranking. Instead of combining scores directly, RRF combines the **ranks** assigned by each retriever.

## Ranking

For each document, the RRF score is computed as:

```text
RRF Score = Σ 1 / (k + rank_i)
```

where:

* **rank_i** = Rank assigned by the *i*th retrieval method.
* **k** = A constant (commonly 60) used to reduce the influence of very high ranks.

Documents that consistently appear near the top across multiple retrieval methods receive higher RRF scores.

```text
                Query
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
      BM25             Dense Retrieval
        │                   │
   Ranked List A      Ranked List B
        └─────────┬─────────┘
                  ▼
      Reciprocal Rank Fusion
                  │
                  ▼
      Final Unified Ranked List
```
