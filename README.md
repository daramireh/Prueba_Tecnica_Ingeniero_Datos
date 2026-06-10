# Prueba Tecnica - Ingeniero de Datos

## Contexto

Una empresa de e-commerce brasilena necesita construir un pipeline de datos que procese informacion de pedidos, clientes, productos, pagos y resenas. Los datos provienen de multiples fuentes CSV y contienen **errores reales**: valores nulos, duplicados, formatos inconsistentes y registros invalidos.

Tu objetivo es disenar e implementar un **ETL con arquitectura medallion (Bronze / Silver / Gold)** que transforme estos datos crudos en tablas analiticas confiables, con **observabilidad en cada etapa** del proceso.

---

## Que recibiras

```
prueba-tecnica/
├── README.md              ← Este archivo
└── data/
    └── raw/               ← CSVs con los datos crudos
```

**Datasets disponibles** (en `data/raw/`):

| Archivo | Registros | Descripcion |
|---|---|---|
| `customers.csv` | ~5,000 | Clientes con nombre, email, ciudad, estado |
| `products.csv` | ~1,000 | Productos con categoria, precio, peso |
| `orders.csv` | ~15,000 | Pedidos con status y fechas |
| `order_items.csv` | ~25,000 | Items por pedido (producto, cantidad, precio) |
| `payments.csv` | ~15,000 | Pagos asociados a pedidos |
| `reviews.csv` | ~10,000 | Resenas con score y comentarios |

---

## Requerimientos

### Infraestructura

- **Docker obligatorio**: tu solucion debe ejecutarse con `docker compose up` (o `docker-compose up`).
- El evaluador no instalara dependencias locales. Todo debe correr dentro de contenedores.
- Lenguaje: **Python**.
- Puedes usar las librerias que consideres necesarias (Polars, Pandas, DuckDB, etc.), justifica tu eleccion.

### Arquitectura Medallion

Implementa un pipeline ETL con 3 capas:

#### Capa Bronze (Raw)
- Ingesta de los CSVs tal cual, sin transformaciones de negocio.
- Almacena en formato **Parquet**.
- Agrega metadata de ingesta: `_source_file`, `_ingested_at`.

#### Capa Silver (Clean)
- Limpieza y normalizacion de datos.
- Manejo de:
  - Valores nulos (decide una estrategia por columna)
  - Registros duplicados
  - Valores fuera de rango (precios negativos, scores invalidos, etc.)
  - Formatos inconsistentes (status en mayusculas/minusculas, etc.)
  - Validacion de integridad referencial (ej: orders con customer_id que no existe)
- Resultado en formato **Parquet**.

#### Capa Gold (Analytics)
Construye al menos **3 tablas analiticas**. Ejemplos sugeridos (puedes proponer otras):

1. **`gold_sales_by_state`**: Revenue, cantidad de pedidos y ticket promedio por estado.
2. **`gold_product_performance`**: Top productos por ventas, score promedio, tasa de devolucion.
3. **`gold_customer_segments`**: Segmentacion de clientes por frecuencia de compra y gasto total (RFM simplificado).
4. **`gold_monthly_kpis`**: KPIs mensuales (revenue, ordenes, cancelaciones, score promedio).

### Observabilidad y Calidad de Datos (IMPORTANTE)

Este es un punto clave de la evaluacion. En **cada etapa** del pipeline (Bronze, Silver, Gold) debes generar **tablas o reportes de calidad** que permitan responder:

- ¿Cuantos registros entraron vs cuantos salieron?
- ¿Cuantos nulos hay por columna?
- ¿Cuantos duplicados se detectaron y eliminaron?
- ¿Cuantos registros fueron descartados y por que?
- ¿Cuantos registros con integridad referencial rota?

**Formato esperado**: tablas de calidad (Parquet, CSV, o en una base DuckDB) que el evaluador pueda consultar. Ejemplo:

| check_name | table | column | records_checked | records_failed | pct_failed | stage | executed_at |
|---|---|---|---|---|---|---|---|
| null_check | customers | email | 5000 | 150 | 3.0 | bronze | 2024-01-15 10:30:00 |
| duplicate_check | orders | order_id | 15000 | 75 | 0.5 | bronze | 2024-01-15 10:30:00 |
| range_check | products | price | 1000 | 20 | 2.0 | silver | 2024-01-15 10:31:00 |

### Tests

- Incluye al menos **tests unitarios** para las funciones de transformacion mas criticas.
- Usa `pytest`.

### Jupyter Notebook (recomendado)

Se recomienda incluir un **Jupyter Notebook** que evidencie el paso a paso del pipeline:
- Mostrar los datos crudos y los problemas detectados.
- Mostrar las transformaciones aplicadas en cada capa.
- Visualizar las tablas de calidad y los registros descartados.
- Consultar las tablas Gold finales.

Esto permite al evaluador seguir tu razonamiento y ver los resultados de forma interactiva.

### Documentacion

- Un `README.md` en tu solucion explicando:
  - Decisiones de diseno (por que elegiste X libreria, Y estrategia de limpieza).
  - Diagrama o descripcion del flujo de datos.
  - Como ejecutar el proyecto.
  - Que mejorarias si tuvieras mas tiempo.

---

## Entregables

1. Repositorio Git (puede ser GitHub, GitLab o un .zip con historial de commits).
2. `docker-compose.yml` funcional.
3. Pipeline ejecutable con un solo comando.
4. Tablas de calidad/observabilidad consultables.
5. Tests.
6. Documentacion.
7. Jupyter Notebook mostrando el paso a paso (recomendado).

---

## Criterios de Evaluacion

| Criterio | Peso |
|---|---|
| Arquitectura y diseno del pipeline | 25% |
| Calidad del codigo (legibilidad, modularidad, buenas practicas) | 20% |
| Observabilidad y data quality | 15% |
| Transformaciones y logica de negocio | 25% |
| Tests | 10% |
| Documentacion y decisiones de diseño | 5% |

---

## Restricciones

- **No uses herramientas no-code** (Airbyte, Fivetran, etc.). El objetivo es ver tu codigo.
- **No uses IA generativa** para resolver la prueba completa. Puedes usarla como apoyo puntual, pero debes poder explicar cada decision en una entrevista tecnica posterior.

