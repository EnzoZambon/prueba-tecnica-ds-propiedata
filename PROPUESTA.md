# Parte B — Propuesta escrita 

## 4.1 Matching de Inmuebles Únicos
Para mitigar el sesgo por registros duplicados, se propone una lógica de de-duplicación avanzada:

*   **Proximidad Geoespacial:** Identificación de registros a < 50 metros con variación de superficie < 5%.
*   **NLP & Similitud:** Uso de *Similitud de Coseno* sobre descripciones para detectar textos idénticos.
*   **Ventanas de Precio:** Cruce de barrio, ambientes y precio con tolerancia del +/- 10%.
*   **Embeddings:** Si hay imágenes, comparar vectores visuales para detectar fotos duplicadas.
*   **Predictor Binario:** Entrenamiento de un clasificador (XGBoost) dedicado exclusivamente a decidir si dos registros representan la misma propiedad.

## 4.2 Arquitectura de Tablas (Medallion Architecture)
Implementación de un flujo de datos organizado por niveles de refinamiento:

El pipeline ideal sigue la arquitectura de Medallion (Bronze/Silver/Gold):

    1. Capa Bronze (Landing/Raw): JSONs o Parquets crudos del scraping. Sin transformaciones. Histórico completo.

    2. Capa Silver (Cleansing/Standardization): Datos tipados, manejo de nulos, eliminación de duplicados (matching 4.1). Aquí vive la "Single Source of Truth".

    3. Capa Gold (Feature Store/Training): Tablas listas para el modelo. One-Hot Encoding aplicado, normalización y agregaciones espaciales (promedio precio/m2 del barrio).

    4. Capa UI/Serving: Una base de datos liviana (PostgreSQL o Redis) que solo contenga los resultados de las predicciones y métricas de negocio para el dashboard.

    Versionado: Usar DVC (Data Version Control) para los datasets y Git para el código del pipeline.

## 4.3 Modelado Avanzado (Objetivo: $R^2 = 0.8$)
Para elevar la precisión del actual LightGBM, se plantean los siguientes frentes:

1.  **Segmentación de Modelos:** Entrenamiento diferenciado por `tipo` (Casas vs. Departamentos).
2.  **Outliers Localizados:** Limpieza basada en la desviación estándar específica de cada barrio.
3.  **Ingeniería de Atributos:**
    *   Cálculo de distancia a estaciones de subte y avenidas principales.
    *   Precio promedio por m² del barrio como variable de entrada.
4.  **Target Transformation:** Aplicación de logaritmo al precio para normalizar la distribución.

## 4.4 MLOps / CI-CD
Evolución del stack tecnológico para garantizar estabilidad.

### Stack Mínimo Viable (MVP)
*   **Tracking:** MLflow (Registro de experimentos y métricas).
*   **CI/CD:** GitHub Actions para tests unitarios automáticos.
*   **Despliegue:** Docker + FastAPI.

### Stack Enterprise
*   **Orquestación:** Apache Airflow o Prefect para re-entrenamiento mensual.
*   **Monitoring:** Evidently AI para detectar *Data Drift* (cambios en los precios de mercado).

---

## Hoja de Ruta (Roadmap 4 Semanas)

### Semana 1: Consolidación de Datos
Es el cimiento. Si el dato está sucio o duplicado, el modelo aprende mal.
*   Implementación del Matching 4.1. 
*   Organización de capas Bronze y Silver.

### Semana 2: Ingeniería de Atributos
Una vez que los datos son únicos y confiables, busco el R².
*   Creación de variables espaciales (distancia a POIs).
*   Pruebas de segmentación por tipo de propiedad.

### Semana 3: Estabilización de Modelado
Con un modelo potente, necesito que sea profesional y rastreable
*   Optimización de hiperparámetros (GridSearchCV).
*   Registro de artefactos y pipelines en MLflow Model Registry.

### Semana 4: Despliegue y Monitoreo
Un modelo en una notebook no genera valor. 
*   Creación del endpoint de predicción (API o Streamlit).
*   Configuración de alertas de calidad de datos.