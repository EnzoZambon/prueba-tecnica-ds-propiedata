# ⚡ Guía de Ejecución Rápida

Este repositorio contiene el pipeline de procesamiento de datos y entrenamiento de modelos para la predicción de precios de alquileres.

## 🛠️ Configuración del Entorno

Para replicar el entorno de desarrollo y evitar conflictos de librerías:

1.  **Crear y activar entorno virtual:**
    ```bash
    python -m venv venv
    # En Windows:
    .\venv\Scripts\activate
    # En Mac/Linux:
    source venv/bin/activate
    ```

2.  **Instalar dependencias:**
    ```bash
    pip install -r requirements.txt

## 🚀 Cómo ejecutar el proyecto

1. **Levantar el servidor de MLflow:**
    Para visualizar el registro de experimentos y la comparación de modelos ($R^2$, MAE, etc.), ejecutá en tu terminal: mlflow ui y luego abrí `http://localhost:5000` en tu navegador.

2.  **Correr el entrenamiento:**

    **Paso 1:** Abrí la notebook `01_limpieza.ipynb` y ejecutá todas las celdas (**Run All**) para la limpieza de datos.
    **Paso 2:** Abrí la notebook `01_limpieza.ipynb` y ejecutá todas las celdas (**Run All**) para el Entrenamiento de modelos (Linear Regression, Random Forest, XGBoost, LightGBM) y el Registro automático de métricas en MLflow.