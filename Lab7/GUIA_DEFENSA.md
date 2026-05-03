# Guía de Defensa - Laboratorio 07 Aprendizaje No Supervisado

**Estudiante:** Erick Manuel Arancibia Flores
**Dataset:** EMNIST Balanced

---

## Resultados numéricos que tenés que tener a mano

| Experimento | Accuracy | Tiempo |
|---|---|---|
| 50 muestras representativas (elegidas por clustering) | **26.29%** | 0.78s |
| 50 muestras aleatorias | **15.37%** | 0.43s |
| 1000 muestras con etiquetas propagadas | **26.01%** | 3.75s |
| 1000 muestras + active learning (50 corregidas) | **27.38%** | 1.44s |

Silhouette score máximo: **0.89 en k=6** (que es el `k` real generado).

---

# ANÁLISIS DE GRÁFICAS Y PREGUNTAS POSIBLES

---

## Gráfica 1: Dataset generado sin etiquetas

**Lo que muestra:** 6 nubes de puntos azules dispersas en un plano 2D, sin solapamiento entre ellas. Las nubes están distribuidas aleatoriamente en el rango aproximado x=[-20, 15] y y=[-15, 20].

**Por qué se ve así:** porque modificamos el generador para que ponga centroides aleatorios con distancia mínima de 5 unidades entre ellos. La desviación estándar de cada blob es 0.7, lo que hace que los puntos queden compactos alrededor del centroide. Esto es lo IDEAL para K-Means: clusters esféricos, compactos y bien separados.

**Posibles preguntas del ingeniero:**

- *¿Qué pasaría si reduces la distancia mínima entre centroides?*
  → Los blobs se empezarían a solapar, K-Means tendría problemas para separarlos correctamente y el silhouette score bajaría notablemente.

- *¿Por qué generas el dataset en 2D y no en más dimensiones?*
  → Para poder verificar visualmente que el algoritmo funciona. En 2D se ven los grupos. Si fueran 10 dimensiones, no podríamos confirmar a ojo si los clusters están bien.

- *¿Qué pasa si pongo `n_centros = 1` o `n_centros = 20`?*
  → Con 1 centro, K-Means con k=1 da inercia alta pero silhouette = 0 (no se puede calcular). Con 20, todavía funciona pero el silhouette baja porque los grupos están más cerca entre sí (menos espacio disponible para separarlos).

**Para hacer la prueba en la defensa:** cambiar `n_centros = 6` por `n_centros = 3` o `n_centros = 15` y mostrar que todo el flujo se reajusta automáticamente.

---

## Gráfica 2: Fronteras de decisión de K-Means

**Lo que muestra:** el plano dividido en 6 regiones de colores pastel separadas por líneas negras. Cada región contiene exactamente uno de los blobs y los centroides aprendidos (X blanca con círculo) están centrados en cada blob.

**Por qué se ve así:** K-Means construye un **diagrama de Voronoi**: cada punto del plano se asigna al centroide más cercano por distancia euclidiana. Por eso las fronteras son rectas (segmentos perpendiculares a la línea entre dos centroides).

**Posibles preguntas:**

- *¿Por qué las fronteras son siempre líneas rectas?*
  → Porque K-Means usa distancia euclidiana al centroide. Esto se llama tessellation de Voronoi. Si quisieras fronteras curvas tendrías que usar otro algoritmo como DBSCAN o Gaussian Mixtures.

- *¿Qué pasa si una muestra cae justo en una frontera?*
  → Se asigna al cluster con menor distancia euclidiana al centroide. En caso de empate exacto, sklearn la asigna al primero que evalúa.

- *¿Por qué hay regiones grandes vacías de puntos?*
  → Porque el espacio entre clusters también tiene que pertenecer a algún cluster. Si llegara una muestra nueva en esa zona, se asignaría al centroide más cercano. Eso es lo que vemos en la celda de `X_new`: las 4 muestras dieron cluster 1 porque caen dentro de esa región.

