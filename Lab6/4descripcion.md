# Dataset 4 — Bike Sharing / Bike Rental (UCI)

## Descripción del Dataset

El dataset **Bike Sharing** del UCI Machine Learning Repository contiene registros por hora de alquiler de bicicletas en Washington D.C. durante 2011 y 2012. Fue publicado por Hadi Fanaee-T y Joao Gama en 2013.

- **Archivo usado:** `hour.csv`
- **Registros:** 17,379 horas
- **Características:** 12 variables predictivas
- **Variable objetivo:** `cnt` convertida en 3 clases de demanda
- **Tipo de problema:** Clasificación multiclase (3 clases)

## Cumplimiento de requisitos

| Requisito | Valor requerido | Valor del dataset |
|---|---|---|
| n (propiedades) | n ≥ 5 | 12 features seleccionadas |
| m (ejemplos) | m ≥ 5,000 | 17,379 registros |
| Tipo | Multiclase | 3 clases de demanda |

## Variables del Dataset

| Variable | Tipo | Descripción |
|---|---|---|
| instant | Índice | Identificador del registro (descartado) |
| dteday | Fecha texto | Fecha (descartada) |
| season | Categórica | Temporada: 1=primavera, 2=verano, 3=otoño, 4=invierno |
| yr | Binaria | Año: 0=2011, 1=2012 |
| mnth | Numérica | Mes: 1 a 12 |
| hr | Numérica | Hora del día: 0 a 23 |
| holiday | Binaria | 1 si es día festivo |
| weekday | Numérica | Día de la semana: 0 a 6 |
| workingday | Binaria | 1 si es día laboral |
| weathersit | Categórica | Clima: 1=claro, 2=nublado, 3=lluvia ligera, 4=lluvia fuerte |
| temp | Continua | Temperatura normalizada (0 a 1) |
| atemp | Continua | Sensación térmica normalizada (0 a 1) |
| hum | Continua | Humedad normalizada (0 a 1) |
| windspeed | Continua | Velocidad del viento normalizada (0 a 1) |
| casual | Numérica | Usuarios ocasionales (descartada, compone cnt) |
| registered | Numérica | Usuarios registrados (descartada, compone cnt) |
| cnt | Numérica | Total de bicicletas alquiladas (variable objetivo original) |

## Creación de la variable multiclase

La variable `cnt` se agrupó en 3 clases de demanda:

| Clase | Nombre | Rango de cnt | Registros |
|---|---|---|---|
| 0 | Demanda BAJA | cnt < 100 | ~6,400 |
| 1 | Demanda MEDIA | 100 ≤ cnt < 300 | ~7,500 |
| 2 | Demanda ALTA | cnt ≥ 300 | ~3,400 |

## Columnas descartadas y por qué

**instant** se descartó porque es un índice sin valor predictivo.

**dteday** se descartó porque es una fecha en formato texto que no se puede usar directamente en operaciones matemáticas.

**casual** y **registered** se descartaron porque juntas componen exactamente cnt (casual + registered = cnt). Incluirlas sería trampa: el modelo aprendería que casual + registered determina la clase sin aprender nada real.

## Lo que se hizo en el laboratorio

### Preprocesamiento

1. Se cargó `hour.csv` con pandas
2. Se creó la variable `demanda` agrupando `cnt` en 3 clases
3. Se seleccionaron 12 features predictivas
4. Se normalizaron con media 0 y desviación estándar 1
5. Se dividió en 75% entrenamiento (13,034 muestras) y 25% prueba (4,345 muestras)

### Modelo implementado

**Regresión Logística One-vs-All**

Para clasificación multiclase se entrenaron 3 clasificadores binarios:
- Clasificador 0: ¿Es demanda BAJA o no?
- Clasificador 1: ¿Es demanda MEDIA o no?
- Clasificador 2: ¿Es demanda ALTA o no?

La predicción final es la clase cuyo clasificador produce la mayor probabilidad (argmax).

### Métricas aplicadas

- **Accuracy:** porcentaje total de horas clasificadas correctamente
- **Matriz de Confusión 3×3:** muestra qué clases confunde el modelo
- **Precision por clase:** cuántas predicciones de cada clase fueron correctas
- **Recall por clase:** cuántas horas de cada clase fueron detectadas
- **F1-Score por clase:** balance entre precision y recall
- **Macro vs Weighted:** macro promedia igual para las 3 clases, weighted pondera por tamaño

### Decisiones tomadas

Se usó `hour.csv` en lugar de `day.csv` porque tiene 17,379 registros vs 731, lo que da mucho más datos para entrenar. Se eligieron los límites de clase (100 y 300) para que las 3 clases tuvieran distribuciones razonables y no demasiado desbalanceadas.

