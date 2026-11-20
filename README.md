# Estimación del Índice de Calidad del Aire (AQI) mediante Aprendizaje Automático

Proyecto final del curso **Modelos y Simulación II** (Universidad de Antioquia).
Autores: Valentina Hoyos, Naneth Martelo y Juan Velásquez.

Se construye y evalúa un sistema de **regresión supervisada** para estimar el AQI de Dhaka
(Bangladesh) a partir de variables meteorológicas, geográficas y urbanas, **sin mediciones de
contaminantes** (dataset [Dhaka Air Quality Index](https://www.kaggle.com/datasets/nasiruddin123/dhaka-air-quality-index-dataset)).

## Estructura del repositorio

```
air-quality-prediction-analysis/
├── data/
│   └── dhaka_air_quality_no_pollutants.csv
├── notebooks/
│   ├── proyecto_final_AQI.ipynb
│   └── Exploración_inicial_dataset_Dhaka_AQI.ipynb
├── reporte/
│   └── air_quality_models_comparation.pdf
├── requirements.txt
└── README.md
```

| Archivo | Descripción |
|---|---|
| `data/dhaka_air_quality_no_pollutants.csv` | Conjunto de datos: 10.000 registros y 13 variables |
| `notebooks/proyecto_final_AQI.ipynb` | **Notebook principal**: reproduce todos los resultados del informe |
| `notebooks/Exploración_inicial_dataset_Dhaka_AQI.ipynb` | Exploración inicial de los datos (primera entrega) |
| `reporte/informe_final.pdf` | Informe final (plantilla IEEE) |
| `requirements.txt` | Librerías necesarias para ejecutar los notebooks |

## Cómo reproducir los resultados

### Opción 1: Google Colab
Abrir el notebook principal con el botón
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/JuanMauwu/air-quality-prediction-analysis/blob/main/notebooks/proyecto_final_AQI.ipynb)
y ejecutar *Entorno de ejecución → Ejecutar todas*. Si el CSV no está disponible localmente, el
notebook lo descarga automáticamente desde este repositorio e instala `umap-learn` si hace falta.

### Opción 2: local
```bash
git clone https://github.com/JuanMauwu/air-quality-prediction-analysis.git
cd air-quality-prediction-analysis
python -m venv .venv && source .venv/bin/activate   # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/proyecto_final_AQI.ipynb
```

> **Tiempo de ejecución:** ≈ 1,5 horas con 2 núcleos de CPU (la búsqueda en malla del SVR y la
> validación cruzada con UMAP son las etapas más costosas). Todas las semillas están fijas
> (`random_state=42`), por lo que los resultados son reproducibles.

## Metodología

* **Preprocesamiento:** se eliminan `aqi` (objetivo) y `aqi_category` (fuga de información);
  `StandardScaler` para las 6 variables numéricas y `OneHotEncoder(drop='first')` para las 5
  categóricas → 31 características. Todo dentro de un `Pipeline` (nada se ajusta antes de la partición).
* **Validación:** partición 80 % / 20 % **estratificada por `aqi_category`** (sólo para la partición,
  nunca como entrada); `GridSearchCV` con validación cruzada `StratifiedKFold` de 5 folds; selección
  por RMSE de validación; intervalos de confianza del 95 % (t de Student) sobre los folds.
  Métricas: RMSE, MAE, MedAE y R².
* **Modelos:** Ridge con expansión polinomial (paramétrico), k-NN (no paramétrico), Random Forest
  (ensamble de árboles), MLP (red neuronal) y SVR con kernel RBF. Todos los óptimos quedan en el
  interior de sus mallas de búsqueda.
* **Reducción de dimensión:** análisis individual (Pearson, Spearman, η², información mutua,
  V de Cramér); **selección por consenso** (4 métodos × 5 folds: prueba F con Bonferroni,
  información mutua y Random Forest frente a variables sonda de ruido, y Lasso); PCA con número de
  componentes por consenso entre folds + criterio *wrapper*; UMAP no supervisado. Los
  hiperparámetros de los 2 mejores modelos se re-ajustan en cada espacio reducido.

## Resultados principales

| Modelo | RMSE validación (IC 95 %) | RMSE test | R² test |
|---|---|---|---|
| Ridge (grado 2) | 26,22 ± 0,58 | 26,13 | 0,892 |
| k-NN | 28,86 ± 1,24 | 28,88 | 0,868 |
| Random Forest | 25,72 ± 0,86 | 25,72 | 0,896 |
| **MLP** | **25,33 ± 0,76** | **25,03** | **0,901** |
| SVR | 25,61 ± 0,75 | 25,50 | 0,897 |
| SVR + PCA (10 comp., −67,7 %) | 25,43 ± 1,35 | 24,77 | 0,903 |
| SVR + UMAP (10 comp.) | 33,14 ± 1,70 | 33,35 | 0,824 |

El informe completo está en [`reporte/air_quality_models_comparation.pdf`]([air_quality_models_comparation.pdf](https://github.com/JuanMauwu/air-quality-prediction-analysis/blob/main/reporte/air_quality_models_comparation.pdf)).