---

## Gráfica 3: Iteraciones del algoritmo (3x2)

**Lo que muestra:** seis sub-gráficas mostrando cómo evoluciona K-Means desde la inicialización (arriba izquierda con X rojas dispersas) hasta la convergencia final (abajo derecha con X blancas centradas en cada blob).

**Por qué se ve así:** el algoritmo K-Means tiene 2 pasos que se repiten en cada iteración:

1. **Update centroids** (columna izquierda): mueve cada centroide al promedio de los puntos asignados a su cluster.
2. **Label instances** (columna derecha): reasigna cada punto al centroide más cercano.

En la iteración 1 (arriba) los centroides arrancan mal posicionados (algunos quedaron juntos en el mismo blob), por eso las fronteras se ven torcidas. En la iteración 2 (medio) ya empiezan a moverse hacia los centros correctos. En la iteración 3 (abajo) están casi convergidos.

**Posibles preguntas:**

- *¿Cuántas iteraciones necesita K-Means para converger?*
  → Depende del dataset y la inicialización. Para nuestro caso con blobs bien separados, converge en 3-5 iteraciones. En problemas reales puede tomar 50-300.

- *¿Qué es el criterio de convergencia?*
  → Cuando los centroides ya casi no se mueven entre iteraciones (el cambio está debajo de un umbral `tol` que sklearn fija por defecto en 1e-4) o cuando se alcanza `max_iter`.

- *¿Por qué usaste `algorithm="elkan"`?*
  → Es una versión optimizada que usa la desigualdad triangular para evitar calcular distancias innecesarias. Es más rápido en datasets densos como el nuestro. La alternativa es "lloyd" (la versión clásica).

---

## Gráfica 4: Sensibilidad a la inicialización (Solution 1 vs Solution 2)

**Lo que muestra:** dos soluciones distintas de K-Means con la misma data pero distinta inicialización. Ambas separan los 6 blobs correctamente pero los **colores de las regiones son distintos** (lo que era amarillo en una es azul en la otra, etc.).

**Por qué se ve así:** los IDs de los clusters son arbitrarios. K-Means no sabe que "este blob es la clase A", solo asigna números 0, 1, 2, ... según el orden en que arranca a iterar. Por eso dos corridas pueden dar el mismo agrupamiento pero con etiquetas permutadas.

**Posibles preguntas:**

- *¿Las dos soluciones son equivalentes?*
  → En este caso sí, porque los blobs están bien separados. Pero en datasets más complejos, dos inicializaciones pueden dar agrupamientos REALMENTE distintos (mínimos locales). Por eso usamos `n_init=10` para correr 10 veces y quedarnos con la mejor.

- *¿Qué métrica usa K-Means para decidir cuál inicialización es mejor?*
  → La **inercia**: la suma de distancias al cuadrado de cada punto a su centroide. Menor inercia = mejor solución.

- *¿Por qué `init="k-means++"` es mejor que `init="random"`?*
  → k-means++ elige los centroides iniciales de forma inteligente: el primero al azar, y los siguientes con probabilidad proporcional a la distancia a los centroides ya elegidos. Así arranca con centroides más separados y converge más rápido a una buena solución.

---

## Gráfica 5: K-Means con 10 inicializaciones aleatorias

**Lo que muestra:** las fronteras de decisión finales después de correr K-Means 10 veces con distintos arranques aleatorios. El resultado es estable y los centroides están bien centrados en los blobs.

**Por qué se ve así:** al correr 10 veces, sklearn se queda con la corrida de menor inercia. Esto reduce el riesgo de quedarte con una mala inicialización.

**Posibles preguntas:**

- *¿Por qué 10 y no 100?*
  → Es un balance entre tiempo de cómputo y robustez. Para datasets simples 10 alcanza. Para datasets complejos podríamos subirlo a 50 o 100.

