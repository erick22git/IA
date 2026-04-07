CREDIT APPROVAL (CRX) DATASET
Descripción general

El Credit Approval Dataset (CRX) se utiliza para analizar solicitudes de crédito y determinar si una persona debe ser aprobada o no.

Este dataset es común en problemas de clasificación, donde el objetivo es predecir si un cliente es confiable para otorgarle crédito.

Tipo de problema: clasificación

Estructura del dataset

El dataset se maneja normalmente en:

crx.csv (archivo preparado con cabeceras A1 a A16)
crx.names (descripción de variables)

Características generales:

690 registros aproximadamente
16 columnas (15 variables de entrada + 1 salida)
Mezcla de datos numéricos y categóricos

Las columnas suelen nombrarse como:

A1, A2, A3 ... A16

(No tienen nombres descriptivos originales, por eso se usan estos identificadores)

Variable objetivo

La variable objetivo es:

A16

Valores posibles:

(aprobado)
(rechazado)

Es una variable categórica, por lo que es un problema de clasificación.

Análisis básico

Al revisar el dataset se observa que:

Hay valores faltantes representados como “?”
No todas las variables son fáciles de interpretar
Hay mezcla de datos numéricos y categóricos
El dataset es relativamente pequeño
Preprocesamiento

Para trabajar con este dataset se recomienda:

Reemplazar o eliminar valores “?”
Convertir variables categóricas a numéricas
Normalizar datos numéricos
Revisar correlaciones entre variables
Importancia

Este dataset es útil porque:

Representa un problema real del sector financiero
Permite practicar clasificación binaria
Es bueno para probar distintos modelos
Ayuda a trabajar con datos incompletos
Conclusión

El Credit Approval Dataset es un dataset sencillo pero útil para clasificación. Aunque las variables no son descriptivas, permite aplicar técnicas importantes de preprocesamiento y modelado.

Archivos utilizados
crx.csv
crx.names
