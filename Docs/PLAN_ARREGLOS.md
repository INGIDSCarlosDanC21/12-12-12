# Plan de arreglos definitivos (2026-11-03)

## Diagnóstico
**1. Crash al recargar la fogata con madera (CONFIRMADO, ya corregido).**
`godot.log`: `Invalid type in function '_behavior_of' ... (previously freed)` en `PCRig.is_holding_fire` (`pc_rig.gd:446`). `Fireplace.interact` consume la madera que llevas en la mano (`despawn`), pero `_held` seguía apuntando al nodo liberado, y `is_holding_fire` / `_fire_blocks_equip` lo pasaban tipado a `_behavior_of`. Lo llama cada fotograma el elfo (`elf.gd:42` -> `player.gd:83`), así que el error se repetía. Corrección aplicada: ambas usan `get_held()` (devuelve null si está liberado). Falta: limpiar `_held` en el momento de consumir y un test de regresión.

**2. Objetos que se quedan flotando (causa de diseño, NO resuelto).**
Hay dos sistemas y el segundo falla:
- Objeto equipado (hacha, antorcha...): cuelga de `viewmodel_hands`, ya pegado a la cámara (flujo G, test de posición OK).
- Objeto FÍSICO agarrado (troncos, tablones, cubos, cualquier cosa recogida con E/clic): `_update_hold` lo persigue con velocidad a 90 Hz y `_snap_held_to_hand` solo lo coloca si masa <= `heavy_mass` y a < 0.6 m; si es pesado, o el rayo/colisión lo frena, queda atrás o flotando al girar. Además mezcla `camera.global_transform` (sin interpolar) y `get_global_transform_interpolated()`. Ninguna prueba mira un fotograma real.
Solución definitiva: mientras se sujeta, el objeto deja de ser física libre. Se congela (`freeze`, kinematic, colisión desactivada o solo con mundo), se coloca como hijo visual del viewmodel/cámara con pose fija (como Minecraft) y solo al soltar/lanzar recupera la física con la velocidad de la cámara. Los pesados se arrastran con otra mecánica o no se levantan.

## Pendientes heredados
- Sin probar en ventana real ni con visor: modelos/texturas nuevos, linterna (pose), aura, puerta VR, audio a oído, pose de objetos en mano.
- Primitivas: oso, oso alfa, miniboss_gnome, gnomo minero, coro de fantasmas, cake, workbench, window, feeder, green_button, ganado, avatar, wrist_watch.
- Draw calls peor punto 148–160 (cabana_lenador).
- Antorcha tirada por cliente nace llena; linterna de bolsillo invisible en avatar remoto PC.
- Costes de armas de fuego propuestos por agente (no GDD); fantasma ya no es invulnerable (confirmar en contrato).
- Tripo T1–T8 aparcado por el usuario.

## Reparto
| # | Tarea | Agente | Motivo |
|---|-------|--------|--------|
| 1 | Test de regresión del crash (comer/gastar madera en fogata con el objeto agarrado, con elfo activo) + limpiar `_held` al consumir | Sonnet barato | pequeño, bien acotado |
| 2 | Agarre físico estilo Minecraft (congelar y fijar al viewmodel) PC, mando y avatar remoto | **Opus caro** | rediseño de `pc_rig.gd` + red + colisiones; debe verificarse con ventana real y capturas |
| 3 | Verificación visual real (capturas con dbg_shot: objeto en mano, girando la cámara, antorcha, linterna, aura, puerta, texturas) | Sonnet medio, tras 2 | solo observar y reportar |
| 4 | Auditoría de errores de script en una partida larga (log) y tests de tipo "objeto liberado" en el resto de rigs (`vr_rig.gd` usa la misma `_behavior_of`) | Sonnet barato | búsqueda sistemática |
| 5 | Primitivas restantes con modelos CC0 (cake, workbench, window, feeder, ganado...) | Sonnet barato | descargar y conectar |
| 6 | Draw calls cabana_lenador < 150 | Sonnet barato | fusión de mallas |
| 7 | Cierre: verificar, tests, LAN, commit, docs | yo | |

Orden: 1 y 4 ya en paralelo; 2 en solitario sobre `pc_rig.gd`; 3 después de 2; 5 y 6 en paralelo con 2 (no tocan `pc_rig.gd`).
