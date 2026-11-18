# Mapa en piezas editables

El terreno ya no es una sola pieza importada. `scenes/world/map.tscn` ahora está formado por nodos separados bajo `Terreno`, que puedes seleccionar, mover y editar en el editor de Godot.

## Estructura

```
Map
├── Terreno                  (Node3D, grupo navegacion_fuente)
│   ├── Chunk_0_0 … Chunk_3_3   16 chunks del suelo (assets/map/piezas/terreno/)
│   ├── Lago                    el agua (assets/map/piezas/lago.glb)
│   │   └── Lake_Water
│   ├── ColisionTerreno         colisión del suelo, sin malla visible
│   ├── ColisionLimites         muros invisibles del borde
│   └── MK_*                    marcadores (Marker3D) de aparición y puntos clave
├── Montanas                    anillo de montañas (sin cambios)
├── Vegetacion, Cabana, Recursos, Granja, Fauna, PuntosInteres …
```

## Cómo editar a mano

- **Mover o girar un chunk:** selecciona `Chunk_X_Y` y usa el gizmo. Si mueves un chunk, la colisión y la malla de navegación deben ir juntas: mueve también `ColisionTerreno` o rehornea la navegación.
- **Mover un marcador de aparición:** selecciona `MK_PlayerSpawn_N`, `MK_WaveSpawn_*`, `MK_KrampusSpawn`, etc. El código los busca por nombre bajo `Terreno`: **no cambies sus nombres**.
- **Cambiar el lago:** selecciona `Lago`. Su material está en `Lake_Water`.
- **Reubicar un punto de interés:** `PuntosInteres` contiene los POI como escenas.

Los nombres `Chunk_X_Y`, `Lake_Water` y `MK_*` son fijos: los usan `game.gd`, `wave_director.gd`, `krampus_director.gd`, `water_source.gd` y las pruebas.

## Qué se generó

`tools/blender/exportar_piezas_terreno.py` leyó `assets/map/terrain.glb` (sin regenerarlo) y exportó cada malla a su propio GLB:

| Archivo | Contenido |
|---|---|
| `assets/map/piezas/terreno/chunk_X_Y.glb` | 16 chunks del suelo |
| `assets/map/piezas/lago.glb` | agua |
| `assets/map/piezas/colision_terreno.glb` | colisión del suelo (`-colonly`) |
| `assets/map/piezas/colision_limites.glb` | colisión del borde (`-colonly`) |

Los marcadores `MK_*` no pasan por GLB: están escritos directamente en `map.tscn` con las mismas posiciones que tenían en el mapa original.

`assets/map/terrain.glb` se conserva como fuente. Ya no lo usa ninguna escena.

## Comprobación

- `scripts/test/test_mapa_piezas.gd`: 21 PASS.
- `test_partida_completa` 26 PASS, `test_bucle_dia` 42, `test_ia` 29.
- `test_muerte` tiene 1 FAIL, "el subtítulo lleva el nombre del jugador". Ya fallaba antes de este cambio, con el `map.tscn` de `HEAD`.
- `tools/verificar.ps1` en OK.

Lo que no se ha podido comprobar aquí: cómo se ve y se manipula el mapa en la ventana del editor. Conviene abrir `map.tscn` una vez y revisar que el suelo y el lago se ven igual.
