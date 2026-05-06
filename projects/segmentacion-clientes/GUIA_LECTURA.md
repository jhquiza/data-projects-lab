# Guía de Lectura: Segmentación de Clientes

> Esta guía está pensada para que un estudiante recorra el proyecto en el
> orden correcto y dedique el tiempo aproximado adecuado a cada etapa.

## Objetivo

Aprender, mediante un caso real, el flujo completo de un proyecto de
**clustering no supervisado**: desde la comprensión del problema de negocio
hasta una demo en Streamlit, pasando por limpieza, feature engineering,
pipelines, K-Means, DBSCAN e interpretación de segmentos.

**Dataset:** Online Retail II (UCI, ~1M transacciones, ~5.8k clientes).
**Algoritmos:** K-Means y DBSCAN.
**Demo:** Streamlit interactivo.

---

## Orden de Lectura Sugerido (~6 horas en total)

### 0. Contexto (15 min)

```
README.md  →  DICCIONARIO_DATOS.md
```

**Qué leer:**
- Caso de negocio y objetivo pedagógico (en `README.md`).
- Diccionario de variables del dataset.

---

### 1. Comprensión de Datos (30 min)

```
notebooks/01_data_understanding.ipynb
```

**Qué aprenderás:**
- Cómo se ve un dataset transaccional real.
- Primer diagnóstico de calidad (faltantes, tipos, rangos).
- Por qué `CustomerID` con NaN es un problema serio.
- Detección de cancelaciones y precios negativos.

---

### 2. Limpieza (45 min)

```
notebooks/02_data_cleaning.ipynb
```

**Qué aprenderás:**
- Decisiones justificadas: ¿imputar, eliminar o transformar?
- Riesgos de una mala limpieza (sesgar segmentos).
- Manejo de duplicados y filas inválidas.

---

### 3. Feature Engineering (45 min)

```
notebooks/03_feature_engineering.ipynb
```

**Qué aprenderás:**
- Qué es RFM y por qué funciona en marketing.
- Cómo extender RFM con `AvgTicket`, `ProductDiversity`, `AvgQuantity`.
- Agregaciones por cliente con pandas.

**Código clave:** `src/features/rfm.py`

---

### 4. EDA (45 min)

```
notebooks/04_exploratory_data_analysis.ipynb
```

**Qué aprenderás:**
- Distribuciones sesgadas (long-tail) y por qué importan.
- Boxplots para detectar outliers.
- Correlaciones entre variables RFM.
- Por qué necesitamos transformaciones antes de clusterizar.

---

### 5. Pipelines de Preprocesamiento (45 min)

```
notebooks/05_preprocessing_pipelines.ipynb
```

**Qué aprenderás:**
- `Pipeline` y `ColumnTransformer` de scikit-learn.
- Cuándo usar `StandardScaler` vs `MinMaxScaler`.
- Por qué `log1p` antes del escalado mejora el clustering en RFM.
- Pipelines combinados (numérico + categórico) y con PCA.

**Código clave:** `src/features/preprocessing.py`

---

### 6. K-Means (60 min)

```
notebooks/06_clustering_kmeans.ipynb
```

**Qué aprenderás:**
- Concepto de centroide y asignación.
- Método del codo (inertia vs k).
- Silhouette Score: cuándo es alto, cuándo es bajo.
- Cómo interpretar los centroides en escala original.

**Código clave:** `src/models/clustering.py`

---

### 7. DBSCAN (60 min)

```
notebooks/07_clustering_dbscan.ipynb
```

**Qué aprenderás:**
- Diferencia conceptual con K-Means.
- `eps`, `min_samples`, puntos núcleo, frontera y ruido.
- Curva k-distance para elegir `eps`.
- Limitaciones en alta dimensionalidad.

---

### 8. Comparación e Interpretación (60 min)

```
notebooks/08_model_comparison_and_interpretation.ipynb
```

**Qué aprenderás:**
- Tabla comparativa K-Means vs DBSCAN.
- Cómo perfilar segmentos (averages por cluster).
- Cómo asignar nombres de negocio a clusters.
- Recomendaciones de acción por segmento.

**Código clave:** `src/models/profiling.py`

---

### 9. Demo en Streamlit (30 min)

```
app/streamlit_app.py
```

**Qué hacer:**
1. Ejecutar `uv run streamlit run app/streamlit_app.py`.
2. Probar diferentes valores de `k` y `eps`.
3. Observar cómo cambian los perfiles.
4. Discutir con un compañero qué configuración tiene más sentido de negocio.

---

## Resumen de Archivos Clave

| Archivo | Propósito |
|---|---|
| `src/config.py` | Rutas, constantes, hiperparámetros. |
| `src/data/loader.py` | Descarga y carga del dataset. |
| `src/features/rfm.py` | Limpieza + features por cliente. |
| `src/features/preprocessing.py` | Pipelines sklearn. |
| `src/models/clustering.py` | K-Means, DBSCAN, métricas. |
| `src/models/profiling.py` | Perfilamiento y etiquetado de segmentos. |
| `src/visualization/plots.py` | Gráficos reutilizables. |
| `app/streamlit_app.py` | Demo interactiva. |

---

## Tips para Maximizar Tu Aprendizaje

1. **Ejecuta cada celda y juega con los parámetros.** Cambia `k=4` por `k=8`,
   cambia `eps=0.5` por `eps=2.0`. Observa qué pasa.
2. **Responde las preguntas de reflexión** al final de cada notebook antes
   de continuar al siguiente.
3. **No copies y pegues sin entender.** Si no entiendes una línea, abre
   la documentación de scikit-learn.
4. **Discute con un compañero.** Las decisiones de negocio en clustering
   rara vez son obvias.
5. **Toma notas en un cuaderno aparte.** Anota las decisiones que tomarías
   tú diferente.
