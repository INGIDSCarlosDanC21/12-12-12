# Controles por plataforma

Tabla única de controles del juego principal (PC, PCVR/Quest 2 y móvil Android). Se revisó contra el código
el 2026-11-09 (ver la columna "Fuente" y la sección "Pendiente de verificar"). Las teclas de PC se pueden
cambiar en **Opciones > Controles**; el nombre de cada acción entre comillas invertidas es su identificador en
`InputRemap.ACTIONS` (`scripts/core/input_remap.gd`). La ayuda del cuaderno y el remapeo leen esa misma lista.

## PC (teclado y ratón; mando opcional)

| Acción | Identificador | Teclado / ratón | Mando | Fuente |
|---|---|---|---|---|
| Avanzar | `move_forward` | W | Stick izquierdo arriba | `pc_rig.gd` |
| Retroceder | `move_back` | S | Stick izquierdo abajo | `pc_rig.gd` |
| Izquierda | `move_left` | A | Stick izquierdo izquierda | `pc_rig.gd` |
| Derecha | `move_right` | D | Stick izquierdo derecha | `pc_rig.gd` |
| Mirar | Ratón (siempre). Stick derecho con mando: `look_left`, `look_right`, `look_up`, `look_down` | Ratón | Stick derecho | `pc_rig.gd` (líneas 1155-1156) |
| Saltar | `jump` | Espacio | A | `pc_rig.gd` |
| Correr | `sprint` | Mayús | L3 (pulsar stick izquierdo) | `project.godot` |
| Agacharse | `crouch` | Ctrl o C | B | `project.godot` |
| Interactuar (usar, abrir, hablar) | `interact` | E | X | `pc_rig.gd` (línea 1395) |
| Acción principal (agarrar, usar herramienta, atacar) | `primary` | Clic izquierdo | RT | `pc_rig.gd` |
| Acción secundaria (mano izquierda) | `secondary` | Clic derecho | LT | `pc_rig.gd` |
| Mano secundaria (segunda mano) | `offhand` | F | D-pad izquierda | `pc_rig.gd` |
| Rotar objeto en la mano | `rotate_held` | R | R3 (pulsar stick derecho) | `pc_rig.gd` |
| Recargar arma | `reload` | R | Back / View | `pc_rig.gd` |
| Soltar (una unidad; Mayús = pila entera) | `drop` | G | D-pad abajo | `inventory_pc.gd` |
| Linterna | `flashlight` | Q | D-pad derecha | `pc_rig.gd` |
| Inventario (cuaderno) | `inventory` | Tab | Y | `inventory_pc.gd` |
| Pausa / cerrar ventana | `pause` | Esc | Start | `hud_pc.gd` |
| Ranura 1 a 5 (barra rápida) | `slot_1`, `slot_2`, `slot_3`, `slot_4`, `slot_5` | 1 a 5 | — | `pc_rig.gd` |
| Ranura anterior | `slot_prev` | Rueda arriba | LB | `project.godot` |
| Ranura siguiente | `slot_next` | Rueda abajo | RB | `project.godot` |

Otras teclas fijas (no están en el remapeo):

| Acción | Teclado / ratón | Fuente |
|---|---|---|
| Mantener Alt: libera el cursor para pulsar la casilla del inventario | Alt | `hud_pc.gd` |
| Cuaderno: clic equipa, arrastrar mueve o fusiona, clic derecho divide la pila por la mitad | Ratón | `inventory_grid.gd`, `inventory_slot_view.gd` |
| Cuaderno: cerrar | Tab, Esc o X | `inventory_pc.gd` (`INV_HELP`) |
| Mesa de trabajo: cerrar | Esc | `crafting_ui_pc.gd` (`CRAFT_UI_HINT_PC`) |
| Menús: aceptar / cancelar | Enter o Espacio / Esc | `project.godot` (`ui_accept`, `ui_cancel`) |

