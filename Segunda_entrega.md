# Respuestas – Neurito 2.0: aprende a saltar

> Los valores salen de ejecutar el código del proyecto (`salto_modelo.py` y `salto_juego.py`) con las semillas por defecto: 5000 ejemplos de entrenamiento, 1500 de prueba y 300 obstáculos por prueba de juego. Si ejecutas el juego en Pygame los números pueden variar un poco, pero la tendencia es la misma.

## Experimento 1: probar el juego sin entrenar

Con los pesos aleatorios la red no sabe cuándo saltar. La precisión de prueba es de **44.9 %**, que es azar. En 300 obstáculos el resultado fue **0 esquivados y 300 choques**. La red nunca superó el umbral de 0.5 en el momento correcto, así que Neurito se queda en el suelo y choca con todo.

## Experimento 2: entrenar de cien en cien épocas

| Épocas | Pérdida | Precisión (prueba) | Resultado en el juego (300 obstáculos) |
|---|---|---|---|
| 1 | 0.7099 | 86.2 % | – |
| 100 | 0.2601 | 91.2 % | 273 esquivados, 27 choques |
| 200 | – | 91.9 % | 298 esquivados, 2 choques |
| 500 | 0.1368 | 94.1 % | 300 esquivados, 0 choques |
| 1000 | 0.0574 | 97.8 % | 300 esquivados, 0 choques |

La pérdida baja de forma constante y la precisión sube. En el juego, el comportamiento mejora desde "choca siempre" hasta "no choca". Con 100 épocas la precisión ya es de 91 % pero todavía hay choques, y con 500 épocas ya no hay ninguno.

## Experimento 3: cambiar el número de neuronas ocultas

| Ocultas | Pérdida (1000 épocas) | Precisión (prueba) | Juego |
|---|---|---|---|
| 3 | 0.2060 | 93.2 % | 300 esquivados, 0 choques |
| 12 | 0.0574 | 97.8 % | 300 esquivados, 0 choques |
| 24 | 0.0502 | 97.7 % | 300 esquivados, 0 choques |

Con **3 neuronas** la red tiene poca capacidad: la pérdida queda alta y la precisión es menor, porque le cuesta dibujar bien los dos bordes de la ventana de salto. Con **12** el resultado es muy bueno. Con **24** casi no mejora respecto a 12 (la precisión en prueba es igual) y se gasta más cómputo. La generalización (precisión en datos que no vio) no empeora con 24, pero tampoco gana: sobra capacidad para un problema tan sencillo.

## Experimento 4: cambiar la tasa de aprendizaje

| Tasa | Pérdida a 100 épocas | Pérdida a 1000 épocas | Precisión (prueba) | Juego |
|---|---|---|---|---|
| 0.01 | 0.6449 | 0.4302 | 86.5 % | 0 esquivados, 300 choques |
| 0.8 | 0.2601 | 0.0574 | 97.8 % | 300 esquivados, 0 choques |
| 1.2 | 0.2560 | 0.0392 | 98.3 % | 300 esquivados, 0 choques |

Con **0.01** los pasos son tan pequeños que en 1000 épocas la red apenas aprende: la pérdida sigue alta y en el juego Neurito sigue chocando. Con **0.8** converge bien. Con **1.2** converge un poco más rápido y en este caso no se volvió inestable, pero una tasa demasiado grande puede hacer que la pérdida salte o no baje. Una tasa pequeña converge lento, y una muy grande puede pasarse del mínimo.

## Experimento 5: cambiar la ventana de entrenamiento

Primero medí la física. Si Neurito salta cuando el tiempo estimado hasta el obstáculo (distancia / velocidad) es de unos 4 fotogramas o más, y menos de unos 15 fotogramas con el obstáculo lento, sobrevive. Con el obstáculo rápido, la zona segura llega hasta unos 22 fotogramas. La ventana original de **7 a 13** queda dentro de la zona segura para todas las velocidades.

| Ventana de etiqueta | Precisión (prueba) | Juego |
|---|---|---|
| 7 a 13 (original) | 97.8 % | 300 esquivados, 0 choques |
| 9 a 11 (más estrecha) | 96.3 % | 300 esquivados, 0 choques |
| 1 a 4 (demasiado tarde) | 97.9 % | 300 esquivados, 0 choques, pero en el borde de la física |
| 20 a 30 (demasiado pronto) | 98.5 % | **0 esquivados, 300 choques** |

La ventana de **20 a 30** es la que prueba el punto: la red la aprende con **98.5 % de precisión**, más que la original, y aun así Neurito no esquiva ningún obstáculo. Salta demasiado pronto, cae al suelo antes de llegar al obstáculo y choca. La red aprendió bien una regla que es incompatible con la física del salto.

## Pregunta central: ¿por qué una red puede clasificar ejemplos con buena precisión y aun así chocar?

Porque la precisión mide **qué tan bien copia las etiquetas**, no **si el juego se gana**. Hay varias razones:

1. **Las etiquetas son una regla elegida por nosotros**, no la física. Si la ventana está mal puesta (como en el experimento 5 con 20 a 30), la red puede acertar el 98 % de los ejemplos y chocar siempre.
2. **Los errores no pesan igual.** La precisión cuenta todos los ejemplos por igual, pero en el juego casi todos los fotogramas son "esperar" y fáciles. Solo importan unos pocos fotogramas críticos, cerca de los bordes de la ventana. Una red con 91 % de precisión puede equivocarse justo ahí, y un solo error en el momento exacto es un choque (con 100 épocas hubo 27 choques en 300 obstáculos).
3. **Los datos de prueba no son el juego.** Son ejemplos sintéticos con la mitad de saltos. En el juego los obstáculos llegan uno tras otro y la red decide en cada fotograma, así que un pequeño error se convierte en una acción real e irreversible.
4. **El resultado también depende de la física del salto**, que la red no ve: la gravedad, el impulso y el tamaño del obstáculo. La red solo ve tres números.

Por eso una precisión alta es necesaria, pero **no garantiza un buen resultado en el juego**.
