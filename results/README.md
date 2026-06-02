# 📈 Resultados experimentales

Esta carpeta contiene las salidas generadas durante los experimentos del proyecto sobre **búsqueda aproximada de vecinos (ANN)** aplicada a embeddings conversacionales estáticos y acumulativos.

A diferencia de la carpeta `data/`, que contiene insumos pesados no versionados, esta carpeta incluye archivos de resultados, métricas, figuras y salidas intermedias livianas que permiten revisar y auditar los experimentos realizados.

---

## 📁 Estructura

```text
results/
├── benchmark_ann/
├── figures/
├── geometry/
└── semantic_llm/
```

---

## 🔎 `benchmark_ann/`

Contiene resultados de evaluación de índices ANN implementados con **FAISS**.

Los experimentos comparan:

- `FlatL2`, como referencia exacta;
- `IVF`;
- `HNSW`;
- `IVFPQ`.

Las métricas principales incluyen:

- `Recall@1`;
- `Recall@10`;
- `Recall@100`;
- QPS (*queries per second*);
- tiempo de búsqueda;
- tiempo de construcción del índice;
- memoria estimada.

Estos archivos permiten analizar el compromiso entre **calidad de recuperación**, **velocidad de consulta** y **memoria** para cada espacio de embedding.

---

## 🧭 `geometry/`

Contiene resultados asociados al análisis geométrico de las representaciones acumulativas.

En particular, se incluyen métricas como:

- similitud coseno entre embeddings estáticos y acumulativos;
- `Overlap@1`;
- `Overlap@10`;
- `Overlap@100`.

Estas métricas permiten observar en qué medida la acumulación secuencial modifica la estructura local de vecindad inducida por cada espacio de representación.

---

## 🧠 `semantic_llm/`

Contiene las salidas de la evaluación semántico-funcional asistida por LLM.

Esta etapa evalúa si los vecinos recuperados son no sólo próximos geométricamente, sino también plausibles desde el punto de vista conversacional.

Los archivos pueden incluir:

- pares consulta-vecino evaluados;
- puntajes asignados por el LLM juez;
- métricas `SS@10`;
- resúmenes `MSS@10 ± SD`;
- comparación pareada entre variantes estáticas y acumulativas;
- porcentajes de consultas donde la variante acumulativa supera a la estática.

El resultado principal de esta etapa muestra que la mejora semántico-funcional de la variante acumulativa se concentra en `Dialog2Flow`.

---

## 🖼️ `figures/`

Contiene figuras generadas a partir de los resultados experimentales.

Entre ellas:

- curvas `Recall@10` y `Recall@100` versus QPS;
- análisis exploratorio de embeddings estáticos y acumulativos;
- visualizaciones UMAP;
- figuras utilizadas en el artículo asociado.

Estas figuras se incluyen para facilitar la revisión de los resultados y la reproducción de las visualizaciones del paper.

---

## ✅ Criterio de versionado

Esta carpeta sí se versiona en GitHub para favorecer la transparencia experimental.

Se incluyen:

- archivos `.csv`;
- archivos `.json` o `.jsonl` de resultados;
- figuras `.png` o `.pdf`;
- salidas tabulares livianas.

No se incluyen aquí los artefactos pesados del pipeline, como:

- embeddings `.npy`;
- índices FAISS `.faiss`;
- archivos `.pkl`;
- resultados de recuperación `.npz` pesados.

Esos archivos deben ubicarse localmente en `data/` y están excluidos por `.gitignore`.

---

## 📌 Nota sobre trazabilidad

Algunos archivos pueden corresponder a corridas específicas por embedding, variante o fecha de ejecución. Se conservaron para mantener trazabilidad del proceso experimental y permitir inspeccionar los resultados que dieron origen a las tablas y figuras del trabajo.

En una futura refactorización, estos resultados podrán reorganizarse en salidas consolidadas, manteniendo los archivos originales como respaldo.