- *¿Qué diferencia hay entre `n_init=1` con `init="k-means++"` y `n_init=10` con `init="random"`?*
  → En la práctica suelen dar resultados similares. k-means++ con una sola inicialización inteligente compite bien con 10 inicializaciones aleatorias. Por eso por defecto sklearn usa k-means++ y `n_init=10`, doble seguridad.

---

## Gráfica 6: Método del codo

**Lo que muestra:** una curva descendente. La inercia parte muy alta (k=1, alrededor de 575.000) y baja muy rápido hasta k=6, donde se aplana drásticamente. La línea punteada roja marca k=6.

**Por qué se ve así:** la inercia mide qué tan compactos son los clusters. Con k=1, todos los puntos están en un solo grupo enorme y la inercia es alta. Cada vez que aumentamos k, dividimos un cluster en sub-clusters más compactos y la inercia baja. Pero llega un momento en que agregar más clusters ya no mejora significativamente la compactación: ese es el **codo**.

En nuestro caso el codo se ve clarísimo en k=6 porque ahí ya capturamos los 6 blobs reales. De k=6 en adelante, agregar más clusters solo significa partir un blob real en pedazos artificiales, lo que da una mejora marginal.

**Posibles preguntas:**

- *¿Por qué la inercia siempre baja al aumentar k?*
  → Porque con más centroides, las distancias promedio a cada centroide se reducen. El caso extremo es k = n (un centroide por cada punto), donde la inercia es 0.

- *¿Qué pasa si el codo no es claro?*
  → En datasets reales el codo a veces es ambiguo. Por eso complementamos con silhouette score y otros métodos.

- *¿Por qué el rango va de 1 a 20?*
  → Porque la consigna pide entre 1 y 20 centroides. 20 es el máximo que evaluamos.

---

## Gráfica 7: Silhouette Score vs Número de Clusters

**Lo que muestra:** una curva en forma de campana. Empieza en 0.50 con k=2, sube hasta el pico en k=6 (≈0.89), y después baja hasta estabilizarse alrededor de 0.32 para k≥12. La línea punteada roja confirma k=6 como el óptimo.

**Por qué se ve así:** el silhouette score mide qué tan bien está cada muestra dentro de su cluster vs qué tan cerca está de otros clusters. Va de -1 a 1:
- Cerca de 1 = muestra bien metida en su cluster
- Cerca de 0 = muestra en frontera entre clusters
- Cerca de -1 = muestra mal asignada

Con k=6 obtenemos 0.89 que es excelente (>0.7 ya es muy bueno). Con menos clusters (k=2, 3, 4, 5) forzamos a juntar blobs reales en uno solo, lo que mete puntos lejos de su centroide y baja el score. Con más clusters (k>6) partimos blobs reales en pedazos, los puntos quedan cerca de la frontera con su "hermano" y el score baja.

**Posibles preguntas:**

- *¿Por qué para k=12 en adelante el score se estabiliza en ~0.32?*
  → Porque ya partimos cada blob real en 2 sub-clusters artificiales. No importa si subes a k=15 o k=20, el comportamiento es similar: cada blob se subdivide.

- *¿Cuál métrica es más confiable, codo o silhouette?*
  → Silhouette es más confiable porque tiene un valor numérico interpretable (entre -1 y 1) y se puede comparar entre datasets. El codo es visual y a veces ambiguo. Pero ambas se usan complementariamente.

---

## Gráfica 8: Diagramas de silueta (k=5, 6, 7, 8) — IMPORTANTE

**Lo que muestra:** 4 sub-gráficos. En cada uno hay barras horizontales coloreadas, una por cluster, y una **línea roja punteada vertical** que marca el silhouette score promedio.

**Tu pregunta sobre la línea punteada y por qué algunos clusters quedan "atrás" de ella:**

