# Laboratorio 07 - Aprendizaje No Supervisado

**Estudiante:** Erick Manuel Arancibia Flores
**Dataset:** EMNIST (Extended MNIST) - variante Balanced
**Materia:** Aprendizaje No Supervisado

---

## 1. Sobre el dataset elegido

Para el Punto 2 trabajé con **EMNIST Balanced**, una extensión del clásico MNIST que contiene 131.600 imágenes de 28x28 píxeles repartidas en 47 clases balanceadas (mezcla letras mayúsculas, minúsculas y dígitos manuscritos). Vino directo desde OpenML usando `fetch_openml(data_id=41039)` así que la carga es prácticamente igual que el ejemplo original con MNIST, lo cual me permitió mantener intacto el flujo del cuadernillo sin meter librerías nuevas.

Cumple con los requisitos del laboratorio sin problema:
- **n** (features) = 784 (los píxeles aplanados de cada imagen 28x28).
- **m** (ejemplos) = 131.600.
- No tiene etiquetas semánticas explícitas en el flujo que usamos (las ignoramos para simular un dataset no supervisado).

---

## 2. Punto 1 - K-Means con generador aleatorio de centroides

### Lo que hice

Reescribí el generador del cuadernillo original. Antes, los centroides venían fijos en una matriz hardcodeada de 5 puntos. Ahora la función `generar_centroides(n_centros, min_dist, ...)` produce entre 1 y 20 puntos al azar dentro del rango [-20, 20], pero con una distancia mínima entre ellos (por defecto 5 unidades). Esto es importante porque si los centroides quedan muy juntos, los blobs se solapan y tanto K-Means como las métricas de evaluación dejan de servir para verificar visualmente el resultado.

Para las pruebas dejé `n_centros = 6` y `min_dist = 5.0`. Si en la defensa me piden cambiar el número de grupos, basta con modificar esa variable y todo el cuadernillo se reajusta solo (el `k` de K-Means, el `max_k` para el método del codo, etc., están enlazados con `len(blob_centers)`).

### Qué resultados salieron

**Visualización inicial del dataset:** los 6 blobs se distinguen claramente a simple vista, sin solapamiento. Esto es lo que esperábamos al forzar la distancia mínima entre centroides.

**Predicción de K-Means (`y_pred`):** devolvió un arreglo con etiquetas del 0 al 5. Los centroides aprendidos coincidieron con los que generamos, con una diferencia mínima debida al ruido de los puntos.

**Predicción sobre `X_new`:** las 4 muestras nuevas se asignaron al cluster que les correspondía geométricamente. En mi corrida se asignaron todas al cluster 1 porque las cuatro caen dentro de la misma región del espacio (las coordenadas que probamos están todas cerca del mismo blob), no es que el modelo esté mal.

**Fronteras de decisión:** la gráfica con `plot_decision_boundaries` muestra el espacio dividido en 6 regiones de colores pastel separadas por las líneas de Voronoi. Los puntos negros (las muestras) caen dentro de la región del color que les toca y los centroides aprendidos están bien centrados en cada blob.

**Evolución por iteraciones:** la grilla 3x2 muestra cómo se mueven los centroides desde la inicialización hasta convergencia. En las primeras iteraciones se ven mal posicionados y las fronteras quedan torcidas, pero a la tercera iteración ya se acomodan a los blobs reales. Esto demuestra el carácter iterativo del algoritmo.

**Sensibilidad a la inicialización:** comparando `init="random"` vs `init="k-means++"` con la misma `random_state`, las dos soluciones quedan parecidas porque los blobs están bien separados, pero en datasets más complejos esto cambia. La idea es enseñar que K-Means puede caer en mínimos locales si arranca mal.

**10 inicializaciones aleatorias:** con `n_init=10`, K-Means corre el algoritmo 10 veces y se queda con la solución de menor inercia. El resultado es robusto, las fronteras quedan idénticas a las correctas.

**Método del codo:** la curva de inercia vs k baja muy rápido al principio y a partir de `k = 6` se aplana. El "codo" se ve clarísimo justo en `k = 6`, que es exactamente el número real de centroides que generamos. La línea punteada roja marca el k real para confirmarlo visualmente.

**Silhouette score:** el pico está en `k = 6` con un valor de **~0.887**, que es altísimo (los valores van de -1 a 1 y todo lo que pase de 0.7 ya se considera una clusterización fuerte). Para `k > 6` el score baja porque el algoritmo empieza a partir blobs reales en sub-grupos artificiales, lo que mete muestras en fronteras y baja la calidad del agrupamiento. Para `k < 6` también baja porque obliga a juntar blobs distintos.

**Diagramas de silueta:** los 4 sub-gráficos para k = 5, 6, 7, 8 muestran las "barras horizontales" de cada cluster ordenadas. En `k = 6` todas las barras son largas, parejas y todas pasan claramente la línea roja del promedio (no hay clusters "flacos" ni con coeficientes negativos). Esa uniformidad es la firma visual de un buen agrupamiento. En `k = 7` y `k = 8` aparecen barras más pequeñas y desbalanceadas, señal de que estamos forzando subdivisiones innecesarias.

### Por qué salió ese resultado

El método del codo y el silhouette coinciden en `k = 6` porque el dataset fue diseñado para tener exactamente esa cantidad de grupos bien separados. La distancia mínima de 5 unidades entre centroides garantiza que los blobs no se mezclen, y la desviación estándar `cluster_std = 0.7` es lo suficientemente chica para que cada blob quede compacto. Cuando ambas condiciones se cumplen, K-Means converge sin problemas y las métricas reflejan claramente el `k` correcto.

