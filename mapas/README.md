# Mapas

## office_0

`office_0.pgm` y `office_0.yaml` son el mapa de `office_0`, hecho con el SLAM de Clearpath (`mapping.launch.py`) y guardado con `map_saver_cli`. La resolución es de 5 cm y el mapa mide unos 11 x 10 m, casi lo mismo que el mundo (11 x 9.75 m).

Para revisarlo rápido, abran `office_0_vs_sdf.png`: es el mapa con las paredes reales del mundo encima, en rojo. Si las líneas negras caen sobre las rojas, el mapa está bien. Las zonas grises son rincones a los que el robot no cabe.

Recorrimos el pasillo, la entrada de cada oficina y las dos salas de arriba. Primero fuimos mirando hacia un lado y después hacia el otro, porque el SLAM solo usa el lidar de adelante y no ve lo que queda atrás.

### Cosas que nos pasaron

- **El mapa salía corrido.** Las oficinas de abajo son casi iguales, y el SLAM confundía una con otra: al entrar a la tercera "saltaba" a la segunda, y el mapa quedaba comprimido y con paredes repetidas. Se arregló desactivando el cierre de lazo del SLAM (`do_loop_closing: false`). Aquí no hace falta, porque en simulación la odometría es casi exacta. Si les sale un mapa raro en otro mundo con habitaciones parecidas, probablemente es lo mismo.
- **`map_saver_cli` a veces fallaba** con `Failed to spin map subscription` cuando la simulación iba lenta. Se arregla agregando `-p save_map_timeout:=5.0` al comando del README:

  ```bash
  ros2 run nav2_map_server map_saver_cli -f mapas/office_0 \
    --ros-args -r map:=/do100_0000/map -p use_sim_time:=true -p save_map_timeout:=5.0
  ```

- **En máquina virtual** hay que hacer `export LIBGL_ALWAYS_SOFTWARE=1` antes de lanzar la simulación. Si no, la vista de Gazebo sale negra y el lidar devuelve puros 0.05 m.
