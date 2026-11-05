1. Observa Neurito sin entrenar. ¿Por qué se atasca?

Sin entrenar, la red tiene pesos aleatorios, así que sus salidas no significan nada. La exactitud de prueba es de 27.7 %, casi igual a adivinar entre cuatro direcciones (25 %). En mis pruebas solo llegó a la comida en el 3 % de los retos. Se atasca porque la red pura siempre elige la dirección con mayor probabilidad. Si esa dirección es una pared, el movimiento no cambia su posición, y como la posición sigue igual, la red vuelve a elegir lo mismo una y otra vez hasta que se acaban los 45 movimientos. No recuerda lo que ya intentó, así que no puede corregirse.

2. Entrena 100, 200 y 400 épocas. Compara pérdida, precisión y movimientos.

En mis capturas del juego, a las 100 épocas la pérdida fue de 0.1224, la exactitud de prueba de 87.3 %, con 1 comida, 0 atascos y 14 paredes. A las 200 épocas la pérdida bajó a 0.0366 y la exactitud subió a 89.9 %, con 25 comidas, 7 atascos y 14 paredes. A las 400 épocas la pérdida fue de 0.0105 y la exactitud de 90.8 %, con 51 comidas, 11 atascos y 14 paredes. Los contadores son acumulados porque no los reinicié.

La pérdida baja mucho al principio, entre 100 y 200 épocas, y después casi se queda quieta. La gráfica de la ventana lo muestra: cae rápido y luego se aplana. La exactitud sube más despacio, de 87 % a 91 %. Los movimientos contra paredes se quedaron en 14 desde las 100 épocas, lo que significa que después la red casi no chocó con paredes. Los atascos sí siguieron subiendo (de 0 a 7 y luego a 11), así que los fallos que quedan no son por paredes sino porque a veces la red da vueltas sin llegar. Entre 200 y 400 épocas la mejora ya es pequeña.

Además medí con 716 retos de prueba con la red pura: a las 100 épocas completó el 51.3 %, a las 200 el 81.3 % y a las 400 el 82.7 %. Esto confirma que lo que más mejora el comportamiento ocurre en las primeras 200 épocas.

3. Compara red pura frente a navegación asistida. ¿Qué parte corresponde al modelo y cuál a las reglas?

Medí los dos modos en los mismos 716 retos. Sin entrenar, la red pura completó el 3.1 % y el modo asistido el 87.8 %. Con 100 épocas, la red pura completó el 51.3 % y el asistido el 100 %. Con 400 épocas, la red pura completó el 82.7 % y el asistido el 100 %.

La parte del modelo es decidir hacia dónde ir, y eso lo aprende: la red pura pasó de 3 % a 83 % gracias al entrenamiento. La parte de las reglas es no entrar a paredes y evitar casillas ya visitadas, y eso no es aprendizaje, lo puse yo en el código. Lo que más me sorprendió es que sin entrenar, el modo asistido ya completa el 87.8 %, o sea que casi todo ese resultado lo hacen las reglas y no la red. Por eso el modo asistido puede parecer muy bueno sin demostrar que la red aprendió. Para evaluar la red de verdad hay que mirar la red pura.

4. Cambia las 96 neuronas ocultas por 16, 32 o 128.

Con 400 épocas, con 16 neuronas la pérdida fue de 0.0342, la exactitud de 87.2 % y la red pura completó el 74.3 % de los retos. Con 32 neuronas, la pérdida fue de 0.0161, la exactitud de 89.2 % y completó el 78.4 %. Con 96 neuronas, la pérdida fue de 0.0107, la exactitud de 91.5 % y completó el 82.7 %. Con 128 neuronas, la pérdida fue de 0.0103, la exactitud de 90.8 % y completó el 85.3 %.

Con pocas neuronas la red aprende peor porque tiene menos capacidad. Al subir a 96 mejora bastante. De 96 a 128 casi no hay diferencia en la pérdida y la exactitud incluso baja un poco, aunque los retos completados suben. Probé con una sola semilla, así que esa diferencia es pequeña y no sacaría una conclusión fuerte. Mi conclusión es que 96 neuronas ya son suficientes para este problema.