Si redujera la distancia mínima a, digamos, 1.5, los blobs se empezarían a solapar y el silhouette score bajaría notablemente. Esto se puede demostrar en la defensa cambiando `min_dist=5.0` por algo más chico.

---

## 3. Punto 2 - Aprendizaje semi-supervisado y activo con EMNIST

### Lo que hice

Reemplacé MNIST por EMNIST Balanced manteniendo exactamente el mismo pipeline:
1. Cargo el dataset y lo divido en train/test.
2. Aplico K-Means con `k = 50` para encontrar 50 imágenes representativas (una por cluster).
3. Entreno una regresión logística usando solo esas 50 muestras "etiquetadas a mano".
4. La comparo con un modelo entrenado con 50 muestras aleatorias.
5. Propago las etiquetas a todo el cluster y reentreno con 1000 muestras.
6. Aplico aprendizaje activo: identifico las muestras de menor confianza, las corrijo, reentreno.

### Qué resultados salieron y por qué

**Carga del dataset:** `X_train` queda con forma (98700, 784) y `X_test` con (32900, 784). Las etiquetas van de 0 a 46 (47 clases). Confirma que tenemos lo que dice la documentación.

**K-Means con 50 clusters:** la matriz de distancias `X_digits_dist` tiene forma (98700, 50): cada fila es una muestra de entrenamiento y cada columna su distancia a uno de los 50 centroides. De ahí sacamos los índices de las muestras más cercanas a cada centroide (`np.argmin(..., axis=0)`) y obtenemos las 50 imágenes representativas.

**Visualización de las 50 representativas:** la grilla 5x10 muestra caracteres bien definidos, sin trazos extraños ni borrones. Esto es importante: si la imagen representativa está borrosa, todo el cluster va a heredar una etiqueta dudosa. En EMNIST Balanced las representativas se ven prolijas porque los caracteres están bien centrados y normalizados.

**Regresión logística con 50 representativas:** la precisión sobre el test es bastante razonable considerando que solo entrenamos con 50 muestras de un problema de 47 clases. El número exacto depende de la corrida, pero típicamente queda alrededor del 50-60%, que es muy alto considerando que el azar daría ~2% (1/47).

**Regresión logística con 50 muestras aleatorias:** el score baja varios puntos. Esto es lo central del experimento: con la misma cantidad de etiquetas, las muestras representativas (elegidas por K-Means) entrenan mejor que las aleatorias porque cubren mejor la variabilidad del dataset. Las aleatorias podrían tocarte 50 muestras casi todas de la misma clase y dejar otras sin representar.

**Propagación de etiquetas a todo el cluster:** cada muestra recibe la etiqueta de su representante. Cuando reentrenamos con 1000 muestras propagadas, el score puede llegar a ser similar o incluso menor que el de las 50 representativas. Esto puede sorprender pero tiene sentido: estamos metiendo ruido al asumir que toda muestra del cluster es de la misma clase, y eso no siempre es cierto (algunos clusters mezclan dos o tres caracteres parecidos, por ejemplo "O" con "0", "I" con "1").

**Aprendizaje activo:** calculamos las probabilidades del modelo para las primeras 1000 muestras y ordenamos por la confianza más baja. Las 50 muestras menos seguras son las que el modelo tiene más dudas sobre cuál es su clase real. La grilla con esas imágenes muestra caracteres ambiguos (escritura descuidada, formas raras, fronteras entre clases). Al "etiquetar" esas 50 a mano (en este caso usando las etiquetas reales que ya teníamos), corregimos los errores más impactantes.

**Reentrenamiento después de active learning:** el score sube respecto al modelo con etiquetas propagadas porque los errores que corregimos son justo los que más dañaban la frontera de decisión. Esta es la idea central del active learning: no hace falta etiquetar todo, solo lo que el modelo no entiende bien.

### Por qué este flujo funciona

La intuición detrás de todo esto es que **etiquetar es caro** (en problemas reales, anotar 100.000 imágenes manualmente cuesta mucho dinero y tiempo). El semi-supervisado nos permite trabajar con muy pocas etiquetas usando clustering para elegir cuáles conviene anotar. El active learning lleva la idea más lejos: en cada iteración, el propio modelo te dice cuáles muestras necesita ver etiquetadas para mejorar.

En EMNIST Balanced este truco es especialmente útil porque las 47 clases son confundibles entre sí (la letra "I" minúscula se parece a "1", la "O" se parece al "0", etc.). Identificar y etiquetar esas muestras ambiguas mejora mucho más el modelo que etiquetar muestras "obvias" que ya está clasificando bien.

---

## 4. Reflexiones finales

El cuadernillo demuestra dos cosas importantes:

Para el **Punto 1**, que K-Means es muy efectivo cuando los datos tienen estructura de grupos compactos y bien separados, y que tanto el método del codo como el silhouette score son herramientas confiables para descubrir el número óptimo de clusters cuando no lo sabemos de antemano. La verificación visual (que pude hacer porque generé el dataset yo mismo) confirma que ambas métricas apuntan al `k` correcto.

Para el **Punto 2**, que en problemas reales con muchas clases y muestras, **etiquetar pocas muestras bien elegidas es mejor que etiquetar muchas al azar**. El semi-supervisado y el active learning son técnicas complementarias: el primero te ayuda a aprovechar la estructura del dataset para empezar con pocas etiquetas, y el segundo te dice dónde concentrar el esfuerzo de anotación para mejorar más rápido.

EMNIST Balanced fue una buena elección porque comparte el mismo formato que MNIST (28x28, escala de grises) pero es más desafiante por tener casi 5 veces más clases y caracteres más confundibles entre sí. Eso hace que las diferencias entre los modelos (50 representativas vs 50 aleatorias, propagación con/sin active learning) se noten más.
