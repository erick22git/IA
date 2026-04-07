AUSTRALIAN CREDIT DATASET
Descripción general

El Australian Credit Dataset se utiliza para analizar solicitudes de crédito y decidir si deben ser aprobadas o no. Es similar al dataset CRX, pero con datos ya más estructurados y limpios.

Se usa principalmente en problemas de clasificación.

Tipo de problema: clasificación

Estructura del dataset

El dataset se maneja en archivos como:

australiano.dat (o convertido a .csv)
australiano.pdf (documentación)

Características generales:

690 registros aproximadamente
15 variables de entrada + 1 variable objetivo
Datos numéricos y categóricos (aunque muchos ya están codificados)

A diferencia de otros datasets, muchas variables ya están en formato numérico, lo que facilita su uso.

Variable objetivo

La variable objetivo es la última columna del dataset.

Valores posibles:

1 (aprobado)
0 (rechazado)

Es un problema de clasificación binaria.

Análisis básico

Al revisar el dataset se observa que:

No tiene tantos valores faltantes como otros datasets
Muchas variables ya están transformadas
Es más fácil de trabajar comparado con CRX
Las variables no tienen nombres descriptivos
Preprocesamiento

Para trabajar con este dataset se recomienda:

Verificar si existen valores faltantes
Normalizar variables numéricas
Revisar distribución de datos
Separar correctamente variables de entrada y salida
Importancia

Este dataset es útil porque:

Es más limpio que otros datasets de crédito
Permite trabajar clasificación de forma más directa
Es ideal para comparar modelos
Representa un problema real financiero
Conclusión

El Australian Credit Dataset es una buena opción para trabajar clasificación binaria sin necesidad de demasiado preprocesamiento. Es más sencillo que otros datasets similares.

Archivos utilizados
australiano.dat (o .csv)
australiano.pdf