5. Cambia la tasa de aprendizaje 0.45 y observa si converge.

Probé cinco valores con 400 épocas. Con 0.01, la pérdida a las 400 épocas seguía en 0.68, la exactitud fue de 73.9 % y la red pura completó solo el 20.4 %. Con 0.1, la pérdida fue de 0.149, la exactitud de 87.2 % y completó el 60.2 %. Con 0.45, la pérdida fue de 0.0107, la exactitud de 91.5 % y completó el 82.7 %. Con 1.5, la pérdida fue de 0.0016, la exactitud de 93.4 % y completó el 86.9 %. Con 5.0, la pérdida se quedó en 1.3955, la exactitud fue de 23.6 % y completó solo el 4.5 %.

Con una tasa muy pequeña sí baja la pérdida, pero tan despacio que a las 400 épocas la red todavía no sirve. Con 0.45 converge bien, y con 1.5 converge incluso más rápido, así que 0.45 no es la mejor, solo una buena. Con una tasa muy grande (5.0) no converge: la pérdida queda en 1.39, que es casi lo que da una red que adivina entre cuatro opciones, o sea que no aprendió nada porque los pasos eran tan grandes que se pasaba del mínimo.

6. Cambia de mapa. Los mapas forman parte del entrenamiento, pero las combinaciones de inicio y meta usadas para probar no se entrenaron.

Con la tecla N cambié de mapa y además medí cada uno por separado con 400 épocas. En el mapa 1 la exactitud fue de 92.4 % y la red pura completó el 85.7 %. En el mapa 2 fue de 90.0 % y completó el 78.5 %. En el mapa 3 fue de 92.2 % y completó el 84.3 %. En modo asistido completó el 100 % en los tres. Funciona parecido en los tres mapas, con el 2 un poco más difícil. Es normal porque los tres mapas sí se usaron para entrenar; lo que la red no vio fueron las combinaciones de posición inicial y meta de la prueba.

Para ver qué pasa con un mapa que no conoce, entrené solo con los mapas 1 y 2 y probé en el 3. La exactitud bajó a 62 % y la red pura completó solo el 22 % de los retos. Entonces la red no sabe resolver laberintos nuevos, solo recuerda los que ya vio, porque lo único que recibe del mapa es su número y no las paredes. Esto confirma la limitación que dice la guía.

7. Haz vibecoding para verificar cómo mejora o empeora Neurito al cambiar DFS por BFS y A*

Le pedí a Claude que cambiara el experto que genera las etiquetas y comparé las tres opciones con el mismo entrenamiento (400 épocas, 96 neuronas). Con BFS la ruta media fue de 6.0 pasos, la pérdida de 0.0107, la exactitud de 91.5 % y la red pura completó el 82.7 % de los retos. Con A* la ruta media también fue de 6.0 pasos, la pérdida de 0.0110, la exactitud de 92.6 % y completó el 86.3 %. Con DFS la ruta media fue de 13.5 pasos, la pérdida de 0.0012, la exactitud de 99.9 %, pero la red pura completó solo el 7.5 % y con asistencia el 44.8 %.

BFS y A* dan casi el mismo resultado porque los dos encuentran el camino más corto, así que las etiquetas son casi iguales. A* quedó un poco mejor, pero la diferencia es pequeña y puede ser por el desempate entre rutas del mismo largo. DFS empeora mucho a Neurito: su ruta no es la más corta y la red aprendió esas etiquetas casi perfecto, pero en el juego casi nunca llega a la comida. Creo que pasa porque el camino de DFS desde una casilla no coincide con el camino desde la siguiente, y al ir siguiéndolo paso a paso la red termina dando vueltas. La lección es que la red copia muy bien a su profesor, sea bueno o malo, y una exactitud altísima no garantiza que el comportamiento en el juego sea bueno.