La línea punteada roja representa el **silhouette score promedio del dataset entero** para ese valor de k. Las barras son los coeficientes de silueta individuales de cada muestra, ordenados de menor a mayor dentro de cada cluster.

**Cuándo una barra "supera" o "queda atrás" de la línea:**
- Si la mayoría de la barra está a la **derecha** de la línea roja → el cluster está bien formado, sus muestras tienen un score por encima del promedio.
- Si parte de la barra queda a la **izquierda** de la línea roja → esas muestras tienen score por debajo del promedio (están en zonas dudosas, cerca de fronteras con otros clusters).

**Análisis específico de tus 4 gráficos:**

**k=5:** 5 barras. La línea está en ~0.85. La barra del cluster 1 (rosa-naranja) tiene una "pendiente" larga que se extiende hacia la izquierda hasta casi 0.3. Esto indica que ese cluster tiene muestras dudosas. Pasa porque al usar solo 5 clusters, dos blobs reales se juntaron en uno y los puntos del medio quedan en frontera.

**k=6 (el óptimo):** 6 barras. La línea está en ~0.89. **Todas las barras están casi completamente a la derecha de la línea roja**, son largas, gruesas y parejas. Esta uniformidad es la firma visual de un buen clustering. Cada blob real tiene su propio cluster y todos los puntos están bien metidos en él.

**k=7:** 7 barras. La línea está en ~0.78. Aquí ya empezamos a ver problemas: el cluster 3 (amarillo claro) y el cluster 6 (azul) son **mucho más cortos y delgados** que el resto. Eso significa que tienen pocas muestras y muchas con score bajo. Lo que pasa es que un blob real fue partido en dos sub-clusters artificialmente, y uno de los pedazos tiene pocos puntos, todos cerca de la frontera con su "hermano".

**k=8:** 8 barras. La línea está en ~0.72. **Tres clusters quedan claramente atrás de la línea roja** (los clusters 5, 6 y 7 al fondo). Son muy pequeños y con scores bajos. Esto confirma que k=8 está sobreestimando el número real de grupos: estamos partiendo blobs reales en 3 pedazos y los pedazos chicos quedan dudosos.

**Posibles preguntas sobre esta gráfica:**

- *¿Por qué en k=6 todas las barras pasan la línea pero en k=8 algunas no?*
  → Porque k=6 es el k correcto: cada cluster captura un blob completo y todos los puntos están bien dentro de su grupo. En k=8 estamos forzando subdivisiones que generan clusters "huérfanos" con puntos en zonas grises.

- *¿Qué significa que una barra sea muy delgada/corta?*
  → Que ese cluster tiene pocas muestras. Eso por sí solo no es malo (un blob real puede tener menos puntos), pero combinado con un score bajo es señal de que el cluster es artificial.

- *¿Y si una barra tiene una "pendiente larga" hacia la izquierda como en k=5?*
  → Significa que dentro del cluster hay muestras con scores muy variados: algunas bien metidas y otras en frontera. Es un cluster mezclado.

- *¿Cómo decides el k óptimo solo viendo este gráfico?*
  → El k óptimo es aquel donde:
  1. Todas las barras superan claramente la línea promedio.
  2. Las barras son de tamaño similar (clusters balanceados).
  3. El promedio (la línea roja) está lo más a la derecha posible.

  En nuestro caso, k=6 cumple las tres condiciones.

---

## Gráfica 9: 50 imágenes representativas de EMNIST

**Lo que muestra:** una grilla 5x10 con 50 caracteres manuscritos en blanco y negro. **Importante: las imágenes se ven rotadas/reflejadas** (los caracteres parecen estar "acostados").

**Por qué se ven rotadas:** EMNIST en OpenML viene con las imágenes **transpuestas** respecto al formato estándar de visualización. Esto es un detalle conocido del dataset: para mostrarlas correctamente habría que aplicar `.T` (transpuesta) a cada imagen 28x28 antes de hacer `imshow`. **PERO esto NO afecta el funcionamiento del clustering ni del modelo**, porque a la red/al algoritmo solo le importan los patrones de píxeles, no la orientación visual. Si en la defensa te preguntan, podés responderlo así.