Nota: `reload` y `rotate_held` comparten R por defecto. El juego decide según lo que lleve en la mano
(objeto agarrable: rota; arma: recarga). Si se quiere separar, se cambia en Opciones > Controles.

## Móvil (Android, sin visor)

| Control | Acción | Fuente |
|---|---|---|
| Joystick (abajo a la izquierda) | Moverse | `touch_stick.gd` |
| Arrastrar por cualquier zona libre | Mirar | `touch_controls.gd` (`_on_look_input`) |
| Correr | `sprint` | `scenes/ui/touch_controls.tscn` |
| Saltar | `jump` | idem |
| Inventario | `inventory` | idem |
| Interactuar | `interact` | idem |
| Atacar / usar herramienta | `primary` | idem |
| Pausa (arriba a la izquierda) | `pause` | idem |

Tamaño y opacidad de los botones: **Opciones > Controles > Botones táctiles** (de 70 % a 150 % de tamaño,
de 30 % a 100 % de opacidad). Se guardan en `user://interfaz.cfg` (`scripts/ui/ui_prefs.gd`).
Los botones se escalan desde la esquina que toca el pulgar: el joystick desde abajo a la izquierda, las
acciones desde abajo a la derecha y la pausa desde arriba a la izquierda.

## PCVR y Meta Quest 2

| Control | Acción | Fuente |
|---|---|---|
| Stick izquierdo | Moverse (movimiento directo) | `vr_rig.tscn` (`MovementDirect`) |
| Stick derecho | Girar: por pasos de 30° (por defecto) o suave | `vr_rig.tscn` (`MovementTurn`); Opciones > Confort VR |
| Botón A/X | Saltar (`jump_button_action = "ax_button"`, en XR Tools; qué mano, pendiente) | `vr_rig.tscn` |
| Botón X de la mano izquierda | Encender o apagar la linterna de bolsillo | `vr_rig.gd` (`_on_hand_button_pressed`) |
| Botón B / Y | Abrir o cerrar el inventario | `inventory_vr.gd` (`toggle_button = "by_button"`) |
| Botón de menú | Pausa | `pause_vr.gd` (`toggle_button = "menu_button"`) |
| Gatillo | Acción principal con el objeto de esa mano (herramientas, armas, comida); al estar derribado o como espectador, cambia de jugador (texto del HUD) | `vr_rig.gd`, `spectator.gd` |
| Botón de agarre (grip) | Agarrar objetos (pendiente de confirmar en Quest) | XR Tools (`grip_click`) |
| Teletransporte | Activo solo si se elige teletransporte en Opciones > Confort VR | `vr_rig.gd` (`function_teleport`) |
| Ayuda de controles en la muñeca | Mira la cara del reloj (izquierda): aparece encima la ayuda de cada mano. Ver texto en `VR_HELP_*` | `scripts/player/wrist_watch.gd` (`_update_help`) |
| Teclado virtual (nombre) | Al enfocar el campo de nombre del lobby en VR se abre el teclado (letras, acentos, Mayús, Borrar, Listo) | `scripts/menu/lobby_debug.gd` (`_on_name_focus_entered`), `scripts/ui/vr_keyboard.gd` |

## Pendiente de verificar

Estas filas vienen de los valores por defecto de XR Tools o de la escena del visor, y no se han probado con
el visor. Revisar en Quest 2 antes de darlas por buenas:

- VR: qué botón agarra y suelta cada objeto (`action_button` y `grip_action` de XR Tools), el botón de
  agacharse, el de correr y el de teletransporte.
- VR: la ayuda de la muñeca solo aparece si la cara del reloj mira a la cámara (umbral en `help_face_threshold`); comprobar en el visor el ángulo y el tamaño del texto.
- Móvil: tamaño de los botones en teléfono pequeño y en tableta (no probado). El ajuste de tamaño y opacidad se ve en Opciones > Controles.
- Mando de PC: los botones marcados como D-pad, LB/RB y L3/R3 no se han comprobado con un mando real.
