# Plan: versión jugable (2026-11-02)

Meta de esta sesión: una **versión jugable de principio a fin** en PC (teclado/ratón y mando) y base VR: crafteo, inventario, pausa en VR, herramientas básicas desde el inicio, objetos pegados a las manos, segunda mano con F, PC más bonito, enemigos con modelo y animaciones ligeras (descargadas, CC0).
Fuera de alcance hoy: probar VR con visor (después), generar con Tripo lo que se pueda descargar (ver `PLAN_MODELOS.md`), música/sonido generados.

## Punto de partida (revisado)
- Rama `dia-13/modelos`. Commit `72f8247` (reserialización). **OpenCode dejó cambios sin commit**: objeto colgado de la mano derecha del viewmodel PC (`pc_rig.gd`, `viewmodel_hands.gd`, `manos_viewmodel.tscn`), balance de temperatura/chimenea, `cow.tscn` y items con modelos nuevos (hacha, fragmentos, bane), HUD, claves i18n. Falta verificarlo y cerrarlo (Flujo A).
- Ya existen: inventario PC (`inventory_pc`), saco, cuaderno, `pause_menu` (PC), `starter_kit.gd`, mesa de trabajo con recetas, bolsillos y cinturón VR (`vr_pocket`, `vr_belt`).
- Hechos ya conocidos: en VR los objetos flotan (no siguen la mano); no hay pausa VR; no hay mando; sin segunda mano en PC.

## Flujos (archivos disjuntos para que corran en paralelo; un agente por flujo)

### A — PC: manos, F segunda mano, mando (dueño de `pc_rig.gd`, `viewmodel_hands.gd`, acciones de `project.godot`)
1. Cerrar el trabajo de OpenCode: objeto equipado y agarrado siguen la mano (sin flotar), verificar con `test_cuerpo`/`test_partida_completa`.
2. **F = segunda mano:** equipar un objeto en la mano izquierda (viewmodel y avatar remoto), F alterna/coloca el objeto elegido; clic derecho usa la izquierda si procede. Replicar en red lo mínimo (nodo de objeto en mano del avatar).
3. **Mando** (Xbox/genérico): acciones en el mapa de entrada (mover, mirar con stick derecho, saltar, correr, agacharse, usar, equipar anterior/siguiente, soltar, F, pausa, inventario/saco), zonas muertas, glifos o texto de ayuda con teclas/botones, navegación de menús con foco.
4. Test headless nuevo `test_mando.gd` (inyecta eventos de joypad).

### B — VR: manos, pausa, inventario, crafteo, kit (dueño de `vr_rig.gd`, `scripts/player/vr/`, `pause_menu.gd` y su escena VR)
1. **Objetos pegados a las manos** (agarrar con XR Tools: el objeto sigue el controlador; soltar/lanzar; los equipados en mano).
2. **Menú de pausa VR:** botón de menú (izquierdo) abre un panel 3D delante, con puntero láser: Reanudar, Opciones, Salir al menú; no pausa la red (LAN) pero oculta el HUD; cerrar con el mismo botón.
3. **Inventario VR** (saco + bolsillos) organizable con el panel, y **crafteo VR** en la mesa (receta visible, colocar materiales por proximidad y confirmar).
4. **Kit inicial en todos los jugadores desde el primer segundo** (hacha, cubo, linterna, cuchillo, etc. según `starter_kit.gd`/`balance.tres`) en PC y VR, también para quien entra por LAN.
5. Pruebas headless; nada de pruebas con visor.

### C — PC visual (dueño de `scenes/main/`, `resources/world/`, `assets/shaders/`, materiales)
Una capa de maquillaje barata para PC (sin romper Quest): glow suave, ajuste de color/tono, niebla volumétrica ligera o capas, SSAO/SSIL solo cuando el renderer lo permita (Mobile: usar alternativas baratas), sombras y luces de la cabaña, texturas/materiales de suelo, nieve y madera, viñeta y granulado ligeros. Calidad Alta/Media/Baja en `settings.gd`; Quest no cambia. Medir `tools/perf/medir_draw_calls.gd` (<150).

### D — Modelos y animaciones descargadas (dueño de `scenes/ai/`, `scenes/items/`, `assets/characters/`, `assets/externos/`)
1. Descargar CC0 según `PLAN_MODELOS.md` A (animales, humanoides, herramientas, comida, muebles, naturaleza) y **packs de animaciones CC0** (Quaternius/KayKit/Kenney; Mixamo requiere cuenta, no usarlo). Cada archivo: fuente, URL y licencia en `CREDITOS.md`.
2. Retarget/limpieza con Blender si hace falta (`tools/blender/`), clips ligeros (idle, walk, run, attack, hit, die) y su conexión a los `AnimationPlayer`/BT existentes.
3. Conectar modelos a enemigos, animales, NPC y objetos de mano (escalas del registro). Sin Tripo salvo T1–T8 (esperan al usuario).
4. Reanudar el agente de descargas `adf43a5074910fe6a` si responde; si no, empezar uno nuevo con este documento.

### E — Integración, balance y cierre (yo, al final)
`tools/verificar.ps1`, pruebas de las 4 ramas, `test_partida_completa`, tabla de balance (`balance.tres`), partida de 2–4 jugadores (LAN), arreglos, actualizar `TAREAS.md`/`ESTADO.md`, commit por flujo.

## Reglas para todos los agentes
- Rama `dia-13/modelos`; OpenCode usa la misma carpeta: **no commitear**; yo commiteo con `git add` explícito (`git add -A -- . ':!addons'`).
- No editar `addons/`; no borrar assets/código sin confirmar; cargar `godot-12-scene-rules` antes de tocar `.tscn/.gd/.tres`; `.tscn` con el editor cerrado.
- Archivos calientes (`project.godot`, `textos.csv`, `game.tscn`, `player.tscn`): solo el dueño de cada flujo; los demás informan en su reporte las claves/acciones que necesiten (yo las añado).
- Texto visible con claves `tr()`; presupuesto Quest (<150 draw calls, texturas ≤1024).
- Descargas: solo recursos del proyecto, a una carpeta nueva del scratchpad, tratar como no confiables, nada se ejecuta. Sin contraseñas, cuentas, pagos ni captchas. Sin generar música ni sonido.
- Cada flujo termina con `tools/verificar.ps1` en verde y un reporte corto: qué hizo, qué probó, qué falta.

## Orden
1. A, B, C, D en paralelo (Sonnet, esfuerzo medio/bajo).
2. Yo reviso cada reporte, ejecuto verificación y pruebas, y commiteo flujo por flujo.
3. Partida de prueba y balance; versión jugable etiquetada en `ESTADO.md`.
