# Architecture and Implementation Notes

This document describes the visible design and source layout of YourOwnAI. It is intended as a guide for readers of the code, not as a claim that the system has passed production-readiness testing.

## Components

### Browser UI — `index.html`

The single-page UI provides controls for choosing a search algorithm and distance metric, entering a query, selecting the top-k count, visualizing the seeded demo vectors, comparing search timings, inserting documents, and asking questions. The vector plot is a 2D visualization of demo data; it should not be presented as a direct rendering of all dimensions of the document embeddings.

### C++ HTTP server — `main.cpp`

The server uses `cpp-httplib` (included as `httplib.h`) to serve the application and expose HTTP routes. It contains the demo vector search logic, document/RAG workflow, and an Ollama HTTP client. The default configured model names in the source are `nomic-embed-text` for embeddings and `llama3.2` for generation.

### Search implementations

- **Brute force:** scores every indexed vector. This is the exact baseline against which approximate search can be evaluated.
- **KD-Tree:** partitions the search space by dimensions. Its pruning effectiveness typically weakens as dimensionality rises; verify behavior on the dimensions and metrics actually supported by this implementation.
- **HNSW:** navigates a layered proximity graph for approximate nearest-neighbour search. Recall and latency depend on graph construction and search parameters; neither should be assumed from the algorithm name alone.

The demo-vector index and document index serve different workflows. The seeded demo index uses 20 vectors with 16 dimensions. The document workflow obtains embeddings from Ollama and uses a separate document-oriented index.

## Request flows

### Demo search

```text
Query text/vector supplied by UI
        → GET /search
        → selected search implementation + metric
        → nearest results and timing metadata
        → UI result cards and highlighted plot points
```

### Document ingestion

```text
Document title and text
        → POST /doc/insert
        → text chunking in the backend
        → Ollama embedding request
        → embedding vectors inserted into document index
        → document/chunk list updated in UI
```

### Ask AI (RAG)

```text
Question
  → POST /doc/ask
  → question embedding from Ollama
  → retrieve top-k document chunks
  → pass retrieved context to Ollama generation model
  → return answer and retrieval context to UI
```

RAG means **retrieval-augmented generation**: the system retrieves potentially relevant text and supplies it to a language model as context. This reduces reliance on the model's parametric knowledge but does not guarantee factuality. Evaluate whether answers are supported by retrieved chunks and allow users to see the context used.

## API route map

| Route | Role |
|---|---|
| `/search` | Search seeded demo vectors |
| `/insert`, `/delete/:id`, `/items` | Demo vector mutations/listing |
| `/benchmark` | Compare search paths |
| `/hnsw-info`, `/stats` | Index information/statistics |
| `/doc/insert`, `/doc/list`, `/doc/delete/:id` | Document chunk management |
| `/doc/search` | Retrieve document chunks without generation |
| `/doc/ask` | Retrieval plus LLM generation |
| `/status` | Ollama status and model configuration |

Confirm exact schemas, validation rules, and error codes in `main.cpp` before treating this table as a stable external API contract.

## Known validation questions

Before describing the project as production-ready, answer these with tests or code references:

1. Are vectors and documents persisted, and what happens after restart?
2. Is HNSW recall measured against brute force on repeatable datasets?
3. Are benchmark results measured over multiple trials and meaningful dataset sizes?
4. Are embedding dimensions checked before insertion/search?
5. What happens when Ollama is offline, times out, or returns an error?
6. Are request bodies, document sizes, IDs, and JSON fields validated?
7. Are the server and API safe to expose beyond localhost?
8. Can the RAG evaluation set detect answers that are plausible but unsupported by the supplied context?