**Lo importante:** las 50 imágenes son los **caracteres más cercanos al centroide de cada uno de los 50 clusters** que armó K-Means. Cada una representa el "ejemplar prototípico" de su grupo.

**Posibles preguntas:**

- *¿Por qué se ven raros los caracteres?*
  → Porque EMNIST en OpenML está almacenado con las imágenes transpuestas (filas y columnas intercambiadas). No afecta al modelo, solo a la visualización. Si quisiera verlas bien, habría que hacer `.reshape(28,28).T` en lugar de `.reshape(28,28)`.

- *¿Por qué elegiste 50 clusters y no 47 (que es el número real de clases)?*
  → Porque el cuadernillo original usaba 50 con MNIST (que tiene 10 clases). La idea es elegir un k mayor o igual al número real de clases para asegurarte que cada clase tenga al menos un cluster representativo. También permite que clases con mucha variedad (como letras con varios estilos) tengan más de un representante.

- *¿Y si cambio k a 100?*
  → Podríamos esperar mejor accuracy porque tenemos más representantes por clase. Pero también significa etiquetar 100 imágenes a mano en lugar de 50. Es un trade-off.

**Para hacer la prueba:** cambiar `k = 50` por `k = 30` o `k = 100` y comparar el accuracy del modelo después.

---

## Gráfica 10: 50 muestras de menor confianza (active learning)

**Lo que muestra:** otra grilla 5x10 con 50 caracteres, pero estos son los que el modelo predijo con **menor probabilidad**. Se ven **más confusos**: trazos incompletos, formas ambiguas, caracteres que podrían ser dos letras distintas.

**Por qué se ven así:** son las muestras donde el modelo no está seguro. Algunas son muy similares a varias clases a la vez (por ejemplo una "O" podría confundirse con un "0", una "L" con una "1"). Otras son simplemente caracteres mal escritos o con poca tinta.

**Posibles preguntas:**

- *¿Cómo identificaste estas muestras como "de baja confianza"?*
  → Calculé `predict_proba` para cada muestra (devuelve la probabilidad de cada clase). Tomé el máximo (probabilidad de la clase predicha) y ordené ascendente. Las primeras 50 son las de menor confianza.

- *¿Por qué la primera muestra tiene confianza 0.99987 pero igual la consideras "de menor confianza"?*
  → Porque incluso esa "menor confianza" es alta en valor absoluto. Es relativa al resto del dataset. El modelo está bastante seguro de TODO porque está sobreajustado a las etiquetas propagadas. Lo que importa es que estas son las MENOS seguras dentro de las primeras 1000 muestras.

- *¿Qué hacés con estas muestras después?*
  → Las "etiqueto manualmente" (en realidad uso las etiquetas reales que ya tengo) y reentreno el modelo. El accuracy sube de 26.01% a 27.38%.

---

# COMPARACIÓN DE RESULTADOS Y EXPLICACIÓN

## Por qué el accuracy con representativas (26.29%) es casi 2x mejor que con aleatorias (15.37%)

Con 50 muestras aleatorias, podrías tocar 50 muestras casi todas de las mismas pocas clases (de 47 que hay). Quedarías con la mitad de las clases sin representar. Por eso el modelo no puede generalizar.

Con 50 muestras representativas, cada una representa un cluster distinto, así que cubrimos mejor la variabilidad del dataset. El modelo tiene al menos un ejemplo de cada "tipo" de carácter.

## Por qué la propagación (26.01%) NO mejora respecto a las 50 representativas (26.29%)

