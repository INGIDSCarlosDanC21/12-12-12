# Proyecto: 12/12/12

## Qué es
Videojuego de terror en realidad virtual y PC, exploración en mundo abierto (freeroam). El jugador cuida una cabaña siguiendo las reglas de una nota antigua mientras sobrevive a criaturas navideñas. Enemigo principal: Krampus.

Diseño completo en `Docs/GDD.md`. Léelo antes de implementar cualquier mecánica.

## Plataformas y objetivos técnicos
- VR y PC sin problemas de compatibilidad.
- Multijugador cross-plataforma: PCVR, PC y Meta Quest 2.
- Mínimo 75 FPS en dispositivos VR.
- MVP en 2 semanas: prioriza lo jugable sobre lo completo.

## Motor y stack
- Motor: **Godot 4.7.2 stable** (versión de Steam), renderer *Mobile*. Motivo: fácil, rápido y ligero.
- Lenguaje: **GDScript** por defecto.
- VR: OpenXR (PCVR con SteamVR o Meta Quest Link; Quest 2 nativo con el plugin OpenXR Vendors).
- Multijugador: solo LAN, hasta 4 jugadores, ENet con netfox.
- Idiomas: español e inglés; todo texto visible usa claves `tr()`.

## Estructura
- `Docs/GDD.md`: documento de diseño (fuente de verdad del diseño).
- `Docs/CONTRATOS.md`: contratos técnicos (fuente de verdad del código y las escenas).
- `Docs/ESTADO.md`: estado actual, último avance y próximo paso.
- `Docs/TAREAS.md`: tablero compartido de tareas, dueño, rama y criterio de aceptación.
- `AGENTS.md`: instrucciones equivalentes para OpenCode; ambas herramientas leen la misma fuente.
- `tools/verificar.ps1`: comprobación automática (import + arranque headless).
- `scenes/`, `scripts/`, `assets/` (carpetas propuestas para Godot; crear al iniciar el proyecto de motor).

## Reglas de trabajo
- Trabajar siempre en una rama nueva; no hacer cambios directos en `main`.
- Antes de empezar, mirar `Docs/TAREAS.md` y reclamar la tarea (`TODO` → `WIP`).
- Un solo agente escribe un archivo a la vez. En los archivos calientes
  (`project.godot`, `player.tscn`, `player.gd`, `game.tscn`, `map.tscn`, `textos.csv`)
  se trabaja por turnos y se avisa en el tablero.
- Cerrar el editor de Godot antes de editar un `.tscn`: si la escena está abierta, al
  guardar pisa los cambios del agente.
- No editar `addons/`. No borrar código ni assets sin confirmación; marcar como obsoleto si hace falta.
- No cambiar mecánicas definidas en el GDD ni `Docs/CONTRATOS.md` sin confirmarlo primero.
- Verificar con `tools/verificar.ps1` antes de cada commit.
- Al terminar una sesión, actualizar `Docs/TAREAS.md` y `Docs/ESTADO.md` (con el bloque de
  handoff de `AGENTS.md`) y hacer commit.

## Decisiones ya tomadas
- Inventario de 13 objetos y lista de animales y enemigos según `Docs/GDD.md`.
- Menú de inicio con tres regalos: Multijugador (izquierda), Jugar (centro), Opciones (derecha).
- La regla 10 se implementa como "No abras la puerta si alguien la toca".
- Motor: Godot 4 con GDScript.

## Entorno instalado (verificado el 2026-10-31)
- Godot: `C:\Program Files (x86)\Steam\steamapps\common\Godot Engine\godot.windows.opt.tools.64.exe` (4.7.2.stable.steam, modo autocontenido: datos en `editor_data\`).
- Plantillas de exportación 4.7.2.stable: en `editor_data\export_templates\`.
- Blender 5.2.3 LTS: `C:\Program Files (x86)\Steam\steamapps\common\Blender\blender.exe`.
- JDK 21 (`C:\Program Files\Java\jdk-21.0.11`) y Android SDK (build-tools 35.0.1 y 36.1.0, platform android-36), ya configurados en los ajustes del editor.
- SteamVR y Meta Quest Link para PCVR.
- Plugin OpenXR Vendors 5.1.0 (compatible con 4.6+) disponible en `C:\Users\mitte\ocean-vr\addons\godotopenxrvendors`.

## Cómo compilar / probar
- Verificación rápida (import + arranque headless, filtra errores): `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`.
- Abrir en el editor: lanzar Godot desde Steam e importar `D:\12-12-12\project.godot`.
- Escena principal: `res://scenes/menu/main_menu.tscn` (menú 360 con tres regalos; el lobby LAN va embebido). Para probar la partida directo, abrir `res://scenes/game/game.tscn` y pulsar F6 (aparece un jugador sin red).
- Probar multijugador en un solo PC: Depurar > Ejecutar varias instancias > 2, una crea partida y la otra se une.
- Ejecutar en PC sin VR: `godot --xr-mode off --path D:/12-12-12` (OpenXR está activado en el proyecto; sin esa opción intenta abrir SteamVR).
- Ejecutar en PCVR: abrir SteamVR o Meta Quest Link y lanzar el proyecto normalmente.
- Comprobar errores sin ventana: `godot --headless --path D:/12-12-12 --import` y luego `godot --headless --xr-mode off --path D:/12-12-12 --quit-after 60`.
- Regenerar el mapa: ver `tools/blender/README.md` (Blender en modo consola con `generate_snow_map.py`).
- Antes de tocar escenas, scripts o recursos: cargar la skill `godot-12-scene-rules` y respetar `Docs/CONTRATOS.md`.
- Pruebas headless: `godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/<prueba>.gd` (partida completa: `test_partida_completa`; LAN: `test_partida_completa_lan` con `-- host` y, ~6 s después, el cliente).
- Exportar APK del Quest 2: `godot --headless --path D:/12-12-12 --install-android-build-template --export-debug "12/12/12" builds/quest/12-12-12.apk` (presets en `export_presets.cfg`; `builds/` no se versiona).
- Medir draw calls: `tools/perf/medir_draw_calls.gd` (resultado en `tools/logs/perf_draw_calls.txt`). Tras editar `scenes/world/fuentes_fusion/`, volver a ejecutar `tools/perf/fusionar_mallas.gd`.
