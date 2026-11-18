# Plan: modo educativo integrado en 12/12/12

Fecha: 2026-11-08. Estado: **plan, sin implementar**.
Sustituye al enfoque de `scenes/edu/` (escena independiente con geometría por código), que se aparta de lo que se pidió: un modo extra dentro del juego, con sus modelos, texturas y sonidos.

## 0. Qué se hizo mal antes

La lección 1 se construyó desde cero (cabaña con cajas, leña con cubos, chimenea con esferas). Eso no reutiliza nada del juego y no encaja con el resto. Lo que sí sirve de la versión anterior:

| Pieza | Destino |
|---|---|
| `scripts/edu/leccion_fuego_logica.gd` (regla de la lección) | se reutiliza tal cual |
| `scripts/edu/edu_i18n.gd` (textos y náhuatl en borrador) | se traslada a `resources/i18n/textos.csv` (ver §4) |
| `scripts/edu/edu_progreso.gd` (progreso local) | se reutiliza |
| `scripts/test/test_edu_leccion1.gd` | se adapta a la escena nueva |
| `scenes/edu/edu_main.tscn`, `scripts/edu/edu_main.gd`, `scripts/edu/leccion_fuego_3d.gd` | se retiran al terminar la integración |

## 1. Cuarto regalo en el menú principal

Estado actual (`scenes/menu/main_menu.tscn`, `scripts/menu/main_menu.gd`):
- Tres regalos: `RegaloMultijugador`, `RegaloJugar`, `RegaloOpciones`, con sus puntos `PuntoMultijugador`, `PuntoJugar`, `PuntoOpciones` en `Inicio/Regalos`.
- Cada regalo es un `MenuPresent` con una `action` (`&"multiplayer"`, `&"play"`, `&"options"`). El despacho está en `_on_present_selected`.
- El tren (`MenuTrain`) trae los regalos; `spot_*` son los sitios donde se paran.

Cambios propuestos:
1. Crear `scenes/menu/present_educativo.tscn` copiando `present_play.tscn` (misma estructura, otra `action = &"educativo"` y otra etiqueta `MENU_EDUCATIVO`).
2. En `main_menu.tscn`: añadir `RegaloEducativo` y `PuntoEducativo` en `Inicio/Regalos`, y `spot_educativo` / `present_educativo` en el `MainMenu` (`node_paths` del nodo raíz).
3. Recolocar los cuatro puntos en el arco, para que el tren los pare con separación suficiente. Posición propuesta: **Multijugador, Jugar, Educativo, Opciones** (el educativo entre Jugar y Opciones, para que quede a la vista sin ser el primero).
4. `main_menu.gd`: añadir `spot_educativo`, `present_educativo` y el caso `&"educativo"` en `_on_present_selected`, que llama a la escena de la lección.
5. Texto del regalo en el rótulo neón (`neon_label.gd`) con la clave nueva.

Riesgos:
- `tools/menu/construir_menu.gd` regenera el menú y **sobrescribe** las ediciones a mano (`ESTADO.md`). Hay que editar a mano o ajustar ese script primero.
- En VR el regalo se toca o se apunta con el rayo: el mismo `MenuPresent` lo cubre sin código nuevo.
- `GDD.md` dice "tres regalos" (decisión confirmada). Cambiarlo es una decisión de diseño: hay que anotar en el GDD la adición del cuarto regalo, según la regla del `CLAUDE.md`.

## 2. Flujo de la lección

- El regalo llama a `get_tree().change_scene_to_file("res://scenes/edu/leccion_1_cabana.tscn")`.
- Partida **sin red**: el modo educativo es un solo jugador. Se usa `NetManager.play_offline()` (ya existe) antes del cambio, para que los objetos con autoridad (chimenea, puerta) se comporten como host.
- Pausa y salida con el sistema existente (`pause_menu`), no con Esc propio.
- Al terminar, volver al menú principal (`change_scene_to_file("res://scenes/menu/main_menu.tscn")`).

## 3. Escenario: reutilizar la cabaña del juego

En lugar de geometría nueva:
- `scenes/world/cabin/cabin.tscn` (ya tiene estructura, puerta `Puerta`, ventanas, `Chimenea`, `MesaTrabajo`, luz, iluminación falsa).
- `scenes/interactables/fireplace.tscn` y su script `scripts/interactables/fireplace.gd` (combustible, nivel, medidor, llama `GPUParticles3D`, sonido de leña).
- `scenes/items/item_bucket_water.tscn` (cubo con agua) y `scenes/items/materials/log.tscn` (leños).
- `scenes/interactables/rules/christmas_tree.tscn` como decoración opcional.
- Jugador: `scenes/player/player.tscn` (rigs PC y VR ya existentes).
- Sonidos: `assets/audio/ambiente/fuego_crujiendo.mp3`, `luz_encender.mp3`, `pasos_madera.mp3`; `assets/audio/sfx/` para pasos.
- Texturas: `assets/textures/cabana/*` (madera, piedra de chimenea, techo) ya usadas por la cabaña.

Escena nueva `scenes/edu/leccion_1_cabana.tscn`: solo instancia la cabaña, el jugador y un `WorldEnvironment` de interior. **No** incluye `game.tscn`, ni ciclo día/noche, ni `GameState`, ni oleadas, ni Krampus.

## 4. Lógica de la lección sobre la chimenea existente

El problema de fondo: la chimenea del juego no sabe nada de la lección. Hay dos caras:

**a) Observar (sin tocar el juego principal):** `LeccionFuegoLogica` escucha las señales de `Fireplace` (`fuel_changed`, `level_changed`) y lee `is_burning()` y `fuel_ratio()`. Los avances (leña, tiempo con fuego) se reflejan en la lógica sin cambiar la chimenea.

