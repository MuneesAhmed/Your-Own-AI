# Portfolio Publication Checklist

Use this checklist before sharing the repository with recruiters or interviewers.

## Must do

- [ ] Review the repository's upstream history and preserve appropriate attribution.
- [ ] Confirm the actual license and include a `LICENSE` file only when the rights and terms are clear.
- [ ] Add clean screenshots for Search, Benchmark, Documents, and Ask AI to `docs/screenshots/`.
- [ ] Capture document ingestion with a non-zero document count and a question whose answer is present in the inserted text.
- [ ] Remove generated binaries (`db.exe`, `main.obj`, object files) from version control and verify `.gitignore`.
- [ ] Confirm the clone/build/run commands work on a clean Windows environment.
- [ ] Test Ollama-offline behavior and write down the expected error message.
- [ ] Test empty input, malformed input, delete of unknown ID, and process restart.
- [ ] Replace performance claims with reproducible results or clearly label them as illustrative.
- [ ] Add automated tests or a repeatable test script for metric calculations and search correctness.

## Good additions

- [ ] Add a short GIF or video showing one search, one benchmark, document ingestion, and one grounded Q&A example.
- [ ] Add a benchmark CSV with hardware, compiler flags, dataset size/dimensions, metric, k, warm-up count, trial count, and median/p95 latency.
- [ ] Add a small retrieval evaluation set with expected relevant chunks and report Recall@k / MRR.
- [ ] Add a simple architecture diagram and note any known limitations.
- [ ] Pin the repository on GitHub only after the README and setup have been verified.

## Interview framing

Be prepared to explain:

1. Why brute force is the correctness baseline.
2. Why HNSW is approximate and which parameters affect recall/latency.
3. Why KD-Tree pruning can degrade in high dimensions.
4. How cosine similarity differs from Euclidean and Manhattan distance.
5. How text becomes an embedding, how chunks are retrieved, and how retrieved context reaches the LLM.
6. How you tested retrieval quality and avoided unsupported RAG answers.
7. Which parts were inherited from upstream, which parts you changed, and what you personally learned or implemented.
