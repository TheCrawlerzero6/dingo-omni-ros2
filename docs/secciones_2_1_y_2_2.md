# 2.1 y 2.2: robot diferencial frente a omnidireccional

## 2.1 Diferencias

Un robot diferencial tiene dos ruedas motrices en el mismo eje, más una o dos ruedas locas de apoyo. Solo controla dos cosas: la velocidad de avance $v$ y la de giro $\omega$. Si $\omega_R$ y $\omega_L$ son las velocidades de las ruedas, $r$ su radio y $b$ la distancia entre ellas:

$$
v = \frac{r(\omega_R + \omega_L)}{2}, \qquad \omega = \frac{r(\omega_R - \omega_L)}{b}
$$

En el mundo, el robot se mueve así:

$$
\dot x = v\cos\theta, \qquad \dot y = v\sin\theta, \qquad \dot\theta = \omega
$$

Las ruedas no pueden deslizar de lado, y eso impone la restricción

$$
-\dot x\sin\theta + \dot y\cos\theta = 0
$$

Es una restricción no holonómica: limita la velocidad, pero no la posición a la que se puede llegar [1]. El robot llega a cualquier $(x, y, \theta)$, pero no en cualquier dirección. Para moverse de lado tiene que girar 90°, avanzar y volver a girar.

El Dingo-O que usamos tiene cuatro ruedas mecanum. Cada rueda lleva rodillos a 45°, así que al girar empuja en diagonal. Combinando el sentido de giro de las cuatro se consigue cualquier velocidad en el plano. El modelo inverso que implementamos en `self_controller.py` es el del README, el mismo que se deduce en [2]:

$$
\begin{bmatrix}\omega_{fl}\\ \omega_{fr}\\ \omega_{rl}\\ \omega_{rr}\end{bmatrix} = \frac{1}{r}\begin{bmatrix}1 & -1 & -(l_x+l_y)\\ 1 & 1 & (l_x+l_y)\\ 1 & 1 & -(l_x+l_y)\\ 1 & -1 & (l_x+l_y)\end{bmatrix}\begin{bmatrix}v_x\\ v_y\\ \omega\end{bmatrix}
$$

En el Dingo-O, $r = 0.05$ m y $l_x + l_y = 0.4704$ m. Antes de esa matriz, la velocidad deseada en el mundo se pasa al marco del robot rotándola por $-\theta$.

En resumen:

| | Diferencial | Omnidireccional (mecanum) |
|---|---|---|
| Velocidades que controla | $v$ y $\omega$ | $v_x$, $v_y$ y $\omega$, independientes |
| Restricción de velocidad | No holonómico | Ninguna en el plano: holonómico |
| Ruedas motrices | 2 | 4, cada una con su motor |
| Ir de lado | Girar, avanzar y volver a girar | Basta con $v_y$ |
| Orientación | Ligada a la dirección de avance | Independiente de la dirección de avance |
| Contacto con el suelo | Rueda maciza, sin deslizamiento lateral | Rodillos que ruedan de lado; hay más deslizamiento |

Clearpath vende el mismo robot en las dos versiones: el Dingo-D es diferencial y el Dingo-O es omnidireccional, con ruedas mecanum de 101.6 mm. El Dingo-O es más largo (686 mm contra 551 mm) y más pesado (17.5 kg contra 12 kg) [3].

## 2.2 Ventajas de cada uno

**Diferencial:**

- La mecánica y el control son más simples: dos motores y dos ecuaciones.
- Es más barato y fácil de mantener; las ruedas normales se gastan menos que los rodillos.
- Tiene mejor tracción y aguanta mejor suelos irregulares. Los rodillos mecanum necesitan un suelo plano.
- Su odometría suele ser más confiable, porque la rueda casi no desliza.

**Omnidireccional:**

- Se mueve en cualquier dirección sin girar antes, así que las trayectorias son más cortas y directas.
- La orientación es libre: puede avanzar mirando a otro lado, por ejemplo para mantener un sensor apuntando a algo.
- Maniobra mejor en pasillos y espacios estrechos, y se puede colocar junto a una mesa o estación sin hacer maniobras.
- Con las mismas ecuaciones del modelo inverso se pueden seguir figuras que un diferencial no puede.

## Lo que vimos en el simulador

- **`test_lateral`:** el robot se movió 1 m a la izquierda sin girar, con ruedas `[-4, 4, 4, -4]` rad/s y un error RMS de 0.024 m. Un diferencial no puede hacer ese movimiento.
- **`square_rotating`:** el robot recorre un cuadrado mientras gira. Traslación y giro son independientes, algo propio del omnidireccional.
- **Deslizamiento:** en `test_giro` pedimos 360° y el robot giró 341°. Por eso `square_rotating` acumula error: RMS real 0.225 m, contra 0.013 m de la odometría. La odometría de las ruedas no ve ese deslizamiento, lo que confirma la desventaja de la tabla.

Las gráficas y los datos están en `resultados/pruebas.md`.

## Fuentes

1. K. M. Lynch y F. C. Park, *Modern Robotics: Mechanics, Planning, and Control*, Cambridge University Press, 2017, cap. 13 "Wheeled Mobile Robots". https://modernrobotics.northwestern.edu/nu-gm-book-resource/13-1-wheeled-mobile-robots
2. H. Taheri, B. Qiao y N. Ghaeminezhad, "Kinematic Model of a Four Mecanum Wheeled Mobile Robot", *International Journal of Computer Applications*, vol. 113, n.º 3, pp. 6–9, 2015. DOI: 10.5120/19804-1586.
3. Clearpath Robotics, *Dingo User Manual*. https://docs.clearpathrobotics.com/docs_robots/indoor_robots/dingo/user_manual_dingo
