# Instacart: análisis de hábitos de compra con Python y pandas

Proyecto de ciencia de datos del programa Tecnólogo en Análisis y Desarrollo de Software (SENA), ficha 3223873.

**Autor:** Maicol Esneider Posada Callejas · **Instructor:** Carlos Navia

## Descripción

Exploración de los datos de Instacart, una plataforma de compras de supermercado en línea, para conocer los hábitos de compra de sus clientes. El proyecto trabaja con cinco archivos CSV suministrados por el instructor: pedidos, productos, pasillos, departamentos y los productos de cada pedido.

## Estructura del repositorio

```
instacart-analisis/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/                 # Los cinco CSV originales (no se suben a GitHub)
│   └── processed/           # Archivos generados por los notebooks
├── notebooks/
│   └── data_quality.ipynb   # Exploración y calidad inicial de los datos
└── reports/
    └── data_quality.md      # Informe de calidad de los datos
```

## Datos

Los CSV no se publican en el repositorio por su tamaño (`order_products.csv` pesa unos 90 MB). Se descargan de la carpeta de Google Drive del curso y se guardan en `data/raw/`:

https://drive.google.com/drive/folders/19vYGYdE0hI1DzvpBcCZskzJ0u2TZjwas

| Archivo | Contenido |
|---|---|
| `instacart_orders.csv` | Un pedido por fila: cliente, número de pedido, día, hora y días desde el pedido anterior |
| `order_products.csv` | Un producto dentro de un pedido: posición en el carrito y si es recompra |
| `products.csv` | Catálogo de productos con su pasillo y departamento |
| `aisles.csv` | Pasillos |
| `departments.csv` | Departamentos |

Los archivos usan punto y coma (`;`) como separador.

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
3. Instalar las dependencias:
   ```
   python -m pip install -r requirements.txt
   ```
4. Copiar los cinco CSV en `data/raw/`.
5. Abrir JupyterLab desde la raíz del proyecto y ejecutar el notebook:
   ```
   jupyter lab
   ```
   Abrir `notebooks/data_quality.ipynb` y usar *Run → Run All Cells*.

## Resultados

El resumen de la calidad de los datos está en [`reports/data_quality.md`](reports/data_quality.md).
