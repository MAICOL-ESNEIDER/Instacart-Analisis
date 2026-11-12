# Instacart: análisis de hábitos de compra con Python y pandas

Proyecto de ciencia de datos del programa Tecnólogo en Análisis y Desarrollo de Software (SENA), ficha 3223873.

**Autor:** Maicol Esneider Posada Callejas · **Instructor:** Carlos Navia

## Descripción

Exploración, limpieza, análisis y estructuración de los datos de Instacart, una plataforma de compras de supermercado en línea, para conocer los hábitos de compra de sus clientes. El proyecto trabaja con cinco archivos CSV suministrados por el instructor: pedidos, productos, pasillos, departamentos y los productos de cada pedido.

**Pregunta orientadora:** ¿Cómo preparar y estructurar los datos de Instacart para obtener información confiable sobre los hábitos de compra?

| Etapa | Notebook | Informe |
|---|---|---|
| Exploración y calidad inicial de los datos | `notebooks/data_quality.ipynb` | `reports/data_quality.md` |
| Limpieza, análisis y combinación (lecciones 2, 3 y 4 de *Preparar y estructurar datos con Python*) | `notebooks/pandas_labs.ipynb` | `reports/processing_report.md` |

## Estructura del repositorio

```
instacart-analisis/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/                          # Los cinco CSV originales (no se suben a GitHub)
│   └── processed/                    # Archivos generados por pandas_labs.ipynb
│       ├── cleaning_report.csv
│       ├── department_summary.csv
│       ├── department_leaders.csv
│       ├── product_profile.parquet
│       └── instacart_master.parquet  # Se genera al ejecutar (no se sube: pesa más de 100 MB)
├── notebooks/
│   ├── data_quality.ipynb
│   └── pandas_labs.ipynb
└── reports/
    ├── data_quality.md
    └── processing_report.md
```

## Datos

Los CSV originales no se publican en el repositorio por su tamaño (`order_products.csv` pesa unos 90 MB). Se descargan de la carpeta de Google Drive del curso y se guardan en `data/raw/`:

https://drive.google.com/drive/folders/19vYGYdE0hI1DzvpBcCZskzJ0u2TZjwas

| Archivo | Contenido |
|---|---|
| `instacart_orders.csv` | Un pedido por fila: cliente, número de pedido, día, hora y días desde el pedido anterior |
| `order_products.csv` | Un producto dentro de un pedido: posición en el carrito y si es recompra |
| `products.csv` | Catálogo de productos con su pasillo y departamento |
| `aisles.csv` | Pasillos |
| `departments.csv` | Departamentos |

Los archivos usan punto y coma (`;`) como separador.

### Archivos procesados

| Archivo | Contenido |
|---|---|
| `cleaning_report.csv` | Informe de cambios del pipeline de limpieza (filas, nulos y memoria en cada paso) |
| `department_summary.csv` | Artículos y tasa de recompra por departamento |
| `department_leaders.csv` | Producto con mayor tasa de recompra de cada departamento |
| `product_profile.parquet` | Ficha de cada producto: veces pedido, clientes, tasa de recompra y posición media en el carrito |
| `instacart_master.parquet` | Tabla maestra con las cinco tablas unidas (4.545.007 filas). No está en el repositorio porque supera el límite de 100 MB de GitHub; se crea al ejecutar `pandas_labs.ipynb` |

## Instrucciones de ejecución

Requisitos: Python 3.11 o superior y Git.

1. Clonar el repositorio:
   ```
   git clone https://github.com/MAICOL-ESNEIDER/Instacart-Analisis.git
   cd Instacart-Analisis
   ```
2. Crear y activar el entorno virtual:
   ```
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1        # Windows PowerShell
   source .venv/bin/activate          # macOS y Linux
   ```
3. Instalar las dependencias (pandas, pyarrow para los archivos Parquet y JupyterLab):
   ```
   python -m pip install -r requirements.txt
   ```
4. Copiar los cinco CSV en `data/raw/`.
5. Abrir JupyterLab desde la raíz del proyecto:
   ```
   jupyter lab
   ```
6. Ejecutar los notebooks de la carpeta `notebooks/` con *Run → Run All Cells*:
   - `data_quality.ipynb`: exploración y calidad inicial.
   - `pandas_labs.ipynb`: lecciones 2, 3 y 4. Las lecciones se ejecutan en orden, porque cada una usa las variables de la anterior. Al terminar, los archivos procesados quedan en `data/processed/`.

Si aparece un `ImportError` relacionado con `pyarrow`, se instala con `python -m pip install pyarrow` y se reinicia el kernel.

## Resultados

- Calidad inicial de los datos: [`reports/data_quality.md`](reports/data_quality.md)
- Limpieza, análisis y combinación: [`reports/processing_report.md`](reports/processing_report.md)
