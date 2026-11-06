# Pruebas del controlador (Integrante 1)

Todas las corridas se hicieron en la arena, con `workshop.launch.py` sin RViz, en una VM de VirtualBox con 6 vCPU. La simulación iba lenta, a un 10–15 % del tiempo real. Esto no cambia los resultados, porque el controlador trabaja con el tiempo simulado. Las gráficas son las que guarda el plotter, renombradas con el nombre de cada figura.

## Nivel 1: funciones, sin simulador

Comparamos `world_to_robot()` e `inverse_kinematics()` con valores calculados a mano. También pasamos el resultado por la cinemática directa de `plotter.py` y comprobamos que devuelve la misma velocidad de entrada. Todo salió bien.

| Función | Entrada | Esperado | Resultado |
|---|---|---|---|
| `inverse_kinematics` | `(0.2, 0, 0)` | `[4, 4, 4, 4]` | OK |
| `inverse_kinematics` | `(0, 0.2, 0)` | `[-4, 4, 4, -4]` | OK |
| `inverse_kinematics` | `(0.2, 0.2, 0)` | `[0, 8, 8, 0]` | OK |
| `inverse_kinematics` | `(0, 0, 0.5)` | `[-4.704, 4.704, -4.704, 4.704]` | OK |
| `world_to_robot` | θ = 90°, ẋ = 0.2 | `(0, -0.2, 0)` | OK |
| `world_to_robot` | θ = 90°, ẏ = 0.2 | `(0.2, 0, 0)` | OK |
| `world_to_robot` | θ = 180°, ẋ = 0.2 | `(-0.2, 0, 0)` | OK |

## Nivel 2: un movimiento a la vez

Leímos `/do100_0000/wheel_velocity_controller/commands` a mitad de cada corrida. El orden es `[fl, fr, rl, rr]`, en rad/s.

| Figura | Comandos esperados | Comandos leídos | RMS real | RMS odometría |
|---|---|---|---|---|
| `test_adelante` (1 m hacia +x) | `[4, 4, 4, 4]` | `[4, 4, 4, 4]` (35 de 35) | 0.018 m | 0.007 m |
| `test_lateral` (1 m a la izquierda) | `[-4, 4, 4, -4]` | `[-4, 4, 4, -4]` (26 de 26) | 0.024 m | 0.013 m |
| `test_giro` (360° en el sitio) | `[-4.704, 4.704, -4.704, 4.704]` | Iguales (39 de 39) | 0.000 m | 0.000 m |

Los signos y el orden de las cuatro ruedas coinciden con la matriz del README y con `wheel_controller.yaml`.

En `test_giro` la posición no cambia, así que el error es cero. Para comprobar el giro medimos la orientación real en otra corrida: el robot giró **341°** de los 360° pedidos. Le faltan unos 19°, casi un 5 %, por deslizamiento de las ruedas.

En `test_adelante.png` y `test_lateral.png` el panel de trayectoria sale angosto: una coordenada casi no cambia y el plotter usa la misma escala en los dos ejes.

## Nivel 3: figuras completas

| Figura | RMS real | Máx. real | RMS odometría | Comentario |
|---|---|---|---|---|
| `square` | 0.026 m | 0.037 m | 0.015 m | Sigue la forma sin problema |
| `square_rotating` | 0.225 m | 0.393 m | 0.013 m | El cuadrado real sale cada vez más girado |
| `comparacion` | 0.024 m | 0.043 m | 0.014 m | Línea, diagonal, arco, pausa, giro y avance con rotación |

En `square_rotating`, la odometría sigue el cuadrado deseado y la pose real no. Las ruedas reciben los valores correctos, pero el robot gira un poco menos de lo que se ordena, como vimos en `test_giro`. El controlador es de lazo abierto: usa el ángulo deseado y nunca la pose real, así que cada lado sale con un poco más de giro acumulado. La odometría solo integra las ruedas, por eso no detecta el deslizamiento. Es lo que advierte el README.

## Archivos

- `test_adelante.png`, `test_lateral.png`, `test_giro.png`
- `square.png`, `square_rotating.png`
- `comparacion.png`: apoyo para la comparación del Integrante 2
