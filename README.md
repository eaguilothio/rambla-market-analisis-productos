# RAMBLA MARKET — Análisis de ventas

Proyecto de limpieza, transformación y análisis de datos de ventas de Rambla Market, resuelto íntegramente en Excel.

## Objetivo del análisis

Rambla Market busca determinar qué productos fueron clave en 2025 dentro de Cataluña — según volumen de ventas, ingresos generados y rentabilidad — con el objetivo de priorizar decisiones comerciales y estratégicas basadas en datos.

## Preguntas de negocio

Este proyecto nace para responder a las siguientes preguntas:

1. ¿Cuáles son los productos líderes en volumen de ventas?
2. ¿Qué referencias generan el mayor impacto en ingresos?
3. ¿Cuáles son los productos más rentables en porcentaje?
4. ¿Cómo se comporta el catálogo según las gamas de precio? ¿Cómo se distribuyen los productos económicos, accesibles y caros?
5. ¿Cómo rinde un producto frente a otros de su misma categoría? En concreto, ¿cuál es el margen del Smartwatch frente al resto de artículos de su categoría?
6. ¿Cuáles son los indicadores globales de salud del negocio — unidades, ingresos, costes y beneficios totales?

## Alcance del análisis

Todo el análisis final está filtrado por **año = 2025** y **comunidad_autónoma = Cataluña**.

## Archivos del proyecto

| Archivo | Descripción |
|---|---|
| `Rambla_Market_datos.xlsx` | Archivo original, sin tocar. Sirve como respaldo/fuente de verdad. |
| `Rambla_Market_datos_trabajados.xlsx` | Libro de trabajo con todo el proceso: limpieza, columnas calculadas, tablas dinámicas, KPIs e informe final. |
| `Rambla_Market_notas_proceso.md` | Bitácora paso a paso (clic a clic) de todo el proceso, con las decisiones tomadas y su justificación. |

## Estructura del libro trabajado

| Hoja | Contenido |
|---|---|
| `00_maestro_productos` | Catálogo maestro de productos (precio de venta recomendado, coste objetivo, margen objetivo). Fuente de referencia para corregir datos crudos. |
| `01_datos_crudos` | Datos de ventas limpios, con columnas calculadas (`año`, `mes`, `nombre mes`, `trimestre`, `ingresos`, `coste_total`, `beneficio`, `beneficio_pct`, `gama`). |
| `TD01_volumen_ventas` | Top 5 productos por unidades vendidas. |
| `TD02_ingresos_costes_beneficios` | Top 5 productos por ingresos, con sus costes y beneficios asociados. |
| `TD03_beneficios_pct` | Top 5 productos por margen de beneficio (%). |
| `TD04_gama` | Distribución de productos por gama de precio (económico / accesible / caro). |
| `TD05_analisis_productos` | Interpretación cruzada de los 3 productos de negocio (cantidad, ingresos, % beneficio). |
| `TD06_beneficios_smartwatch` | Margen del Smartwatch comparado con el resto de su categoría. |
| `KPIS` | Indicadores globales del negocio (unidades, ingresos, costes, beneficio). |
| `TOP_productos` | Resumen de los productos destacados por categoría (volumen, ingresos, beneficio). |
| `informe_final` | Resumen ejecutivo: producto de negocio por volumen/ingresos/beneficio + estrategia recomendada + tablas resumen. |

## Proceso (resumen)

1. **Familiarización y formato** — Se convirtieron los rangos en tablas nativas (Ctrl+T) y se codificaron los encabezados por color según tipo de columna (categóricas, numéricas, IDs/fechas).
2. **Revisión de calidad** — Se revisó cada bloque de columnas (IDs, fechas, categóricas, numéricas) buscando nulos, duplicados, errores tipográficos y rangos imposibles, marcando cada corrección por color (🟡 corregido, 🟠 imputado por índice/coincidir, ⚪ excluido del análisis).
3. **Columnas calculadas** — Se añadieron `ingresos`, `coste_total`, `beneficio` y `beneficio_pct`.
4. **Tablas dinámicas nativas** — Se crearon tablas que permiten comparar productos por distintos criterios (volumen, ingresos, rentabilidad, gama de precio).
5. **KPIs de negocio** — Se extrajeron las cifras globales (unidades, ingresos, costes, beneficio) de Cataluña 2025 a partir de las tablas dinámicas.
6. **Clasificación de margen** — Se analizó el margen del Smartwatch frente a su categoría para poder focalizar la estrategia.
7. **Informe final** — Se consolidaron los tres "productos de negocio" (volumen, ingresos, beneficio) con su estrategia recomendada en una sola hoja de lectura ejecutiva.

## Hallazgos por pregunta de negocio

| Pregunta | Respuesta | Evidencia |
|---|---|---|
| ¿Productos líderes en volumen? | Smartwatch (80 uds.), seguido de Chaqueta acolchada y Robot de cocina. | `TD01_volumen_ventas` |
| ¿Mayor impacto en ingresos? | Tablet 10" (10.466,43 €), por delante de Altavoz Bluetooth y Auriculares inalámbricos. | `TD02_ingresos_costes_beneficios` |
| ¿Productos más rentables (%)? | Perfume eau de parfum 90ml (52,5%), seguido de Bolso bandolera y Tablet 10". | `TD03_beneficios_pct` |
| ¿Cómo se distribuye el catálogo por gama? | Smartwatch y Tablet 10" caen en la gama cara; el Perfume, en la accesible. | `TD04_gama` |
| ¿Margen del Smartwatch frente a su categoría? | 33% — margen adecuado, segundo mejor de la categoría electrónica. | `TD06_beneficios_smartwatch` |
| ¿Indicadores globales del negocio? | 366 uds. · 37.683,96 € en ingresos · 23.932,97 € en costes · 13.750,99 € de beneficio. | `KPIS` |


## Conclusiones y estrategia

| Categoría | Producto | Estrategia |
|---|---|---|
| Volumen | Smartwatch | Mantener como referencia de volumen, evitando subidas de precio que afecten a las ventas. |
| Ingresos | Tablet 10" | Potenciar su promoción entre clientes con mayor poder adquisitivo. |
| Beneficio | Perfume eau de parfum 90ml | Aumentar su visibilidad mediante publicidad y promoción. |