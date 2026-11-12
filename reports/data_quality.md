# Informe de calidad de datos: Instacart

**Aprendiz:** Maicol Esneider Posada Callejas · Ficha 3223873 · Tecnólogo en Análisis y Desarrollo de Software (SENA)
**Instructor:** Carlos Navia

Este informe resume la exploración inicial de los cinco archivos de Instacart hecha en [`notebooks/data_quality.ipynb`](../notebooks/data_quality.ipynb) (evidencia GA1-220501094-01-AA1-EV04). En esa etapa no se corrigió ningún dato: solo se identificaron sus características y problemas.

## 1. Estructura de los archivos

Los cinco archivos son CSV separados por punto y coma (`;`), por eso se cargan con `pd.read_csv(..., sep=";")`.

| Archivo | Una fila es | Filas | Columnas | Clave |
|---|---|---|---|---|
| `instacart_orders.csv` | Un pedido | 478.967 | 6 | `order_id` |
| `products.csv` | Un producto del catálogo | 49.694 | 4 | `product_id` |
| `aisles.csv` | Un pasillo | 134 | 2 | `aisle_id` |
| `departments.csv` | Un departamento | 21 | 2 | `department_id` |
| `order_products.csv` | Un producto dentro de un pedido | 4.545.007 | 4 | `order_id` + `product_id` |

Relación entre las tablas: `orders` se une con `order_products` por `order_id`; `order_products` con `products` por `product_id`; y `products` con `aisles` y `departments` por `aisle_id` y `department_id`.

## 2. Problemas de calidad encontrados

| Tabla | Problema | Evidencia | Impacto |
|---|---|---|---|
| `orders` | Filas duplicadas | 15 filas idénticas, todas del miércoles (`order_dow = 3`) a las 2 a. m. | Se cuentan 15 pedidos de más (478.967 filas, pero 478.952 pedidos distintos) |
| `orders` | Valores nulos en `days_since_prior_order` | 28.819 nulos, que coinciden con los pedidos con `order_number = 1` | No es un error: el primer pedido no tiene pedido anterior. Rellenarlos con 0 cambiaría el significado |
| `orders` | Entero leído como decimal | `days_since_prior_order` aparece como `float64` por los nulos | Puede confundir; se resuelve con un tipo entero que admita nulos |
| `orders` | Posible tope en los días | El máximo es 30 y 51.338 pedidos tienen exactamente 30 | 30 puede significar "30 días o más"; el promedio real sería mayor |
| `products` | Productos sin nombre | 1.258 nulos en `product_name`, todos en el pasillo 100 y el departamento 21 | No se pueden identificar en un reporte |
| `aisles` y `departments` | Valor centinela "missing" | El pasillo 100 y el departamento 21 se llaman "missing" | Pandas no lo detecta como nulo, pero significa "desconocido" |
| `order_products` | Nulos en `add_to_cart_order` | 836 nulos, concentrados en 70 pedidos; la posición máxima registrada es 64 | Parece que no se guardó la posición después del puesto 64 en pedidos muy grandes |
| `order_products` | Entero leído como decimal | `add_to_cart_order` aparece como `float64` por los nulos | Igual que en `orders` |

## 3. Acciones de limpieza propuestas

1. Eliminar las 15 filas duplicadas de `orders` y comprobar que `order_id` quede único.
2. Conservar los nulos de `days_since_prior_order`, porque representan primeros pedidos.
3. Rellenar los nombres de producto faltantes con un valor explícito (por ejemplo, `Unknown`).
4. Convertir las columnas enteras a tipos enteros que admitan nulos y reducir el tamaño de los tipos numéricos.
5. Estandarizar el texto de `product_name` (espacios sobrantes y mayúsculas).
6. Investigar los nulos de `add_to_cart_order` antes de decidir qué hacer con ellos.

Estas acciones se aplican en la Lección 2 de [`notebooks/pandas_labs.ipynb`](../notebooks/pandas_labs.ipynb).

## 4. Conclusión

Los datos de Instacart están completos en su mayoría y las cinco tablas se pueden relacionar por sus claves. Los problemas encontrados son pocos y concretos (duplicados, nulos con causa conocida, un valor centinela y tipos de dato), así que se pueden corregir antes del análisis sin perder información importante.
