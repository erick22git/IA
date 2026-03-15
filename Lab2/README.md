# Laboratorio 1 - Regresión Lineal Multivariable

## Estudiante

Erick Arancibia

## Descripción del laboratorio

En este laboratorio se implementa un modelo de regresión lineal utilizando Python para predecir el precio de viviendas a partir de múltiples características.
El objetivo es entrenar un modelo de aprendizaje automático capaz de realizar predicciones minimizando el error de costo mediante el uso de diferentes métodos de entrenamiento.

Se implementaron tres enfoques:

* Regresión lineal multivariable con gradiente descendente
* Regresión polinómica
* Cálculo de parámetros mediante la ecuación normal

Todo el proceso fue realizado en un cuadernillo de Python utilizando la librería Pandas para el procesamiento del dataset.

---

# Dataset utilizado

Dataset: House Sales in King County USA

Este dataset contiene información sobre ventas de viviendas en el condado de King County, Estados Unidos (zona de Seattle). Cada registro representa una vivienda vendida junto con sus características físicas y geográficas.

Características del dataset:

* Número de ejemplos (m): 21613
* Número de características (n): 21

Por lo tanto el dataset cumple con los requisitos del laboratorio:

* n ≥ 20 características
* m ≥ 20000 ejemplos

Algunas de las características utilizadas son:

* bedrooms
* bathrooms
* sqft_living
* sqft_lot
* floors
* waterfront
* view
* condition
* grade
* sqft_above
* sqft_basement
* yr_built
* yr_renovated
* lat
* long
* sqft_living15
* sqft_lot15

La variable objetivo (y) corresponde al precio de la vivienda.

---

# Preprocesamiento de datos

Para preparar el dataset se realizaron los siguientes pasos:

1. Carga del dataset utilizando la librería Pandas.
2. Selección de variables numéricas.
3. Separación de variables predictoras (X) y variable objetivo (y).
4. Normalización de las características utilizando media y desviación estándar.
5. Adición de una columna de unos para el cálculo del modelo de regresión.

---

# Modelos implementados

## 1. Regresión lineal multivariable

Se implementó el algoritmo de gradiente descendente para minimizar la función de costo.

La función de costo utilizada es:

J(θ) = (1/2m) Σ (hθ(x) − y)²

Durante el entrenamiento se calculó el error en cada iteración y se generó el gráfico de convergencia del costo.

---

## 2. Regresión polinómica

Para mejorar la capacidad del modelo se añadieron características polinómicas elevando algunas variables al cuadrado.

Esto permite capturar relaciones no lineales entre las variables y el precio de la vivienda.

---

## 3. Ecuación normal

También se calcularon los parámetros del modelo utilizando la ecuación normal:

θ = (XᵀX)⁻¹ Xᵀy

Este método permite obtener los parámetros óptimos sin necesidad de iteraciones.

---

# Resultados

Se entrenaron los tres modelos y se evaluó su comportamiento observando la reducción del error de costo durante el entrenamiento.

Además se realizaron al menos 100 predicciones utilizando el modelo entrenado para verificar su funcionamiento.

