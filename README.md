# 🔎 Búsqueda aproximada de vecinos sobre embeddings conversacionales

**Trabajo Final**  
Curso: *Algoritmos Recientes para Búsqueda de Proximidad en Altas Dimensiones*  
Doctorado en Ciencias de la Computación  
Universidad Nacional de San Luis (UNSL)

Docentes: **Edgar Chávez** y **Nora Reyes**

Autor: **Juan Manuel Fernández**

---

## 📌 Descripción

Este repositorio contiene el código, notebooks y resultados experimentales del trabajo final del curso *Algoritmos Recientes para Búsqueda de Proximidad en Altas Dimensiones*.

El trabajo estudia la **búsqueda aproximada de vecinos** (*Approximate Nearest Neighbor Search*, ANN) sobre **embeddings estáticos y acumulativos** para memoria conversacional en diálogos orientados a tareas.

El objetivo principal es analizar cómo distintas representaciones vectoriales de situaciones conversacionales se comportan bajo índices ANN, considerando tres dimensiones:

- eficiencia de recuperación;
- estructura geométrica del espacio vectorial;
- plausibilidad semántico-funcional de los vecinos recuperados.

---

## 🧠 Resumen del experimento

Se construyó una colección experimental a partir de **Dialog2Flow**, compuesta por:

- **101.021 diálogos completos**;
- **1.000.023 turnos conversacionales**.

Sobre esta colección se evaluaron tres espacios de representación:

1. `all-MiniLM-L6-v2`
2. `all-mpnet-base-v2`
3. `dialog2flow-joint-bert-base`

Para cada espacio se consideraron dos variantes:

- **embeddings estáticos**, donde cada turno se codifica de manera independiente;
- **embeddings acumulativos**, donde se incorpora información secuencial del historial mediante:

```text
h_t = LayerNorm(h_{t-1} + e_t)
```

La recuperación se evaluó con índices implementados en **FAISS**:

- `FlatL2` como referencia exacta;
- `IVF`;
- `HNSW`;
- `IVFPQ`.

---

## 📊 Métricas evaluadas

La evaluación combina tres niveles de análisis.

### 1. Recuperación ANN

Se evaluó la fidelidad de los índices aproximados respecto de la búsqueda exacta mediante:

- `Recall@1`
- `Recall@10`
- `Recall@100`

También se midieron:

- QPS (*queries per second*);
- tiempo de búsqueda;
- tiempo de construcción del índice;
- memoria estimada del índice.

### 2. Geometría de representaciones acumulativas

Para estudiar el efecto de la acumulación secuencial se calcularon:

- similitud coseno entre embedding estático y acumulativo;
- `Overlap@1`;
- `Overlap@10`;
- `Overlap@100`.

### 3. Evaluación semántico-funcional

Además de las métricas geométricas, se incorporó una evaluación asistida por LLM sobre los vecinos recuperados.

Para cada consulta se evaluaron los vecinos del top-10 mediante un puntaje ordinal de 1 a 5. A partir de esos puntajes se calcularon:

- `SS@10`: promedio semántico-funcional por consulta;
- `MSS@10`: promedio global sobre la muestra de consultas;
- comparación pareada entre variante estática y acumulativa.

---

## 🧪 Hallazgo principal

Los resultados muestran que la acumulación secuencial **no mejora de manera universal** cualquier espacio de embeddings.

En particular:

- sobre `MiniLM` y `MPNet`, la variante acumulativa no mejora la plausibilidad semántico-funcional de las vecindades recuperadas;
- sobre `Dialog2Flow`, la variante acumulativa obtiene los valores más altos de `MSS@10` bajo `IVF`, `HNSW` e `IVFPQ`;
- en `Dialog2Flow`, la variante acumulativa supera a la estática en la mayoría de las consultas evaluadas.

Esto sugiere que la utilidad de las representaciones acumulativas depende de la **geometría del espacio base**, y que pueden resultar especialmente efectivas cuando se aplican sobre embeddings orientados a regularidades de flujos conversacionales.

---

## 📁 Estructura del repositorio

