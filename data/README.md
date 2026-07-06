# Data

Local inputs required to reproduce the experiments. Heavy artifacts are **not**
versioned (size) and are ignored via `.gitignore`; keep them here locally.

The experimental collection is derived from **Dialog2Flow** (Burdisso et al.,
2024), which unifies several task-oriented dialogue corpora (e.g., MultiWOZ,
Schema-Guided Dialogue) under a common format. From it we retain 101,021 complete
dialogues (1,000,023 turns).

Expected local artifacts:

```
data/
├── dialogs-2.0.pkl                     # processed dialogues / turns
├── ids.npy                             # dialogue / turn identifiers
├── embeddings_minilm.npy               # static turn embeddings (all-MiniLM-L6-v2)
├── embeddings_mpnet.npy                # static turn embeddings (all-mpnet-base-v2)
├── embeddings_dialog2flow.npy          # static turn embeddings (dialog2flow-joint-bert-base)
├── accumulative_embeddings_minilm.npy  # normalized-cumulative states
├── accumulative_embeddings_mpnet.npy
└── accumulative_embeddings_dialog2flow.npy
```

Sources: Dialog2Flow — <https://github.com/idiap/dialog2flow> · MultiWOZ —
<https://github.com/budzianowski/multiwoz>
