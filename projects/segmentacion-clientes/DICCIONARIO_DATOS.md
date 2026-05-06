# Diccionario de Datos

## Dataset Original — Online Retail II (UCI ML Repository, ID 502)

**Fuente:** https://archive.ics.uci.edu/dataset/502/online+retail+ii
**Cobertura:** 2009-12-01 a 2011-12-09
**Granularidad:** una fila = una línea de factura.
**Total de filas:** ~1,067,371 (aproximado, antes de limpieza).

| Columna | Tipo | Descripción | Notas |
|---|---|---|---|
| `Invoice` | str/int | Número de factura (6 dígitos). Si empieza por **`C`** indica cancelación. | Las cancelaciones se eliminan en limpieza. |
| `StockCode` | str | Código del producto (5 dígitos). Algunos contienen letras (ej. `POST` = correo). | ~5,300 productos únicos. |
| `Description` | str | Nombre del producto en inglés. | Tiene faltantes y mayúsculas inconsistentes. |
| `Quantity` | int | Unidades compradas en esa línea. | Puede ser negativo (devoluciones). |
| `InvoiceDate` | datetime | Fecha y hora de la factura. | Formato `YYYY-MM-DD HH:MM:SS`. |
| `Price` | float | Precio unitario en libras esterlinas (GBP). | Puede ser 0 o negativo (raro, errores). |
| `CustomerID` | int (con NaN) | ID anonimizado del cliente. | ~25% de las filas tienen `NaN`. Se eliminan. |
| `Country` | str | País del cliente. | ~38 países; UK domina (~90%). |

> El archivo original viene en Excel (`.xlsx`) con dos hojas:
> `Year 2009-2010` y `Year 2010-2011`. El loader las concatena.

---

## Variables Derivadas (post limpieza)

### A nivel de transacción

| Columna | Tipo | Cálculo | Significado |
|---|---|---|---|
| `LineTotal` | float | `Quantity * Price` | Total monetario de esa línea. |

### A nivel de cliente (features finales)

Estas son las variables sobre las que se hace el clustering.
Se calculan agrupando todas las transacciones de cada `CustomerID`.

| Variable | Tipo | Cálculo | Interpretación de Negocio |
|---|---|---|---|
| `Recency` | int | Días desde la última compra hasta el snapshot date. | **Bajo = activo** · alto = inactivo. |
| `Frequency` | int | Número de facturas únicas. | **Alto = cliente fiel** · bajo = ocasional. |
| `Monetary` | float | Suma total gastada (GBP). | **Alto = cliente valioso**. |
| `AvgTicket` | float | `Monetary / Frequency`. | Gasto promedio por factura. Distingue volumen alto de tickets grandes. |
| `ProductDiversity` | int | Conteo de `StockCode` únicos comprados. | Alto = explorador · bajo = leal a pocos productos. |
| `AvgQuantity` | float | Cantidad promedio de items por factura. | Alto puede indicar comportamiento mayorista. |

---

## Snapshot Date

El **snapshot date** es la fecha de referencia para calcular `Recency`.

- **Convención del proyecto:** `max(InvoiceDate) + 1 día`.
- **Por qué `+1`:** evita que un cliente que compró el último día tenga
  `Recency = 0`, lo cual causa problemas con `log1p` y dificulta el
  ordenamiento.
- **Snapshot date típico para este dataset:** 2011-12-10.

---

## Notas de Calidad

| Problema | Acción tomada | Justificación |
|---|---|---|
| `CustomerID` faltante (~25%) | **Eliminar** filas. | No se pueden atribuir a un cliente; imputar sería inventar. |
| Cancelaciones (Invoice con `C`) | **Eliminar**. | No representan compras netas. Para un análisis profundo se podría restar al RFM, pero introduce complejidad. |
| `Quantity <= 0` o `Price <= 0` | **Eliminar**. | Errores o devoluciones; no aportan al perfil de cliente. |
| Duplicados exactos | **Eliminar**. | Son errores de carga, no transacciones distintas. |
| Outliers en `Quantity` y `Monetary` | **Conservar + transformar**. | Pueden ser mayoristas legítimos. La transformación `log1p` reduce su influencia sin descartarlos. |

---

## Tamaño Esperado tras Limpieza

| Etapa | Filas (aprox.) | Clientes únicos |
|---|---|---|
| Crudo | 1,067,371 | ~5,940 |
| Sin `CustomerID` faltante | ~805,000 | ~5,940 |
| Sin cancelaciones | ~785,000 | ~5,940 |
| Con `Quantity` y `Price` válidos | ~780,000 | ~5,890 |
| Sin duplicados | ~770,000 | ~5,880 |

> Las cifras exactas dependen de la versión del archivo de UCI.
> Los notebooks reportan los conteos reales al ejecutarse.
