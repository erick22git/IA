ADULT INCOME DATASET
Descripción general

El Adult Income Dataset es un conjunto de datos que se utiliza para predecir si una persona gana más o menos de 50K al año, basándose en información personal y laboral.

Este dataset es bastante conocido y se usa mucho para problemas de clasificación.

Tipo de problema: clasificación

Estructura del dataset

El dataset generalmente se maneja en un archivo principal:

adult.csv

Características generales:

Aproximadamente 48,000 registros
14 columnas de entrada y 1 de salida
Datos numéricos y categóricos

Algunas columnas importantes:

age: edad
workclass: tipo de trabajo
education: nivel educativo
occupation: ocupación
relationship: estado familiar
race: raza
sex: género
hours-per-week: horas trabajadas por semana
native-country: país de origen
income: ingreso (variable objetivo)
Variable objetivo

La variable objetivo es:

income

Valores posibles:

<=50K

50K

Es una variable categórica, por lo tanto es un problema de clasificación.

Análisis básico

Al revisar el dataset se observa que:

Hay valores faltantes representados como “?”
Existen varias variables categóricas
Algunas columnas tienen muchas categorías diferentes
Puede haber desbalance entre las clases
Preprocesamiento

Para trabajar con este dataset se recomienda:

Reemplazar o eliminar valores “?”
Convertir variables categóricas a numéricas
Normalizar variables numéricas si es necesario
Revisar el balance de clases
Importancia

Este dataset es importante porque:

Es muy usado en Machine Learning
Permite trabajar con clasificación binaria
Representa un problema real (ingresos)
Es útil para practicar preprocesamiento
Conclusión

El Adult Income Dataset es ideal para problemas de clasificación. Permite analizar cómo diferentes factores influyen en el nivel de ingresos de una persona.

Archivos utilizados
adult.csv
