# Plan: lavado de cara del juego principal (juice, pulido visual y sonoro)

Fecha: 2026-11-08. Alcance: **no rehacer el juego**. Pulir lo que existe: retroalimentación, transiciones, iluminación, partículas, sonido de interfaz y consistencia visual. Plataformas: Quest 2, PC y Android.

## 1. Diagnóstico rápido (por lectura de código, no por partida)

| Zona | Estado | Pulido necesario |
|---|---|---|
| Menú 360 (`scenes/menu/main_menu.tscn`, `present*.tscn`) | Funcional, con tren y regalos | Transiciones entre páginas, retroalimentación al pasar el puntero, sonido ya existe (`aceptar.wav`) |
| Golpes a recursos (`scripts/world/resource_node.gd`) | Sacudida y caída implementadas | Partículas de astillas y polvo; número de daño flotante |
| Recogida de objetos (`scripts/game/world_items.gd`) | Funcional | Tween de "pop" y sonido de recogida diferenciado por tipo |
| Fuego (`scripts/interactables/fireplace.gd`) | Medidor, luz y partículas | Parpadeo de luz más orgánico, chispas al echar leña |
| Puerta (`scripts/interactables/door.gd`) | Sonidos CC0 ya conectados | Pequeño rebote al cerrar |
| HUD (`scripts/ui/hud_pc.gd`) | Funcional | Transiciones de mensajes (ya hay `_aparecer` en el modo educativo), iconos con brillo al cambiar |
| Nieve (`scenes/fx/nieve_mundo.tscn`) | Muy presente | Decisión: el usuario define si el juego principal sigue nevado |
| Iluminación | Ciclo día/noche, luna, aurora | Bloom suave, sombras de contacto, tono de color por hora |

## 2. Tareas (cada una con criterio de aceptación)

| ID | Tarea | Archivos | Aceptación |
|---|---|---|---|
| L-01 | Partículas de astillas al golpear árboles y rocas | `scripts/world/resource_node.gd`, nuevo `scenes/fx/astillas.tscn` | Se ve al golpear, sin errores en pruebas de recursos |
| L-02 | Número de daño o de recogida flotante | `scripts/ui/`, nuevo `scenes/ui/texto_flotante.tscn` | Aparece y se desvanece en 0,8 s |
| L-03 | Pop al recoger objetos y sonido distinto por categoría | `scripts/game/world_items.gd`, `scripts/player/pc_rig.gd` (acción `pickup`) | Un sonido por categoría; sin cambio de comportamiento |
| L-04 | Chispas al echar leña en el fuego | `scripts/interactables/fireplace.gd` (solo efecto visual) | Partículas al `_add_fuel`; sin cambio de combustible |
| L-05 | Bloom y tono de color más cálido de noche | `scenes/world/day_night.tscn`, `resources/world/` | Captura nocturna revisada |
| L-06 | Transición entre páginas del menú (fundido) | `scripts/menu/main_menu.gd`, `scenes/menu/main_menu.tscn` | Fundido de 0,2 s al abrir opciones o lobby |
| L-07 | Retroalimentación al pasar el puntero y al elegir regalo | `scripts/menu/present.gd` | Escala y sonido de clic ya existentes; ajustar tiempos |
| L-08 | Rebote de la puerta al cerrarse | `scripts/interactables/door.gd` | Animación corta sin cambiar el estado de la puerta |
| L-09 | Mezcla de audio: música de menú y ambiente por zona | `scripts/core/settings.gd` (buses), `assets/audio/` | Volúmenes en -18 dB para música |
| L-10 | Pruebas de regresión visual: capturas de menú, cabaña y noche | `tools/`, `scripts/test/test_gui.gd` | Capturas revisadas por el usuario |

Regla: cada efecto es **solo visual o sonoro**. No se toca lógica de partida, red ni combustible.

## 3. Orden

1. L-01, L-02, L-03: los que más se notan al jugar.
2. L-06, L-07: la primera impresión del menú.
3. L-04, L-08: detalles de la cabaña.
4. L-05, L-09: ambiente y mezcla.
5. L-10: revisión final por capturas.

## 4. Dependencias

- Plataformas: cada efecto se mide en Quest 2 (72 Hz) con el presupuesto de draw calls de `Docs/ESTADO.md`. Las partículas de astillas se limitan a 20 por golpe.
- Audio: los sonidos nuevos (pasos en arena, cigarras, fogón) vienen del tablero de audio. Si no están, se usan los existentes.
- Nieve: si el usuario decide quitarla del juego principal, L-05 y la iluminación se revisan.

## Estado (2026-11-08, noche)

| ID | Estado | Nota |
|---|---|---|
| L-01 | Hecho | Astillas al golpear (`efectos.gd`) |
| L-02 | Hecho | Número de botín al romper un recurso |
| L-03 | Hecho | Texto y sonido al recoger (`recoger.mp3`); sin sonido por categoría todavía |
| L-04 | Hecho | Chispas al echar leña |
| L-05 | Hecho | Bloom suave (`entorno.tres`); pendiente de captura nocturna en Quest |
| L-06 | Hecho | Fundido de páginas (PC); en VR no aplica |
| L-07 | Parcial | Ya existían escala y sonido al pasar el puntero; sin cambio nuevo |
| L-08 | Hecho | Rebote al cerrar la puerta |
| L-09 | Hecho | Música a -12 dB en el bus; reproductor del menú a 0 dB |
| L-10 | Parcial | Pruebas de regresión pasan; capturas y revisión visual pendientes |

Pruebas: `test_puerta`, `test_fogata_madera`, `test_bucle_dia`, `test_partida_completa`, `test_menu`, `test_gui`, `test_inventario_pilas`, `test_inventario_ui`, `test_movimiento`, `test_tactil`, `test_cuerpo` pasan. `verificar.ps1` solo marca el aviso antiguo de `rules_note.tscn`.