```text
ANN-UNSL/
├── data/
│   └── README.md
│
├── notebooks/
│   ├── notebook_01_carga_datos_dialog2flow.ipynb
│   ├── notebook_01b_generacion_embeddings_dialog2flow.ipynb
│   ├── notebook_01c_generacion_embeddings_generales.ipynb
│   ├── notebook_02_benchmark_ann_embeddings.ipynb
│   ├── notebook_03_estados_acumulativos.ipynb
│   ├── notebook_04_analisis_resultados.ipynb
│   └── notebook_05_evaluacion_semantica_llm.ipynb
│
├── resultados/
│   ├── figuras/
│   ├── semantic_llm/
│   └── *.csv
│
├── paper/
│   ├── main.tex
│   ├── references.bib
│   └── figures/
│
├── requirements.txt
├── .gitignore
└── README.md
```

> Nota: algunas notebooks tienen variantes por embedding o por etapa experimental. Se conservaron para mantener trazabilidad de las ejecuciones utilizadas en el trabajo.

---

## 💾 Datos e insumos

Los archivos de datos pesados **no se incluyen en el repositorio**.

La carpeta `data/` debe contener localmente los artefactos necesarios para reproducir los experimentos:

```text
data/
├── dialogs-2.0.pkl
├── ids.npy
├── embeddings_minilm.npy
├── embeddings_mpnet.npy
├── embeddings_dialog2flow.npy
├── accumulative_embeddings_minilm.npy
├── accumulative_embeddings_mpnet.npy
└── accumulative_embeddings_dialog2flow.npy
```

Los archivos `.pkl` y `.npy` se excluyen de GitHub por tamaño.

---

## 📓 Descripción de las notebooks

### `notebook_01_*`

Carga y preparación de datos a partir de Dialog2Flow.  
Incluye la construcción de la colección experimental y la generación de embeddings base.

### `notebook_02_*`

Construcción y evaluación de índices ANN sobre embeddings estáticos.  
Incluye `FlatL2`, `IVF`, `HNSW` e `IVFPQ`.

### `notebook_03_*`

Construcción de representaciones acumulativas normalizadas.  
Calcula similitud entre representaciones estáticas y acumulativas, y medidas de superposición de vecindades.

### `notebook_04_*`

Análisis agregado de resultados experimentales y generación de figuras/tablas.

### `notebook_05_*`

Evaluación semántico-funcional asistida por LLM.  
Calcula `MSS@10`, desvíos estándar y comparación pareada entre variantes estáticas y acumulativas.

---

## 📈 Resultados incluidos

El repositorio incluye salidas experimentales en la carpeta `resultados/`, tales como:

- métricas ANN;
- resultados geométricos;
- evaluación semántico-funcional con LLM;
- figuras utilizadas en el paper;
- archivos CSV/JSONL de soporte para trazabilidad.

Los datos pesados, embeddings e índices FAISS no se versionan.

---

## ⚙️ Reproducción de los experimentos

### 1. Clonar el repositorio

```bash
git clone https://github.com/<usuario>/<repositorio>.git
cd <repositorio>
```

### 2. Crear entorno virtual

```bash
python -m venv venv
source venv/bin/activate
```

En Windows:

```bash
venv\Scripts\activate
```

### 3. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 4. Ubicar los datos

Colocar los archivos requeridos en `data/`, según se detalla en `data/README.md`.

### 5. Ejecutar notebooks

Ejecutar las notebooks en el orden indicado:

```text
01 → 02 → 03 → 04 → 05
```

---

## 🔐 Uso de API key para evaluación LLM

La evaluación semántico-funcional requiere una API key de OpenAI.

No incluir claves en el repositorio.

Puede configurarse en el entorno mediante:

```python
import os
os.environ["OPENAI_API_KEY"] = "TU_API_KEY"
```

o mediante un archivo `.env` local, excluido por `.gitignore`.

---

## 📄 Paper

El artículo asociado se encuentra en la carpeta:

```text
paper/
```

Título:

**Búsqueda aproximada de vecinos sobre embeddings estáticos y acumulativos para memoria conversacional en diálogos orientados a tareas**

---

## 🧰 Dependencias principales

- Python 3.10+
- NumPy
- Pandas
- FAISS
- Sentence Transformers
- Scikit-learn
- UMAP
- Matplotlib
- OpenAI API

---

## 👤 Autor

**Juan Manuel Fernández**  
Doctorado en Ciencias de la Computación  
Universidad Nacional de San Luis (UNSL)  
