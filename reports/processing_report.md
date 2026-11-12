# Informe de procesamiento de datos: Instacart

**Evidencia:** GA1-220501094-01-AA1-EV06 – Limpieza, análisis y estructuración de datos con Pandas (Instacart)
**Aprendiz:** Maicol Esneider Posada Callejas · Ficha 3223873 · Tecnólogo en Análisis y Desarrollo de Software (SENA)
**Instructor:** Carlos Navia

Este informe resume los resultados de [`notebooks/pandas_labs.ipynb`](../notebooks/pandas_labs.ipynb), que desarrolla las lecciones 2, 3 y 4 del material *Preparar y estructurar datos con Python*: los ejemplos de cada lección, los laboratorios guiados 2, 3 y 4 y los 21 ejercicios de los retos.

**Pregunta orientadora:** ¿Cómo preparar y estructurar los datos de Instacart para obtener información confiable sobre los hábitos de compra?

## 1. Lección 2: limpieza y estandarización

Laboratorio guiado 2: pipeline de limpieza con informe de cambios, encadenado con `pipe` y validado con `assert`.

| Problema | Evidencia | Decisión | Resultado |
|---|---|---|---|
| Pedidos duplicados en `orders` | 15 filas idénticas, todas del miércoles a las 2 a. m. | Eliminar | 478.952 pedidos y `order_id` único |
| Tipos de dato demasiado grandes y enteros leídos como decimales | `float64` en columnas enteras | Corregir con `astype` y tipos `Int16` que admiten nulos | `orders` de 23,0 a 7,2 MB y `order_prods` de 145,4 a 54,5 MB |
| Nulos en `days_since_prior_order` | 28.817 después de quitar duplicados, todos primeros pedidos | Conservar | La media de espera no se distorsiona |
| Productos sin nombre | 1.258, todos en pasillo y departamento `missing` | Rellenar con `Unknown` | 0 nulos en `product_name` |
| Texto sin estandarizar | 403 nombres con espacios dobles y 16 con espacios invisibles | Corregir con métodos `.str` | 0 y 0; 6.321 nombres cambiaron |
| Duplicados implícitos de nombre | 141 nombres repetidos con `product_id` distinto | Conservar y documentar | Se agrupa por `product_id` |
| Nulos en `add_to_cart_order` | 836 en 70 pedidos de 65 productos o más | Conservar | Posiciones después del puesto 64 sin registrar |

El informe de cambios del pipeline quedó en `data/processed/cleaning_report.csv`. Después de la limpieza, las tablas pasan todas las reglas de validación.

## 2. Lección 3: filtrado, ordenamiento y agrupación

Laboratorio guiado 3: perfil de hábitos de compra por segmento de cliente (ocasional, regular y frecuente) y franja horaria.

| Segmento | Clientes | Pedidos | Espera mediana (días) |
|---|---|---|---|
| Ocasional (1 pedido) | 55.357 | 55.357 | 16 |
| Regular (2 a 4) | 71.553 | 191.649 | 11 |
| Frecuente (5 o más) | 30.527 | 231.946 | 6 |

- **Cuándo se compra:** la hora pico es las 10 a. m. (40.578 pedidos), el domingo y el lunes son los días más activos y la tarde es la franja con más pedidos en todos los segmentos. Los domingos el pico se corre a las 3 p. m.
- **Quién compra:** el 19,4 % de los clientes (los frecuentes, con 5 pedidos o más) hace casi la mitad de los pedidos y vuelve cada 6 días (mediana); 6.327 clientes tienen 10 pedidos o más.
- **Con qué frecuencia:** 7 días es una espera muy común (9,3 % de los pedidos), lo que muestra un hábito de compra semanal. El fin de semana la mediana de espera sube a 8 días.
- **Qué tan grande es el pedido:** 49.420 pedidos tienen 20 productos o más; el más grande tiene 127.
- **Recompra:** los primeros productos del carrito son los que más se repiten (tasa de 0,679 en la posición 1).

Para mercadeo, la mejor ventana es la mañana para los clientes frecuentes y la tarde del domingo y el lunes para el resto.

## 3. Lección 4: combinación y estructuración

Laboratorio guiado 4: tabla de análisis por producto y producto con mayor tasa de recompra de cada departamento.

**Validación de las combinaciones:**

- Cada `merge` de la tabla maestra usó `how='left'` y `validate='m:1'`, y el `assert` confirmó que se mantuvieron las 4.545.007 filas de `order_prods`: no se duplicaron ni se perdieron artículos.
- La prueba con un catálogo con un producto repetido mostró que `validate` detiene la unión en lugar de duplicar filas en silencio.
- La auditoría con `indicator=True` encontró 28.906 pedidos sin productos (`left_only`) y 0 productos sin pedido (`right_only`).
- En el laboratorio 4, el `merge` con `validate='1:1'` confirmó que la ficha tiene un solo registro por producto, y las dos formas de obtener los líderes por departamento dieron el mismo resultado.

**Archivos guardados en `data/processed/`:**

| Archivo | Contenido |
|---|---|
| `cleaning_report.csv` | Informe de cambios del pipeline de limpieza (Lección 2) |
| `instacart_master.parquet` | Tabla maestra: un artículo por fila con producto, pasillo, departamento y datos del pedido (4.545.007 filas, 14 columnas) |
| `department_summary.csv` | Artículos y tasa de recompra por departamento |
| `product_profile.parquet` | Ficha de 45.573 productos: veces pedido, clientes, tasa de recompra y posición media |
| `department_leaders.csv` | Producto con mayor tasa de recompra de cada departamento (18 departamentos) |

La tabla maestra pesa más de 100 MB, el límite de GitHub, así que no se sube al repositorio: se genera al ejecutar este cuaderno.

Los cinco productos líderes con mayor tasa de recompra (entre los pedidos al menos 1.000 veces):

| Departamento | Producto | Veces pedido | Tasa de recompra |
|---|---|---|---|
| dairy eggs | Whole Organic Omega 3 Milk | 1.286 | 0,863 |
| produce | Banana | 66.050 | 0,844 |
| snacks | Extra Fancy Unsalted Mixed Nuts | 1.436 | 0,822 |
| beverages | Zero Calorie Cola | 1.256 | 0,818 |
| bakery | Organic Honey Sweet Whole Wheat Bread | 1.607 | 0,799 |

## 4. Conclusiones

**Respuesta a la pregunta orientadora:** los datos de Instacart se preparan en tres etapas. Primero se **limpian** con un pipeline repetible: se eliminan los 15 pedidos duplicados, se corrigen los tipos, se decide qué hacer con cada nulo según su causa y se estandariza el texto, verificando todo con `assert`. Después se **analizan** con filtros, ordenamientos y agrupaciones para responder preguntas de negocio. Por último se **estructuran** en una tabla maestra validada que une las cinco tablas sin perder ni duplicar filas. Así, los resultados sobre los hábitos de compra son confiables.

**Hallazgos principales:**

1. Los clientes de Instacart compran sobre todo de día: la hora pico es las 10 a. m., la tarde es la franja más activa y el domingo y el lunes son los días con más pedidos. El alcohol es la excepción: se compra más los viernes.
2. Un grupo pequeño de clientes frecuentes (19,4 %) hace casi la mitad de los pedidos y vuelve cada 6 días. Muchos clientes tienen un hábito de compra semanal.
3. La recompra es alta en productos básicos como leche, agua y frutas, y baja en productos que duran, como las especias. Los productos que se repiten se agregan primero al carrito.
4. El 6 % de los pedidos no tiene productos registrados. Por eso es necesario auditar cada unión antes de sacar conclusiones.
