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
