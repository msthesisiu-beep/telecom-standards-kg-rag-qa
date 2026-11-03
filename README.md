# kg-rag-thesis

Implementation of **Post-Rerank Knowledge Graph Augmentation for Telecom Standards Question Answering: A Controlled
Quality-Cost Ablation Study** (Raju KSR), following Chapters 3–4 and Appendices 2–6.

## Run (VS Code)
1. Install Google's [Colab VS Code extension](https://marketplace.visualstudio.com/items?itemName=Google.colab), open this
   notebook, then choose **Select Kernel → Colab → New Colab Server** and sign in with the Google account that has G4 access.
   Select a G4 GPU if offered. This keeps editing and execution in VS Code while the notebook runs on Colab's remote kernel.
   Alternatively, select the local project kernel; this computer has no CUDA GPU and will run on CPU.
2. With the local kernel, install dependencies (`pip install -r requirements.txt` and `pip install -e .`). The Colab
   kernel installs them from the project in Drive when Part 1 runs.
3. Before the paid run, verify OpenAI API billing is active. For a local kernel, put `OPENAI_API_KEY` in the ignored `.env`.
   For a Colab kernel, add `OPENAI_API_KEY` to Colab Secrets and enable notebook access; the remote runtime cannot read the
   local `.env`. Never commit or paste the key into notebook source.
4. Ensure the project and its already-downloaded `data/raw/` files are in `MyDrive/kg-rag-thesis` for the Colab kernel.
   The currently available five Release 17 Markdown files mean no Hugging Face token is needed for this sampled run.
5. Run Parts 1–3, execute the notebook's safe preflight cell and confirm it reports the Colab runtime, a GPU, an OpenAI key
   and the local inputs. Only then run
   **OpenAI local results run · exploratory only**.
   Do not run Gate 1's model probe or the full thesis stages if you only want the sampled run.
6. This profile samples 50 questions, 300 passages and 20 KG-extraction passages; runs B/C on up to 50 questions, 0/A/D
   on up to 10, and evaluates up to five. Results and the persistent $5 local budget ledger are isolated under
   `data/interim/smoke50_openai/`. Output includes answer, retrieval, evaluation and cost summaries. The budget guard
   reserves estimated request costs before sending; it is not a guarantee of provider billing or available account funds.
7. The OpenAI roles share one model and the sample skips thesis audit/held-out gates. Treat all outputs as exploratory,
   not thesis-validated evidence. Runtime depends on model downloads and local CPU/GPU speed; 30–50 minutes is not
   guaranteed. The current VS Code path uses CUDA automatically only where the relevant encoder code permits it.

## Run (Google Colab)
1. Upload `kg-rag-thesis-code.zip` to `MyDrive/`.
2. Open `notebooks/KG_RAG_Raju_KSR_v2.ipynb` in Colab with a T4 GPU, and add the `GEMINI_API_KEY` and `HF_TOKEN` secrets.
3. Run the cells in order. Cells 07 and 11 wait for your blind audits.

## Layout
| Path | Content |
|---|---|
| `configs/` | `study.yaml` (every thesis parameter), `models.yaml` (provider roles, prices and budget cap), `statistical_plan.yaml` |
| `src/kgrag/data` | TeleQnA, TSpec-LLM, 3GPP version check, chunker v2.4, corpus quality |
| `src/kgrag/validation` | BM25 + E5 pooling, configured-provider verifier, X1–X7 register, blind audits |
| `src/kgrag/benchmark` | freeze + stratified 20/80 split with hashes |
| `src/kgrag/retrieval`, `reranking` | BGE + FAISS top-30; bge-reranker-base + development threshold |
| `src/kgrag/knowledge_graph` | extraction, normalisation, fidelity verification, NetworkX graph, C/D/control selection |
| `src/kgrag/experiments` | configurations 0/A/B/C/D, budget modes, invariance checks, resumable runner, ablation selection |
| `src/kgrag/evaluation` | script metrics, RAGAs-protocol evaluator, judged triple relevance |
| `src/kgrag/statistics` | Appendix 6 tests, test families, quality–cost/Pareto |
| `src/kgrag/reporting` | Chapter 5 figures/tables + results manifest |
| `src/kgrag/workflow.py` | stage functions called by the notebook (with gates) |
| `artifacts/diagrams` | Figures 4.1–4.3 (`.mmd` editable source, `.png`, `.svg`) |
| `literature_review/` | Appendix 5 κ recomputation (validated) |
| `reports/` | methodology audit, compliance matrix, implementation status, model selection, gate tracker |

## Tests
`pip install -r requirements.txt && pip install -e . && pytest -q`: 33 tests, synthetic fixtures only.

## Data and licences
TeleQnA (MIT, password-protected archive) and TSpec-LLM (CC-BY-NC-4.0) are downloaded at run time and are **not**
redistributed. `.gitignore` excludes raw data, interim files and JSONL logs that contain corpus text. The register
publishes IDs and reason codes only.

## Publishing to a private GitHub repository
```bash
cd kg-rag-thesis
gh repo create kg-rag-thesis --private --source . --push
```
