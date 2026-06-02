# 📓 Notebooks del proyecto

Este directorio contiene las notebooks utilizadas para ejecutar y documentar el pipeline experimental del trabajo.

Las notebooks corresponden a las distintas etapas del análisis sobre **búsqueda aproximada de vecinos (ANN)** aplicada a embeddings conversacionales estáticos y acumulativos. Algunas etapas se encuentran separadas por modelo de embedding o por variante experimental para preservar las salidas ejecutadas y facilitar la trazabilidad de los resultados.

---

## 🧭 Organización general

El flujo experimental se organiza en cinco etapas:

1. **Preparación de datos**
2. **Generación de embeddings**
3. **Evaluación ANN sobre embeddings estáticos**
4. **Construcción y análisis de embeddings acumulativos**
5. **Evaluación semántico-funcional asistida por LLM**

---

## 📂 Notebooks principales

### `notebook_01_carga_datos_dialog2flow.ipynb`

Carga y preparación de la colección experimental derivada de **Dialog2Flow**.

Esta etapa construye o carga el archivo base de diálogos/turnos utilizado por el resto del pipeline.

**Salidas esperadas:**

- `dialogs-2.0.pkl`
- `ids.npy`

---

### `notebook_01b_generacion_embeddings_dialog2flow.ipynb`

Generación o carga de embeddings específicos para flujos conversacionales mediante:

- `dialog2flow-joint-bert-base`

**Salida esperada:**

- `embeddings_dialog2flow.npy`

---

### `notebook_01c_generacion_embeddings_generales.ipynb`

Generación o carga de embeddings generales de turno mediante modelos de Sentence Transformers:

- `all-MiniLM-L6-v2`
- `all-mpnet-base-v2`

**Salidas esperadas:**

- `embeddings_minilm.npy`
- `embeddings_mpnet.npy`

---

### `notebook_02_benchmark_ann_embeddings.ipynb`

Construcción y evaluación de índices de búsqueda aproximada de vecinos sobre embeddings estáticos.

Incluye:

- `FlatL2`
- `IVF`
- `HNSW`
- `IVFPQ`

Métricas principales:

- `Recall@1`
- `Recall@10`
- `Recall@100`
- QPS
- memoria estimada del índice

---

### `notebook_03_estados_acumulativos.ipynb`

Construcción de embeddings acumulativos normalizados a partir de los embeddings estáticos.

La actualización utilizada es:

```text
h_t = LayerNorm(h_{t-1} + e_t)
```

El estado acumulativo se reinicia al comienzo de cada diálogo y los turnos se procesan respetando el orden conversacional.

**Salidas esperadas:**

- `accumulative_embeddings_minilm.npy`
- `accumulative_embeddings_mpnet.npy`
- `accumulative_embeddings_dialog2flow.npy`

También se calculan métricas geométricas como:

- similitud coseno entre embeddings estáticos y acumulativos;
- `Overlap@1`;
- `Overlap@10`;
- `Overlap@100`.

---

### `notebook_04_analisis_resultados.ipynb`

Análisis agregado de resultados experimentales y generación de figuras utilizadas en el paper.

Incluye, entre otros elementos:

- curvas `Recall@10` / `Recall@100` versus QPS;
- análisis geométrico de embeddings acumulativos;
- visualizaciones UMAP;
- tablas resumen.

---

### `notebook_05_evaluacion_semantica_llm.ipynb`

Evaluación semántico-funcional asistida por LLM sobre los vecinos recuperados.

Calcula:

- `SS@10`;
- `MSS@10`;
- desvío estándar entre consultas;
- comparación pareada entre variantes estáticas y acumulativas.

Esta etapa permite analizar si los vecinos recuperados son no sólo próximos geométricamente, sino también plausibles desde el punto de vista conversacional.

---

## ⚠️ Notas sobre trazabilidad

Algunas notebooks pueden aparecer duplicadas o separadas por modelo de embedding. Esto se debe a que se conservaron ejecuciones específicas para preservar resultados intermedios, salidas impresas y configuraciones utilizadas durante el desarrollo experimental.

En una futura refactorización, estas notebooks podrán integrarse en una versión más modular, manteniendo la misma lógica experimental.

---

## 🔐 API key

La evaluación semántico-funcional con LLM requiere una API key de OpenAI.

No incluir claves en las notebooks ni en el repositorio. Configurar la clave mediante variable de entorno:

```python
import os
os.environ["OPENAI_API_KEY"] = "TU_API_KEY"
```

---

## 📌 Orden recomendado de ejecución

```text
01 → 01b/01c → 02 → 03 → 04 → 05
```

En caso de contar con los archivos `.npy` ya generados en `data/`, las etapas de generación de embeddings pueden omitirse.
