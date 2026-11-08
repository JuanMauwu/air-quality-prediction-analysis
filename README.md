# Estimación del Índice de Calidad del Aire (AQI) mediante Aprendizaje Automático

Este repositorio contiene el desarrollo del proyecto final para la materia **Modelos y Simulación II** de la Universidad de Antioquia. El objetivo principal es construir y evaluar un sistema de predicción supervisada de regresión para estimar el AQI en la ciudad de Dhaka.

## Estructura del Repositorio

* `data/`: Contiene el conjunto de datos `dhaka_air_quality_no_pollutants.csv`.
* `notebooks/`: Notebooks (.ipynb) con la limpieza, exploración y modelado.
* `report/`: Documento PDF del informe final bajo el formato plantilla IEEE Transactions.
* `requirements.txt`: Lista de librerías y dependencias necesarias para la ejecución.

## Modelos Evaluados

De acuerdo con los requerimientos del curso, se comparan 5 familias de algoritmos supervisados de regresión:
1. **Modelo Paramétrico:** Regresión Lineal
2. **Modelo No Paramétrico:** k-NN
3. **Ensamble de Árboles:** Random Forest Regressor
4. **Red Neuronal Artificial:** Perceptrón Multicapa (MLPRegressor)
5. **Máquina de Vectores de Soporte:** Support Vector Regressor (SVR)

## Reducción de Dimensionalidad

Se evalúa la redundancia de características y el impacto en los 2 mejores modelos mediante:
* Análisis individual de correlaciones (Pearson/Spearman)
* Extracción Lineal mediante **PCA**
* Extracción No Lineal mediante **UMAP**

## Instrucciones de Ejecución

1. Clonar el repositorio.
2. Crear un entorno virtual e instalar las dependencias:
   ```bash
   pip install -r requirements.txt
