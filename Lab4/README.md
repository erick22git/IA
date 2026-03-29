Laboratorio 4 — Clasificación Multiclase con Regresión Logística One-vs-All
Materia: Inteligencia Artificial I

Descripción
En este laboratorio se implementa el modelo de regresión logística One-vs-All para clasificación multiclase, aplicado a dos datasets diferentes según los requisitos de la consigna. El enfoque One-vs-All consiste en entrenar un clasificador binario por cada clase, donde cada clasificador aprende a distinguir su clase del resto. La predicción final es la clase cuyo clasificador devuelve la mayor probabilidad.

Estructura del repositorio
Lab4/
├── lab4_no_grafico_census.ipynb      # Notebook 1: dataset tabular (Census Income)
├── lab4_multimodal_fashion.ipynb     # Notebook 2: dataset con imágenes (Fashion)
├── census-income.data                # Dataset Census Income KDD
├── styles.csv                        # Metadatos del dataset Fashion
└── images/                           # Carpeta con imágenes de productos de moda

1 Dataset No Gráfico
Dataset: Census Income KDD
Fuente: UCI Machine Learning Repository
Características

Registros: +199,000 (m ≥ 50,000 )
Propiedades: 41 columnas demográficas y laborales (n ≥ 40 )
Clases: 5 grupos de ingresos creados combinando nivel de ingresos y semanas trabajadas

Clases definidas
ClaseDescripción0Ingresos bajos / pocas semanas trabajadas1Ingresos bajos / medio tiempo2Ingresos bajos / tiempo completo3Ingresos altos / tiempo parcial4Ingresos altos / tiempo completo
Proceso

Carga del CSV con Pandas y definición manual de los 42 nombres de columna
Creación de 5 clases multiclase a partir de las variables income y weeks_worked
Balanceo del dataset para igualar el número de ejemplos por clase
Codificación de variables categóricas con LabelEncoder
Normalización con StandardScaler y división 80/20
Entrenamiento del modelo One-vs-All con scipy.optimize
Evaluación con gráfica de costo por clasificador y gráfica de precisión por clase

2 — Dataset Multimodal con Imágenes
Dataset: Fashion Product Images
Fuente: Kaggle — Fashion Product Images Dataset
Características

Imágenes: +9,600 balanceadas (m ≥ 5,000 )
Propiedades: 3,072 valores por imagen (32×32×3 píxeles) (n > 100 )
Clases: 4 categorías de productos de moda
Tipo: Multimodal — combina archivo CSV con imágenes JPEG

Clases
ClaseCategoríaEjemplos0Accessories2,4031Apparel2,4032Footwear2,4033Personal Care2,403
Proceso

Carga del CSV styles.csv con Pandas y selección de columnas relevantes
Filtrado de clases con menos de 500 ejemplos
Balanceo tomando 2,403 imágenes por clase
Carga de imágenes con OpenCV: resize 32×32, conversión BGR→RGB, normalización 0-1, aplanamiento a vector de 3,072 valores
Codificación de etiquetas con LabelEncoder y división 80/20
Entrenamiento del modelo One-vs-All con scipy.optimize
Evaluación con gráficas de costo y precisión, predicción con imagen de prueba


Tecnologías utilizadas

Python 3
NumPy — operaciones matemáticas y vectoriales
Pandas — carga y preprocesamiento de datasets
OpenCV (cv2) — procesamiento de imágenes
Matplotlib — visualización de gráficas
Scikit-learn — normalización, división de datos y métricas
SciPy — optimización del modelo (método TNC)