**b) Apagar con agua:** el juego principal **no tiene** apagado con agua (no hay referencias a agua en `fireplace.gd`, `torch_behavior.gd` ni `consumable_behavior.gd`). Dos opciones:
- **Opción 1 (recomendada, no toca el juego principal):** una subclase `scripts/edu/fireplace_edu.gd` con `class_name FireplaceEdu extends Fireplace` que sobrescribe `interact(player)`: si el jugador lleva el cubo de agua, apaga el fuego y avisa a la lección. La escena de la lección usa esta subclase en lugar de la chimenea original.
- **Opción 2:** añadir el apagado con agua a `fireplace.gd` del juego principal (cambio pequeño, pero toca un archivo caliente y el comportamiento del juego normal). Solo si se decide que el agua forme parte del juego.

Recomendación: opción 1.

**c) Salir:** la puerta `Puerta` del cabin ya tiene `interact`. La lección sustituye ese paso por un `Area3D` en el umbral: al cruzarlo con la lección completa, cuenta como salida. Si no está completa, la puerta no se abre y el HUD muestra la pista.

## 5. Textos y náhuatl

- Pasar las claves `L1_*` y `MENU_EDUCATIVO` a `resources/i18n/textos.csv` (al final del fichero, como pide `Docs/PLAN_SEMANA_2.md`).
- Añadir la columna `nah` (borrador de §0.3 de `PLAN_MODO_EDUCATIVO.md`) y regenerar `textos.nah.translation`.
- Registrar el idioma nuevo en `project.godot` (lista de locales). **Archivo caliente**: requiere turno y cerrar el editor.
- Todo texto visible usa `tr()` (regla del proyecto); se acaba el `EduI18n` propio.

## 6. Archivos calientes que se tocarían

| Archivo | Cambio | Motivo |
|---|---|---|
| `scenes/menu/main_menu.tscn` | cuarto regalo y punto | necesario |
| `scripts/menu/main_menu.gd` | caso `&"educativo"` | necesario |
| `scenes/menu/present_educativo.tscn` (nuevo) | regalo | necesario |
| `resources/i18n/textos.csv` | claves nuevas | necesario |
| `project.godot` | locale `nah` | necesario |
| `Docs/GDD.md` | cuarto regalo | decisión de diseño |

`scripts/interactables/fireplace.gd` **no** se toca con la opción 1.

## 7. Calendario (hasta el 18 de noviembre; video el 11)

| Día | Trabajo |
|---|---|
| 8 nov (hoy) | Aprobar este plan. Cerrar el editor antes de tocar escenas del menú |
| 9 nov | Escena `leccion_1_cabana.tscn` con la cabaña, el jugador y la chimenea real. Subclase `FireplaceEdu`. Prueba de que el fuego arde y se puede alimentar |
| 10 nov | Regalo educativo, cambio de escena, lógica conectada, puerta como salida, agua. Grabar el video **el 10 por la tarde** |
| 11 nov | Entrega del video al DASC (límite) |
| 12–17 nov | L2 y L3, náhuatl revisado, pruebas en PC, Android y VR, guía docente |
| 18 nov | Exhibición (8–14 h) y jurado (16:30–19:00) |

El 11 de noviembre es el límite duro: el video se graba con la lección integrada, y si no está lista el 10 por la tarde, el video muestra lo que haya.

## 8. Pruebas

- `test_edu_leccion1.gd`: adaptar a la escena nueva (instanciar la lección, alimentar y apagar la chimenea real).
- Prueba del menú: el regalo educativo existe, tiene la acción `&"educativo"` y cambia de escena.
- `verificar.ps1` con el juego principal sin cambios de comportamiento (`test_menu`, `test_partida_completa`).
- Prueba manual en PC con `--xr-mode off`.

## 9. Decisiones que necesito

1. ¿Apruebas el cuarto regalo entre Jugar y Opciones?
2. ¿Apagar con agua por la subclase de la chimenea (opción 1) o como mecánica del juego principal (opción 2)?
3. ¿Retiramos la versión independiente (`scenes/edu/edu_main.tscn` y su 3D por código) cuando la integrada esté lista?

## Estado al cierre del 2026-11-08

Hecho y probado:
- Lección 1 dentro de la cabaña real (`scenes/edu/leccion_1_cabana.tscn`), con `FireplaceEdu` y la chimenea del juego sin cambios.
- Cuarto regalo Educativo en el menú (`e21ea00`), con clic real que abre la lección.
- Pausa: la lección y la chimenea se detienen (`7981cf5`).
- Flujo real con entradas de teclado y ratón: tronco a la chimenea, cubo al inventario, apagado, salida y pantalla final (`2a346c5`).
- Pruebas: `test_edu_leccion1` 30/30; `test_menu` OK.

Pendiente, en orden sugerido:
1. Revisión del náhuatl por una persona hablante (decide variante).
2. Lección 2 (puerta: visitantes conocidos y avisar a un adulto). Contenido sensible: revisar el guion antes de programarlo.
3. Lecciones 3 a 7 con el mismo patrón (objetivo, objetos del juego, comprobación, pista).
4. Textos: pasar `EduI18n` a `tr()` y `textos.csv` (sin columna náhuatl hasta tener revisión).
5. VR y Android: el apagado y la recogida dependen del ratón; hace falta control por puntero y botones táctiles.
6. Guía docente y hoja de inclusión.
7. Pulido: textos de pista más claros, mensajes del HUD y pruebas del flujo real como prueba automática.

Limitación conocida del juez: `observar` solo cuenta tiempo con fuego; no hay penalización por apagar sin agua salvo que la salida se bloquee.
