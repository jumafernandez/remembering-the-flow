# remembering-the-flow

Companion code, notebooks, and experimental results for the paper **"Remembering
the Flow: Retrieval with Calibrated Dynamic Turn Representations for
Conversational Memory."**

Approximate nearest neighbor (**ANN**) retrieval over **static**, **cumulative**,
and **calibrated-EMA** turn representations for **conversational memory** in
task-oriented dialogue (TOD). Given a query turn, we retrieve similar situations
from *other* dialogues (cross-dialogue retrieval) and ask which turn
representation surfaces the most functionally plausible neighbors.

## Study at a glance

Over a one-million-turn collection derived from **Dialog2Flow** (101,021 dialogues
/ 1,000,023 turns) we compare three turn representations —

- **Static** — each turn encoded independently, `e_t`.
- **Normalized cumulative** — `h_t = LayerNorm(h_{t-1} + e_t)`, reset per dialogue.
- **EMA** — `h_t = LayerNorm((1 - α) · h_{t-1} + α · e_t)`, calibrated `α`.

— over three embedding spaces (`all-MiniLM-L6-v2`, `all-mpnet-base-v2`,
`dialog2flow-joint-bert-base`), retrieved with FAISS indexes (FlatL2, IVF, HNSW,
IVFPQ) and examined through three lenses: ANN fidelity/efficiency (Recall@k, QPS,
memory), representation geometry (cosine, Overlap@k), and an LLM-assisted
semantic–functional judgment (**MSS@10**) under a strict cross-dialogue protocol
with held-out queries.

**Main finding.** Accumulation does not help universally: over general-purpose
encoders it does not improve semantic–functional retrieval, while over the
conversation-aware Dialog2Flow space it does — and a calibrated EMA (`α = 0.6`)
improves it further, holding under the cross-dialogue protocol and on held-out
queries. The value of *remembering* depends on the base geometry.

## Repository layout

```
remembering-the-flow/
├── notebooks/   # experimental pipeline (see table below)
├── results/     # versioned outputs: benchmark_ann · geometry · semantic_llm · figures
├── data/        # inputs (heavy artifacts gitignored; see data/README.md)
├── requirements.txt
└── README.md
```

### Pipeline (`notebooks/`)

| Stage | Notebook(s) |
|---|---|
| Data loading | `notebook_01_carga_dialogos_dialog2flow` |
| Turn embeddings | `notebook_01b_…` (Dialog2Flow) · `notebook_01c_…` (general: MiniLM/MPNet) |
| ANN benchmark on static embeddings | `notebook_02_benchmark_ann_*` (one per space) |
| Dynamic (cumulative) states + geometry | `notebook_03_estados_latentes_dinamicos_{D2F,miniLM}` |
| Aggregate analysis & figures | `notebook_04_analisis_resultados` |
| Semantic eval (static vs cumulative) | `notebook_05_evaluacion_semantica_llm_…` |
| EMA family (`α` sweep) + analysis | `version_4/notebook_03_ema_…`, `…_04_…`, `…_05_…` |
| Intra-dialogue contamination diagnostic | `version_4/notebook_06_diagnostico_contaminacion_intradialogo` |
| Cross-dialogue evaluation, 500 queries | `version_5/notebook_07_evaluacion_semantica_500q_cross_dialogue` |

The `version_4/` and `version_5/` folders hold the later experimental stages (EMA
calibration and the cross-dialogue protocol) that extend the base pipeline.

## Results

`results/` is versioned for transparency:

- `benchmark_ann/` — ANN fidelity/efficiency: Recall@{1,10,100}, QPS, build time, memory.
- `geometry/` — cosine(static, cumulative) and Overlap@k.
- `semantic_llm/` — per-space LLM judgments: query metrics, MSS@10 ± SD summaries, paired static–cumulative deltas, and the raw query–neighbor score pairs.
- `figures/` — figures generated from the results.

Heavy artifacts (embeddings `.npy`, FAISS indexes, `.pkl`) are **not** versioned;
see [`data/README.md`](data/README.md).

## Reproduce

```bash
python -m venv venv && source venv/bin/activate    # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Place the required inputs in `data/` (see `data/README.md`), then run the
notebooks in order. If the embedding `.npy` files are already present, the
generation stages can be skipped.

The semantic–functional evaluation calls an LLM judge and requires an OpenAI API
key (never commit keys):

```python
import os
os.environ["OPENAI_API_KEY"] = "..."   # or a local .env (gitignored)
```

## Citation

If you use this material, please cite the associated paper:

> J. M. Fernández, S. Burdisso, M. Errecalde. *Remembering the Flow: Retrieval
> with Calibrated Dynamic Turn Representations for Conversational Memory.* CACIC 2026.

## Acknowledgment

This work began as a doctoral course project at the Universidad Nacional de San
Luis (UNSL) and is released as the experimental companion to the paper.
