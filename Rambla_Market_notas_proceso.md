# RAMBLA MARKET — Notas del proceso (paso a paso, clic a clic)

Proyecto de limpieza y análisis de ventas de Rambla Market. Trabajamos sobre `Rambla_Market_datos.xlsx` (archivo original, sin tocar) y volcamos todo el trabajo en `Rambla_Market_datos_trabajados.xlsx`.

## Índice

- [PASO 0 — Familiarízate con el archivo de crudo y aplica el esquema de color](#paso-0)
- [PASO 1 — Revisar calidad](#paso-1)
- [PASO 2 — Columnas calculadas](#paso-2)
- [PASO 3 — Crear tablas dinámicas nativas](#paso-3)
- [PASO 4 — Extraemos KPIs de las tablas dinámicas](#paso-4)
- [PASO 5 — Clasificación de margen (TD06)](#paso-5)
- [PASO 6 — Informe final](#paso-6)

---

## PASO 0 — Familiarízate con el archivo de crudo y aplica el esquema de color

1. Abre Rambla_Market_datos.xlsx.

Verás 2 pestañas:

- 00_maestro_productos
- 01_datos_crudos

Ambas tablas de datos están sin ningún formato.

2. Antes de limpiar nada, convierte los datos en tablas:
- En 00_maestro_productos: Ctrl + T
- En 01_datos_crudos: Ctrl + T

3. Colorea los encabezados según el tipo de columna, tanto en 00_maestro_productos como en 01_datos_crudos.

Selecciona cada grupo de encabezados y aplica un relleno desde Inicio > Color de relleno:

| Color | Tipo de columna | Columnas |
|---|---|---|
| 🔵 Azul claro | Categóricas (texto) | producto, categoria, comunidad_autonoma, canal |
| 🟢 Verde claro | Numéricas | cantidad, precio_unitario, coste_unitario, descuento_pct, coste_objetivo, precio_venta_recomendado, margen_objetivo_pct |
| ⚪ Gris claro | Identificadores y fechas | id_venta, id_producto, fecha |
| 🟣 Morado | Fecha/hora | Columnas de fecha/hora |

> **Importante:** En este paso no se limpia ni se modifica ningún dato. Solo se convierten los rangos en tablas y se aplica el formato de color a los encabezados.

---

## PASO 1 — Revisar calidad

4. Revisar la calidad de los datos columna por columna: nulos, duplicados, errores tipográficos, rangos y fechas.

00_maestro_productos parece tener los datos correctos, así que vamos a revisar 01_datos_crudos por categorías de columna.

### 1) IDs

- **id_venta:** Aplicamos Formato condicional → Resaltar reglas de celdas → Más reglas → Aplicar formato únicamente a los valores únicos o duplicados. De esta forma, comprobamos que los valores sean correctos y que no existan duplicados cuando el identificador debe ser único. Tras la comprobación, verificamos que todo es correcto.
  - En caso de encontrar valores duplicados, podríamos eliminarlos seleccionando la columna y accediendo a Datos → Quitar duplicados.
- **id_producto:** Aplicamos el mismo procedimiento para comprobar posibles valores duplicados. Todo correcto.

### 2) Fechas

**fecha:**

- Con el filtro activado en la columna `fecha`, ordena de A a Z: los valores de texto y los vacíos suelen agruparse al principio o al final.
- Antes de modificar la columna, comprobamos si Excel reconoce los valores como fechas reales. Podemos hacerlo utilizando temporalmente `=AÑO(celda)`.
  - Si devuelve correctamente el año (por ejemplo, 2025), Excel reconoce la celda como fecha y no es necesario convertirla.
  - Si devuelve `#¡VALOR!`, probablemente la fecha está almacenada como texto y debemos convertirla.
- Para convertir fechas almacenadas como texto, seleccionamos la columna fecha y vamos a Datos → Texto en columnas → Siguiente → Siguiente. En el tercer paso seleccionamos Fecha y el formato DMA (Día-Mes-Año) y pulsamos Finalizar.
- Una vez que Excel reconoce correctamente las fechas, añadimos dos columnas nuevas junto a fecha: **año** y **mes**.
  - En la columna año: `=AÑO(celda_fecha)`
  - En la columna mes: `=MES(celda_fecha)`
  - Ejemplo, para una fecha 08-05-2025 → año: 2025, mes: 5
- También añadimos **nombre mes**: `=TEXTO(celda columna fecha, formato mes "mmmm")`
- Y **trimestre**: `="T"&REDONDEAR.MAS(celda_mes/3;0)` — copiamos la fórmula y ponemos la celda del mes.

**Problemas detectados y su resolución:**

- Algunas fechas incluyen un sufijo extraño, por ejemplo, `26-11-24_TEXT`, lo que provoca un error de valor.
- Hay fechas en distintos formatos, como `2024` o `24`.
- **Acción:** normalizamos las fechas con formato de dos dígitos (24) al formato de cuatro dígitos (2024). Luego eliminamos la parte de texto con Reemplazar.
  - 🟡 Marcamos en amarillo para que haya trazabilidad.
  - 🟠 Marcamos las celdas vacías en naranja, sin poner texto porque nos podría complicar los gráficos.
- Errores de fechas y vacíos quedan excluidos del análisis.

### 3) Categóricas

Usamos el filtro de Excel para ver los valores únicos de cada columna.

- **producto:** Revisamos mediante el filtro para comprobar valores vacíos y posibles diferencias de escritura. Todo correcto.
- **categoria:** Revisamos mediante el filtro para comprobar valores vacíos y posibles diferencias de escritura. Todo correcto.
- **canal:** Revisamos mediante el filtro para comprobar valores vacíos y posibles diferencias de escritura. Todo correcto.
- **comunidad_autonoma:** Revisamos mediante el filtro y detectamos dos problemáticas:
  - Valores vacíos → Buscar y seleccionar → Reemplazar → los sustituimos por "Sin dato", para identificar explícitamente los registros sin información. 🟡 Marcamos en amarillo.
  - Diferencias de escritura para una misma categoría → filtramos por los valores a modificar y usamos Buscar y seleccionar → Reemplazar, estandarizando a "Cataluña" y "C. Valenciana".

### 4) Numéricas

**cantidad:**
- Unidades en negativo → se pasan a positivas. 🟡 Marcadas en amarillo.
- Número excesivo (480) → se comprueba si es error tipográfico (ej. querían poner 4 u 8 y se tecleó 480) → se establecen 4 unidades. 🟡 Marcadas en amarillo.
- Cantidad = 0 → no tiene sentido registrar una venta de cero unidades (ningún cliente compra cero productos generando una línea de ticket) → se rectifica al valor mínimo lógico de compra, que es 1. 🟡 Marcadas en amarillo.

**precio_unitario:**
- Rango de precios imposibles detectado: mínimo -175,33 y máximo 1069.
- Vamos a la tabla maestra para determinar el rango real de la empresa: 7,85–177,72.
- Seleccionamos los valores inferiores a 7,85: los números negativos pasan a positivos tras comprobar coherencia con los precios maestros.
- Para valores nulos o en cero: aplicamos `=INDICE` + `COINCIDIR` para extraer el precio de venta recomendado correspondiente a cada producto. 🟠 Marcados en naranja (para diferenciar de los negativos rectificados a positivos).
- Para valores extremos (≥180, filtro por número): también aplicamos `=INDICE`. 🟠 Marcados en naranja.
  - No aplicamos `=INDICE` a todos los precios porque mostraríamos una uniformidad artificial de precios cuando en la realidad hay variaciones (aunque mínimas) entre productos.
- Formato final: moneda.
- Columna adicional a la derecha de producto: `SI` condicional para gama (caro / accesible / económico), a partir del promedio de precio_unitario. Por encima de 58,49 → caro.

**coste_unitario:**
- Celdas vacías → se rellenan con `=INDICE` + `COINCIDIR`:
  1. Rango de coste_objetivo en la tabla maestra (valor final que quieres traer).
  2. id_producto de la fila actual en la tabla de trabajo (el que necesita el valor).
  3. Rango completo de códigos de producto en la tabla maestra (dónde buscar la coincidencia), + `;0` para coincidencia exacta.
- 🟡 Marcadas en amarillo.
- Formato final: moneda.

**descuento_pct:**
- Nota: los porcentajes se guardan como decimales (15% = 0,15) → les asignamos el %
- Valores vacíos → corresponden a 0% (siempre revisar que sea así) → filtramos por celdas vacías, rellenamos con 0%. 🟡 Marcadas en amarillo.
- Valor -10% → se corrige a positivo (10%).
- Valor 135% → se corrige a 13,5% (queda guardado como 0,135). 🟡 Marcadas en amarillo.

---

## PASO 2 — Columnas calculadas

| Columna | Fórmula |
|---|---|
| `ingresos` | `=[@cantidad]*[@precio_unitario]*(1-[@descuento_pct])` |
| `coste_total` | `=[@cantidad]*[@coste_unitario]` |
| `beneficio` | `=[@ingresos]-[@coste_total]` |
| `beneficio_pct` | `=SI.ERROR([@beneficio]/[@ingresos];0)` |

> Mi Excel no tiene configurado `@` al ser una versión antigua. Uso referencias directas:
>
> | Columna | Fórmula |
> |---|---|
> | `ingresos` | `=J2*K2*(1-M2)` |
> | `coste_total` | `=J2*L2` |
> | `beneficio` | `=N2-O2` |
> | `beneficio_pct` | `=SI.ERROR(P2/N2;0)` — para evitar errores de división; se pasa a formato porcentaje después. |

---

## PASO 3 — Crear tablas dinámicas nativas

| Tabla dinámica | Pregunta que responde | Análisis |
|---|---|---|
| TD01 | ¿Qué productos se venden más? | Top 5 productos por cantidad vendida |
| TD02 | ¿Qué productos generan más ingresos? ¿Los costes superan a los ingresos? ¿Hay beneficio? | Top 5 productos por ingresos, comparando costes y beneficios |
| TD03 | ¿Qué productos tienen mayor porcentaje de beneficio? | Rentabilidad por producto |
| TD04_gama | *¿Qué productos son económicos, accesibles y caros según los productos de negocio?* | Distribución de productos por gama de precio |

Todas filtradas por **año = 2025** y **comunidad_autonoma = Cataluña**.

### TD01_volumen_ventas — *¿Qué productos se venden más?*

| Zona | Campo(s) |
|---|---|
| Filtro de informe | año, comunidad_autonoma |
| Rótulos de fila | producto |
| Rótulos de columna | — |
| Valores | Suma de cantidad |

1. Colocamos `producto` en Rótulos de fila y `cantidad` en Valores (Suma de cantidad).
2. Más opciones de ordenación → Descendente → Suma de cantidad.
3. Filtros de valor → Diez mejores → cambiamos a 5 → aceptar.
4. Gráfico de barras, con los valores encima de cada barra.
5. Filtramos por año = 2025, comunidad_autonoma = Cataluña.

- Smartwatch como producto más vendido.

### TD02_ingresos_costes_beneficios — *¿Qué productos generan más ingresos? ¿Los costes superan a los ingresos? ¿Hay beneficio?*

| Zona | Campo(s) |
|---|---|
| Filtro de informe | año, comunidad_autonoma |
| Rótulos de fila | producto |
| Rótulos de columna | (Σ Valores — al meter 3 medidas, Excel las despliega en columnas) |
| Valores | Suma de ingresos, Suma de costes, Suma de beneficio |

1. Colocamos `producto` en Rótulos de fila.
2. Añadimos `ingresos`, `costes` y `beneficio` en Valores, todos como Suma.
3. Más opciones de ordenación → Descendente → Suma de ingresos.
4. Filtros de valor → Diez mejores → cambiamos a 5 por Suma de ingresos → aceptar.
5. Reducimos a 2 decimales.
6. Gráfico con los valores encima de cada barra.
7. Filtramos por año = 2025, comunidad_autonoma = Cataluña.

- Tablet 10 como producto que más ingresos, costes y beneficios presenta en el TOP 5 ( no del total de productos). 

### TD03_beneficios_pct — *¿Qué productos tienen mayor porcentaje de beneficio?*

| Zona | Campo(s) |
|---|---|
| Filtro de informe | año, comunidad_autonoma |
| Rótulos de fila | producto |
| Rótulos de columna | — |
| Valores | Promedio de beneficio_pct |

> Para márgenes o porcentajes se utiliza el Promedio, no la Suma.

1. Colocamos `producto` en Rótulos de fila.
2. Añadimos `beneficio_pct` en Valores → configuramos campo de valor a Promedio → formato porcentaje.
3. Más opciones de ordenación → Descendente → Promedio de beneficio_pct.
4. Filtros de valor → Diez mejores → cambiamos a 5 por Promedio de beneficio → aceptar.
5. Gráfico con los valores encima de cada barra.
6. Filtramos por año = 2025, comunidad_autonoma = Cataluña.

- Perfume eau de parfum 90ml representa el producto que ofrece más beneficio y en tercer lugar aparece la Tablet 10.

### TD04_gama — *¿Qué productos son económicos, accesibles y caros según los productos de negocio?*

| Zona | Campo(s) |
|---|---|
| Filtro de informe | año, comunidad_autonoma |
| Rótulos de fila | gama → producto (anidado) |
| Rótulos de columna | — |
| Valores | Promedio de precio_unitario |

1. Colocamos `gama` en Rótulos de fila, y `producto` debajo (anidado).
2. Añadimos `precio_unitario` en Valores → Promedio.
3. Filtramos por año = 2025, comunidad_autonoma = Cataluña.

- Smartwatch: el que más volumen representa → caro
- Tablet 10: el que más ingresos, costes representa y ocupa el tercer puesto en beneficio → caro 
- Perfume eau de parfum 90ml: el que más beneficio representa → accesible

---

## PASO 4 — Extraemos KPIs de las tablas dinámicas

**KPIs de negocio — Cataluña, 2025**

| KPI | Valor |
|---|---|
| Unidades Totales Vendidas | 366 |
| Ingresos Totales | 37.683,96 |
| Costes Totales | 23.932,97 |
| Beneficio Total | 13.750,99 |

---

## PASO 5 — Extraemos los productos de negocio

| Categoría | Producto |
|---|---|
| Volumen | Smartwatch |
| Ingresos | Tablet 10" |
| Costes | Tablet 10" |
| Beneficio | Perfume eau de parfum 90ml |


### TD05_analisis_productos

Se analizan la cantidad vendida, los ingresos y el porcentaje medio de beneficio de los tres productos destacados en el TOP:

- Accesible — Perfume Eau de Parfum 90 ml: presenta un alto porcentaje de beneficio, pero registra el menor volumen de ventas, con 19 unidades. → Oportunidad de aumentar su visibilidad mediante acciones de publicidad y promoción.
- Caro — Smartwatch: es el producto con mayor volumen de ventas, situándose en el TOP 1 en unidades vendidas. Mirar rentabilidad por categoría para enfocar la estrategia. 
- Caro — Tablet 10": presenta un volumen de ventas intermedio en comparación con otros productos (61 unidades), pero genera elevados ingresos y se encuentra en el TOP 3 en beneficios. → Potenciar su promoción entre clientes con mayor poder adquisitivo.


### TD06_beneficios_smartwatch — *¿Qué porcentaje de beneficio tiene el producto smartwatch en comparación con su categoría?*

| Zona | Campo(s) |
|---|---|
| Filtro de informe | año, comunidad_autonoma |
| Rótulos de fila | producto |
| Rótulos de columna | — |
| Valores | Promedio de beneficio_pct |

> Para márgenes o porcentajes se utiliza el Promedio, no la Suma.

1. Colocamos `producto` en Rótulos de fila.
2. Añadimos `beneficio_pct` en Valores → configuramos campo de valor a Promedio → formato porcentaje.
3. Más opciones de ordenación → Descendente → Promedio de beneficio_pct.
4. Filtros de valor → Diez mejores → cambiamos a 5 por Promedio de beneficio → aceptar.
5. Gráfico con los valores encima de cada barra.
6. Filtramos por año = 2025, comunidad_autonoma = Cataluña.


### Interpretación del margen

Como referencia orientativa, el margen puede clasificarse en los siguientes rangos:

| Margen        | Interpretación    | Acción recomendada                                                                                                              |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **< 20%**     | 🔴 **Bajo**       | Revisar costes, precio de venta o promociones(descuentos).                                                        |
| **20% – 30%** | 🟠 **Bajo-medio** | Analizar la estructura de costes y evaluar posibles ajustes de precio o promociones.                                            |
| **30% – 40%** | 🟢 **Adecuado**   | Margen saludable como referencia, aunque debe compararse con productos similares.                                               |
| **40% – 50%** | 🟢 **Bueno**      | Producto con una rentabilidad atractiva; analizar qué factores contribuyen al buen margen.                                      |
| **> 50%**     | 🟢 **Alto**       | Margen elevado; evaluar si existe capacidad para aumentar volumen mediante precio o promociones sin deteriorar la rentabilidad. |

> **Nota:** estos rangos son orientativos y no deben interpretarse como valores universales. La valoración del margen depende del sector, estructura de costes y estrategia comercial.

- Smartwatch: margen del 33%, adecuado a otros productos de su misma categoría ( el segundo que más beneficios tiene en la categoría electrónica). Mantener el producto como referencia de volumen, evitando incrementos de precio que puedan afectar significativamente a las ventas.

### Informe final

Última hoja del libro (`informe_final`), donde se consolidan los tres productos de negocio detectados en los pasos anteriores junto con su estrategia recomendada y las tablas dinámicas resumen que los sustentan.

**INFORME DE PRODUCTOS DE NEGOCIO: CATALUÑA Y 2025**

| Producto de Volumen | Estrategia | Producto de Ingresos | Estrategia | Producto de Beneficios | Estrategia |
|---|---|---|---|---|---|
| Smartwatch | Mantener el producto como referencia de volumen, evitando incrementos de precio | Tablet 10" | Potenciar su promoción entre clientes con mayor poder adquisitivo | Perfume eau de parfum 90ml | Oportunidad de aumentar su visibilidad mediante acciones de publicidad y promoción |

Debajo de cada producto se enlaza (copiando/pegando como valores) el Top 5 de la tabla dinámica correspondiente, para dar trazabilidad al informe:

**Top 5 por volumen (Suma de cantidad)**

| Producto | Cantidad |
|---|---|
| Smartwatch | 80 |
| Chaqueta acolchada | 76 |
| Robot de cocina | 71 |
| Aceite de oliva virgen extra 1L | 70 |
| Crema facial hidratante | 69 |
| **Total general** | **366** |

**Top 5 por ingresos (Suma de ingresos)**

| Producto | Ingresos |
|---|---|
| Tablet 10" | 10.466,43 |
| Altavoz Bluetooth | 8.258,21 |
| Auriculares inalámbricos | 8.029,31 |
| Smartwatch | 5.535,67 |
| Lámpara LED de pie | 5.394,34 |
| **Total general** | **37.683,96** |

**Top 5 por beneficio (Promedio de beneficio_pct)**

| Producto | Beneficio % |
|---|---|
| Perfume eau de parfum 90ml | 52,5% |
| Bolso bandolera | 46,5% |
| Tablet 10" | 44,0% |
| Café en grano 1kg | 43,8% |
| Aceite de oliva virgen extra 1L | 43,6% |
| **Total general** | **45,6%** |

**Lectura del informe:**

- El informe reúne en una sola hoja los tres "productos de negocio" —el más vendido, el que más ingresa y el más rentable— junto con su estrategia, para que cualquier persona no técnica pueda leer la conclusión sin recorrer las tablas dinámicas.
