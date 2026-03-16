# Regresión Logística Binaria – Census Income Dataset

## Descripción del proyecto

Este proyecto tiene como objetivo implementar un modelo de **regresión logística binaria** para resolver un problema de clasificación utilizando un dataset real.

El modelo busca predecir una clase a partir de diferentes características presentes en los datos, aplicando técnicas básicas de **Machine Learning** y **preprocesamiento de datos con Python**.

El dataset utilizado contiene información socioeconómica de personas y permite clasificar si el ingreso de un individuo supera cierto nivel.

---

## Dataset utilizado

Se utilizó el dataset **Census Income KDD**, el cual contiene información demográfica y económica de aproximadamente **199,523 registros** y **más de 40 variables**.

Este dataset cumple con los requisitos del ejercicio:

* Más de **30000 ejemplos**
* Más de **40 propiedades**
* Problema de **clasificación binaria**

---

## Tecnologías utilizadas

Para el desarrollo del proyecto se utilizaron las siguientes herramientas:

* Python
* Pandas
* NumPy
* Matplotlib
* Scikit-Learn

---

## Proceso realizado

El desarrollo del modelo siguió los siguientes pasos:

1. **Carga del dataset** utilizando la librería Pandas.
2. **Análisis inicial de los datos** para comprender su estructura.
3. **Preprocesamiento del dataset**, incluyendo la conversión de variables categóricas a valores numéricos.
4. **Normalización de los datos** para mejorar el entrenamiento del modelo.
5. **División del dataset** en:

   * 80% datos de entrenamiento
   * 20% datos de prueba
6. **Implementación del modelo de regresión logística** utilizando la función sigmoide.
7. **Entrenamiento del modelo** mediante el algoritmo de descenso de gradiente.
8. **Visualización de la función de costo** para observar el proceso de aprendizaje.
9. **Evaluación del modelo** mediante predicciones sobre los datos de prueba.

---

## Resultados

Durante el entrenamiento se observó una **disminución progresiva de la función de costo**, lo cual indica que el modelo logra aprender correctamente a partir de los datos.

Finalmente, el modelo fue evaluado utilizando el conjunto de prueba, obteniendo una precisión adecuada que demuestra la efectividad del algoritmo de clasificación implementado.

---

## Autor

Trabajo desarrollado como parte de una práctica de laboratorio sobre **regresión logística y clasificación binaria** en Machine Learning.

