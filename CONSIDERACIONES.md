# Consideraciones

- Para las iniciales solo agreguen su figura en `FIGURES` (`trajectory.py`) y córranla con `self_controller`. Si la figura gira mientras avanza, el robot se va a desviar un poco: gira como un 5 % menos de lo que se le pide porque las ruedas patinan. Es normal porque el control es de lazo abierto, y les sirve para el análisis. Las pruebas que hicimos están en `resultados/pruebas.md`.

- En `office_0`, el SLAM confundía las oficinas porque son casi iguales, y el mapa salía corrido. Si en `office_4` les sale algo raro, revisen `mapas/README.md`, ahí está lo que hicimos.

- Al guardar el mapa, agreguen `-p save_map_timeout:=5.0` al `map_saver_cli` del README, porque si no a veces falla.

- El SLAM solo usa el lidar de adelante, así que lo que queda atrás del robot no se mapea. Hay que girarlo o volver mirando hacia el otro lado.

- Para validar `office_0`, en `mapas/` está `office_0_vs_sdf.png` con el mapa encima de las paredes reales.

- Los videos piden tiempo real y en máquina virtual la simulación va lentísima, así que mejor graben en una PC con Ubuntu instalado. Si igual usan VirtualBox, antes de lanzar pongan `export LIBGL_ALWAYS_SOFTWARE=1`; si no, Gazebo se ve negro y el lidar no sirve.
