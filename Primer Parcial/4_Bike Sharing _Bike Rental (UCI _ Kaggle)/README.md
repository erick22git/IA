BIKE SHARING DATASET
Descripción general

El Bike Sharing Dataset contiene información sobre el alquiler de bicicletas en un sistema público. Los datos registran cuántas bicicletas se alquilan en diferentes momentos, junto con condiciones como el clima, fecha y hora.

Este dataset se usa para analizar la demanda de bicicletas y predecir cuántos alquileres habrá en determinado momento.

Tipo de problema: regresión

Estructura del dataset

Este dataset está compuesto por varios archivos, los más usados son:

day.csv
hour.csv
train.csv
test.csv

En general:

Tiene registros por día y por hora
Incluye variables de tiempo y clima
Contiene datos numéricos y categóricos

Algunas columnas importantes:

cnt: total de bicicletas alquiladas
temp: temperatura
hum: humedad
windspeed: velocidad del viento
season: estación del año
workingday: si es día laboral o no
Variable objetivo

La variable objetivo es:

cnt (cantidad de bicicletas alquiladas)

Es una variable numérica, por lo que el problema es de regresión.

Análisis básico

Al revisar el dataset se puede notar que:

No suele tener muchos valores nulos
Hay patrones claros según la hora o el día
El clima influye bastante en la cantidad de alquileres
Hay diferencias entre días laborales y fines de semana
Preprocesamiento

Para trabajar con este dataset se recomienda:

Revisar y limpiar datos si es necesario
Convertir variables categóricas a numéricas
Normalizar variables como temperatura o humedad
Analizar la variable de tiempo (hora, día, mes)
Importancia

Este dataset es útil porque:

Permite analizar comportamiento de usuarios
Es fácil de entender
Sirve para practicar modelos de regresión
Tiene variables reales del entorno (clima, tiempo)
Conclusión

El Bike Sharing Dataset es un dataset claro y práctico para trabajar regresión. Permite ver cómo diferentes factores afectan la demanda de bicicletas.

Archivos utilizados
day.csv
hour.csv
train.csv
test.csv