Esto puede sorprender: ¿no debería ser mejor entrenar con 1000 muestras que con 50? La respuesta es **NO siempre**. La propagación introduce ruido: si un cluster contiene caracteres de varias clases (porque K-Means no es perfecto), todas las muestras del cluster reciben la etiqueta del representante, incluso las que en realidad son de otra clase.

En nuestro caso, propagar agregó ruido que canceló el beneficio de tener más datos. El accuracy quedó igual (incluso un poquito peor). Esto NO es un fallo: es la limitación natural de la propagación pura.

## Por qué el active learning (27.38%) sí mejora

Después de propagar, identificamos las 50 muestras donde el modelo está más inseguro. Esas son justamente las muestras que están en frontera entre clases o que tienen etiquetas mal propagadas. Al "etiquetarlas a mano" y corregirlas, eliminamos el ruido más dañino. Por eso el accuracy sube a 27.38%.

Si repitiéramos active learning varias iteraciones, iría mejorando progresivamente.

---

# COSAS IMPORTANTES QUE PODÉS CAMBIAR EN VIVO PARA DEMOSTRAR

1. **`n_centros` en el Punto 1** (de 6 a 3, 10, 15, 20):
   - Demuestra que el flujo se reajusta solo.
   - Ver cómo cambia el método del codo y el silhouette.

2. **`min_dist` en el generador** (de 5.0 a 2.0):
   - Los blobs se solapan.
   - El silhouette score baja drásticamente.
   - Demuestra cómo afecta la separación a la calidad del clustering.

3. **`n_init` en KMeans** (de 10 a 1):
   - Aumenta la variabilidad entre corridas.
   - Demuestra el rol de las inicializaciones múltiples.

4. **`init` en KMeans** (de "k-means++" a "random"):
   - Demuestra la importancia de la inicialización inteligente.

5. **`k` en el Punto 2** (de 50 a 30 o 100):
   - Cambia cuántas etiquetas hay que poner a mano.
   - Más k = más etiquetas, pero potencialmente mejor accuracy.

6. **Cantidad de muestras de active learning** (cambiar `sorted_ixs[:k]` a `sorted_ixs[:100]`):
   - Demuestra que más correcciones = mejor accuracy.

---

# WARNINGS QUE APARECIERON Y QUÉ SIGNIFICAN

1. **`UserWarning: The number of unique classes is greater than 50% of the number of samples`**
   → Porque con 50 muestras y 47 clases, casi cada muestra es de una clase distinta. Sklearn te avisa que esto es raro pero no es un error. En nuestro caso es esperado porque estamos haciendo semi-supervisado con muy pocas etiquetas.

2. **`UserWarning: X has feature names, but LogisticRegression was fitted without feature names`**
   → Es un warning cosmético: una parte del código pasa un DataFrame con nombres de columnas y otra parte un array de numpy sin nombres. No afecta el resultado.

3. **`ConvergenceWarning`** (puede aparecer):
   → Si aparece, significa que la regresión logística no terminó de converger en 5000 iteraciones. No es problema porque la solución parcial ya es razonable.

---

# RESUMEN DE 30 SEGUNDOS PARA LA DEFENSA

> "Para el Punto 1 modifiqué el generador para que cree centroides aleatorios entre 1 y 20 con distancia mínima entre ellos, así puedo verificar visualmente que K-Means recupera los grupos. Apliqué el método del codo y silhouette score, ambos coinciden en k=6 que es el k real generado, con un silhouette de 0.89.
>
> Para el Punto 2 usé EMNIST Balanced de OpenML, 131.600 imágenes de 28x28 píxeles en 47 clases. Apliqué K-Means con 50 clusters para encontrar imágenes representativas y entrené una regresión logística que dio 26% de accuracy con solo 50 etiquetas, contra 15% con 50 muestras aleatorias. Después propagué las etiquetas a 1000 muestras y apliqué active learning corrigiendo las 50 muestras de menor confianza, llegando a 27.38%. Esto demuestra que la calidad de las etiquetas pesa más que la cantidad."
