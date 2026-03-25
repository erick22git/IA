# PRACTICO 01 – REGRESIÓN LOGÍSTICA

## 1. Descripción
En este práctico se implementó el algoritmo de Regresión Logística para resolver un problema de clasificación binaria utilizando Python y el dataset Adult Income.

## 2. Dataset utilizado
Se utilizó el Adult Income Dataset del repositorio UCI Machine Learning. 
Este dataset contiene información demográfica y laboral de personas, y el objetivo es predecir si una persona gana más de 50K al año.

El dataset cumple con los requisitos:
- Más de 1000 ejemplos
- Más de 5 características
- Problema de clasificación binaria

## 3. Características utilizadas
Para la implementación del modelo se utilizaron las siguientes características:
- Edad
- Horas trabajadas por semana

## 4. Variable objetivo
La variable objetivo es el ingreso anual:
- 1 → Ingreso mayor a 50K
- 0 → Ingreso menor o igual a 50K

## 5. Algoritmo implementado
Se implementó Regresión Logística desde cero, incluyendo:
- Función sigmoide
- Función de costo
- Descenso por gradiente
- Optimización con scipy.optimize
- Límite de decisión
- Predicción
- Precisión del modelo

## 6. Resultados
Se obtuvo la gráfica de:
- Datos de entrenamiento
- Convergencia del costo
- Límite de decisión
- Precisión del modelo
