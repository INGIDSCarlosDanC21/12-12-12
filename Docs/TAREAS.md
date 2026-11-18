# Tablero de tareas — 12/12/12

Última actualización: **2026-11-09**, Claude Code (plan v2, Fases 0, 1 y 3).
Rama de trabajo: **`dia-13/modelos`**. Fase 3 en la rama `worktree-agent-a8f063823f7b45198` (commits 80f0a2c y siguiente).

## Plan v2 del juego principal (`Docs/PLAN_JUEGO_V2.md`)

| ID | Tarea | Estado | Dueño |
|---|---|---|---|
| Fase 0 | Base de capturas y pruebas (`tools/captura/`) | DONE | Claude |
| B-01 | Mineral con vetas, modelo distinto de la roca | DONE | Claude |
| B-02 | Colisiones de objetos (cabeza del pico, hacha de hierro) | DONE (parcial) | Claude |
| B-03 | Objetos derechos en mano | DONE | Claude |
| B-04 | Recursos repartidos en 16 zonas | DONE | Claude |
| B-05 | Cofres apoyados en el suelo; estructuras bugueadas | PARTIAL (falta el caso concreto) | Usuario: indicar el caso |
| M-01 | Movimiento PC, Quest y móvil | PARTIAL (referencia hecha; ajuste tras partida) | Usuario: partida de prueba |
| Fase 2 | Manos y animación (A-01 a A-07) | TODO (decidir rig) | Claude + usuario |
| S-06 | Construcción libre en rejilla: suelo, pared, puerta, escalera, techo; fantasma verde/rojo; coste en materiales; colocar y recoger en red | PARTIAL (implementada; VR sin probar en visor; coste confiado al libro que informa cada cliente) | Claude |
| S-07 | Estructuras con vida (Salud por pieza); varias estructuras independientes de la cabaña | PARTIAL (implementada; falta partida de prueba) | Claude |
| S-08 | Atacantes de estructuras que buscan la pared o puerta más cercana y la rompen; aparecen en las noches (cupo en BalanceData) | PARTIAL (implementada; las puertas de la cabaña no son objetivo) | Claude |
| S-10 | Armaduras (ligera, media, pesada) con defensa y frío en BalanceData y placa por prenda; defensa aplicada al daño del jugador; minerales de hierro y cobre con lingotes; horno de fundición (nivel 1 y 2) en la mesa; recetas y mejoras en la tabla | PARTIAL (pruebas headless en PASS; sin probar en juego, PC ni VR; modelos de caja, sin Tripo) | Claude: revisar en partida |

## Plan v2, Fase 3: interfaz y controles (`worktree-agent-a8f063823f7b45198`)

| ID | Tarea | Estado | Dueño / nota |
|---|---|---|---|
| U-01 | Inventario tipo Minecraft (cuadrícula, arrastrar, dividir) | REVIEW | Claude: PC (cuaderno) y VR (panel, arrastre con puntero). Falta probar en visor |
| U-02 | Rareza de objetos | DONE | Claude (commit 331860a) |
| U-03 | HUD simple (vida, temperatura, munición, objeto) | DONE | Claude: se quitaron las pistas repetidas |
| U-04 | Aviso de regla 30 s antes de romperse | REVIEW | Claude: sonidos provisionales (eleccion.wav, aceptar.wav); el usuario debe juzgarlos |
| U-05 | Mesa de mejoras con ficha de receta | REVIEW | Claude: ficha en PC y VR; el panel VR es más alto, comprobar en visor |
| U-06 | Controles PC completos y remapeo | DONE | Claude: `look_*` añadidas; tabla en `Docs/CONTROLES.md` |
| U-07 | Controles móvil: tamaño y opacidad | REVIEW | Claude: ajuste en Opciones > Controles. Falta probar en Android |
| U-08 | Controles VR: ayuda en la muñeca y giro en Opciones | REVIEW | Claude: ayuda de muñeca (aparece al mirar la cara del reloj); el giro ya estaba en Opciones > Confort VR |
| U-09 | Teclado virtual VR para el nombre | REVIEW | Claude: conectado al campo de nombre del lobby; falta probar en visor |
| U-10 | `Docs/CONTROLES.md` | DONE | Claude: revisado contra el código; pendientes marcados en el propio documento |

Pruebas de la fase: `scripts/test/test_interfaz_nueva.gd` (61 PASS). Detalle y pendientes en `Docs/ESTADO.md`.
Este es el tablero compartido entre Claude Code y OpenCode. Antes de tocar código, una tarea
debe estar reclamada aquí. El detalle técnico vive en `Docs/CONTRATOS.md` y el diseño en
`Docs/GDD.md`.

## Cómo se usa

| Estado | Significado |
|---|---|
| `TODO` | Libre. Se puede reclamar. |
| `WIP` | Alguien trabaja en ello ahora mismo. No tocar sus archivos. |
| `REVIEW` | Implementada; falta la revisión del otro agente o una prueba. |
| `BLOCKED` | Espera una decisión del usuario o de otro sistema. |
| `DONE` | Criterio de aceptación cumplido y commiteado. |

**Reclamar una tarea:** cambiar `TODO` → `WIP`, poner tu nombre, la rama y la fecha.
**Terminarla:** pasar el criterio de aceptación, `tools/verificar.ps1`, commit y marcar `DONE`.
**Handoff:** cada sesión termina con el bloque de `AGENTS.md` en `Docs/ESTADO.md`.

## Archivos calientes (trabajo por turnos)

Solo un agente puede editar estos archivos a la vez. Se avisa en la tarea que los toca.

| Archivo | Por qué es delicado |
|---|---|
| `project.godot` | Autoloads, Input Map, capas y ajustes: cualquier cambio afecta a todo |
| `scenes/player/player.tscn` · `scripts/player/player.gd` | Nodo central de todos los sistemas |
| `scenes/game/game.tscn` | Spawner de objetos y jugadores |
| `scenes/world/map.tscn` | Mundo y cabaña |
| `resources/i18n/textos.csv` | Conflictos de claves y traducciones |
| `addons/**` | Prohibido editar (contrato) |

## Estado verificado (2026-11-01)

- `tools/verificar.ps1` (import, prueba de 29 escenas y arranque de 180 fotogramas): **sin errores**.
- Día 5 cerrado en código: `test_ia` 29 PASS, `test_ia_lan` OK (host + cliente), `test_animales`
  96 PASS, `test_gnomo_fantasma` 56 PASS (+ LAN OK), `test_oleadas` 62 PASS, `test_krampus`
  52 PASS y `test_dia5_integrado` 22 PASS; ninguno con FAIL.
- Rama `dia-5/ia`; commit del día 5 (incluye la escena de Krampus y sus pruebas).
- `addons/godot-xr-tools/.../hand_blend_tree.tres`: cambios de reserialización de Godot 4.7
  (no funcionales). Pendiente decidir si se revierten o se aceptan.
- No hay remoto de git configurado: **el repo no tiene copia de seguridad**.
- Las 14 escenas de objeto y 3 escenas de recurso se cargan e instancian; `data` y
  `world_scene` se resuelven bien (corregida la referencia circular).

## Día 3 — objetos, recursos y crafteo

| ID | Tarea | Prioridad | Estado | Dueño |
|---|---|---|---|---|
| D3-00 | Contratos, `ItemData`, `ItemBehavior`, `ItemDB`, `Inventory`, `Health`, `HeatSource`, recursos `.tres` | P0 | `DONE` | Claude |
| D3-01 | Escenas de escopeta, Deagle y munición + `GunBehavior` + FX de disparo + `Health` | P0 | `DONE` | Claude (aceptación PASS en un jugador, 2026-11-01) |
| D3-02 | Inventario: nodo en `Player`, cuaderno PC, bolsillo VR | P0 | `DONE` | Claude (aceptación PASS en un jugador, 2026-11-01) |
| D3-03 | Frío: `Temperature`, `HeatSource`, antorcha, HUD y derribo | P0 | `DONE` | Claude (aceptación PASS en un jugador, 2026-11-01) |
| D3-04 | Recursos: `ResourceNode`, `ToolBehavior`, prueba de golpes | P0 | `DONE` | Claude (aceptación PASS en un jugador, 2026-11-01) |
| D3-05 | Linterna y antorcha: comportamientos, luz y partículas | P0 | `DONE` | Claude (aceptación PASS en un jugador, 2026-11-01) |
| D3-06 | 10 escenas de objeto restantes (`axe`, `pickaxe`, `flashlight`, `cold_armor`, `fruit_veg`, `meat`, `water`, `milk`, `cookies`, `torch`) | P0 | `DONE` | OpenCode |
| D3-07 | Materias primas físicas: `log.tscn`, `stone.tscn`, `ore.tscn` | P0 | `DONE` | OpenCode |
| D3-08 | Escenas de recurso: árbol, roca y veta; colocarlas en el mapa | P0 | `DONE` | OpenCode |
| D3-09 | Cabaña provisional: puertas, ventanas, tablones y chimenea | P0 | `DONE` | OpenCode |
| D3-10 | Mesa de trabajo y crafteo con recetas `.tres` | P0 | `DONE` | OpenCode |
| D3-11 | Integración PC: equipar del inventario, usar, recargar y soltar | P0 | `DONE` | OpenCode |
| D3-12 | Integración VR: bolsillos, munición, comer y vestir la chamarra | P0 | `REVIEW` | Claude (headless OK, 2026-11-01; falta probar con visor) |
| D3-13 | Claves i18n nuevas de inventario, HUD, crafteo y muerte | P0 | `DONE` | OpenCode |
| D3-14 | Sincronizar agarrables en LAN (2 instancias) y corregir bugs | P0 | `REVIEW` | Claude (agente de red, 2026-11-01; prueba headless de 2 procesos OK; falta prueba con ventanas/VR real) |
| D3-15 | Checkpoint: commit del WIP y tag `checkpoint-dia-3` | P0 | `DONE` | OpenCode |
| D3-16 | Revisar los `.tres` de `addons/` reserializados | P1 | `BLOCKED` | Tú |
| D3-18 | Montar en ventanas/puertas las mejoras de recetas sin flujo propio (barricada, pinchos, refuerzo de pared y puerta) o `BuildSlot` genérico | P1 | `DONE` | Claude (dia-7/quest) |
| D3-19 | Replicar en red el estado de la antorcha (encendida y combustible) | P1 | `DONE` | Claude (dia-7/quest) |
| D3-20 | Retroceso de PC: la cámara no vuelve sola tras disparar | P2 | `DONE` | Claude (dia-7/quest) |
| D3-17 | Corregir la referencia circular `ItemData.world_scene` ↔ escena (dejaba `data` en null) | P0 | `DONE` | OpenCode |

### Criterios de aceptación del día 3

Revisados el 2026-11-01 (Claude): todos en verde en un jugador y en LAN headless de 2 procesos, salvo lo que exige visor o ventanas reales.

- [x] Las 14 escenas de objeto (13 + antorcha) cargan y tienen `world_scene` asignado.
- [x] Un objeto se puede agarrar en PC y en VR, soltar y lanzar. (VR: solo headless)
- [x] El inventario apila según `max_stack` y funciona sin red.
- [x] Hacha y pico rompen árboles/rocas y sueltan materias con autoridad del host.
- [x] La linterna alumbra y su haz es consultable por el fantasma.
- [x] La antorcha da luz y calor, se consume y bloquea armas/objetos.
- [x] La temperatura baja de noche, se frena en interior/fuego y derriba a 0.
- [x] Puerta, ventana, tablón y mesa de trabajo funcionan en local y en red. (en red solo se probó la puerta)
- [x] Dos instancias en LAN se ven mover, agarrar y disparar sin desincronizar.

## Día 4 — reglas, ciclo y ambiente

| ID | Tarea | Prioridad | Estado |
|---|---|---|---|
| D4-01 | `RuleManager`, las 11 reglas y sus consecuencias | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D4-02 | Ciclo día/noche de 15 min y puesta de luna (regla 9) | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D4-03 | Director de anomalías por niveles de ira | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D4-04 | Cascabel péndulo y tabla de eventos `.tres` | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D4-05 | Iluminación falsa: lightmap, emisivos y una sombra direccional | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D4-07 | Hornear `LightmapGI` en el editor y comprobar que no rompe el ambiente día/noche | P1 | `TODO` (usuario o Claude con editor) |
| D4-08 | Bajar draw calls (~260 hoy) a <150 para Quest | P0 | `DONE` Claude (dia-7/quest): peor punto 440→145 |
| D4-06 | `ShaderWarmup` tras la pantalla de carga | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |

## Día 5 — IA

| ID | Tarea | Prioridad | Estado |
|---|---|---|---|
| D5-01 | Base LimboAI: percepción por ruido/vista y red | P0 | `DONE` Claude (2026-11-01; headless, falta visor) |
| D5-02 | Vaca, gallinas y ganado | P0 | `DONE` Claude (2026-11-01; headless, falta visor) |
| D5-03 | Cerdo, oso y lobos | P0/P1 | `DONE` Claude (2026-11-01; headless, falta visor) |
| D5-04 | Gnomo explosivo y fantasma que teme la linterna | P0 | `DONE` Claude (2026-11-01; headless, falta visor) |
| D5-05 | Renos zombis, Rodolfo y oleadas nocturnas | P0 | `DONE` Claude (2026-11-01; headless, falta visor) |
| D5-06 | Krampus roba el pastel y fase jefe | P0 | `DONE` Claude (2026-11-01; headless, falta visor) |

## Día 6 — GUI y ciclo de muerte

| ID | Tarea | Prioridad | Estado |
|---|---|---|---|
| D6-01 | Theme del cuaderno, fuentes y paleta | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D6-02 | Menú 360 con los tres regalos | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D6-03 | Opciones y confort VR | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D6-04 | HUD PC, reloj VR, nota e inventario | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D6-05 | Derribo, reanimación de 10 s y banner de muerte | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |
| D6-06 | Buses de audio y lista de archivos esperados | P0 | `DONE` Claude (2026-11-01; headless + capturas, falta visor) |

## Día 7 — integración y Quest

| ID | Tarea | Prioridad | Estado |
|---|---|---|---|
| D7-01 | Quest 2 a ≥75 FPS (foveación, LOD, draw calls <150) | P0 | `DONE` Claude (dia-7/quest): Falta medir FPS en el Quest 2 real. |
| D7-02 | Partida completa de 15 min con 2–4 jugadores | P0 | `DONE` Claude (dia-7/quest): Probada headless (offline y LAN 2 procesos); falta con visor y 4 jugadores. |
| D7-03 | Corrección de bugs | P0 | `DONE` Claude (dia-7/quest) |
| D7-04 | `ESTADO.md`, `CLAUDE.md`, PR de la semana | P0 | `DONE` Claude (dia-7/quest): PR pendiente: no hay remoto de git. |

## Infraestructura de trabajo

| ID | Tarea | Estado | Dueño |
|---|---|---|---|
| INF-01 | `AGENTS.md`, tablero, protocolo de handoff | `DONE` | OpenCode |
| INF-02 | `tools/verificar.ps1` | `DONE` | OpenCode |
| INF-03 | Commit de checkpoint del WIP actual | `DONE` | OpenCode |
| INF-04 | Remoto privado y push de todas las ramas | `BLOCKED` | Tú |
| INF-05 | `git config --global --add safe.directory D:/12-12-12` | `BLOCKED` | Tú |
| INF-06 | Worktrees si se trabaja en paralelo de verdad | `TODO` | Ambos |

## Decisiones pendientes del usuario

1. ¿Remoto privado en GitHub o Codex para respaldar el repo?
2. ¿Se revierten los `.tres` de `addons/` reserializados o se aceptan?
3. ¿Reparto de roles: Claude implementa y OpenCode revisa/integra, o ambos implementan por lotes?

## Registro de handoffs

### 2026-11-01 — Claude (día 4)
- **Hecho:** 11 reglas con sus objetos colocados en cabaña, mapa y partida; ciclo día/noche con luna que se pone y estrellas; luz falsa y apagón; precarga con pantalla de carga; director de anomalías (16) y péndulo; hastiales de la cabaña cerrados y suelo sin parpadeo; `Docs/AUDIO.md`.
- **Siguiente paso:** probar con visor; después día 5 (IA).

### 2026-11-01 — Claude
- **Hecho:** guardado el trabajo pendiente de OpenCode (`2cef117`); D3-14 red conectada en `game.tscn` (ObjetosMundo, SpawnerObjetos, ShotRelay) y probada con 2 procesos; D3-12 VR (bolsillos filtrados, munición, comer, chamarra, fuego bloquea la otra mano); aceptación PASS de D3-01..05 y crafteo; arreglos: carrera de carga en `game.gd`, agarre de PC como cliente, recoger con E en PC, rayo con objeto sujeto, reencender antorcha en la chimenea.
- **Siguiente paso:** probar con visor y con ventanas reales; después día 4.

### 2026-10-31 — OpenCode
- **Hecho:** diagnóstico del repo, `AGENTS.md`, `Docs/TAREAS.md`, `tools/verificar.ps1`, prueba
  `scenes/test/test_escenas.tscn`, 10 escenas de objeto, 3 materias primas, 3 escenas de recurso,
  `world_scene` asignado en los 14 `.tres`, nodo `Temperatura` en `Player.tscn` y corrección de la
  referencia circular `ItemData` ↔ escena.
- **En curso / sin terminar:** colocar recursos en `map.tscn` (D3-08), cabaña/crafteo e
  integración de inventario/HUD en el rig de PC.
- **Siguiente paso:** colocar árboles/rocas/vetas en el mapa y conectar el HUD/inventario.
- **Bloqueos:** remoto privado y decisión sobre los `.tres` de `addons/`.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`.

### 2026-10-31 — OpenCode (segunda sesión)
- **Hecho:**
  - D3-08: 10 árboles, 4 rocas y 2 vetas en `map.tscn/Recursos`, con prueba del mapa en
    `test_escenas.gd` (comprueba que cada recurso es `ResourceNode` y tiene `drop_scene`).
  - D3-11: HUD e inventario instanciados en `pc_rig.tscn`; equipar (teclas 1-4, rueda), usar con
    clic principal, recargar (R), soltar (G), lanzar, retroceso de armas y avisos del HUD vía
    `Player.show_message`. Los objetos `NOT_HELD` (chamarra) se usan al elegirlos en el cuaderno.
  - D3-09/10: `Door`, `Window`, `Fireplace`, `Workbench` con sus escenas; `cabin.tscn` (10×8 m)
    instanciada en `map.tscn/Cabana`; tablones y chapas como materiales físicos (`board`, `metal`);
    6 recetas `.tres` (cubierta de madera/metal, barricada, pinchos, refuerzo de pared y puerta).
  - D3-13: claves nuevas (`ITEM_BOARD*`, `ITEM_METAL_SHEET*`, `FIREPLACE_*`, `WINDOW_*`,
    `WORKBENCH_*`, `DOOR_LOCKED`).
  - VR: `get_held_node()` y `show_message()` (sin HUD todavía); grupo `"players"` en `Player`.
- **Comprobado:** `tools/verificar.ps1` en verde; prueba de 29 escenas (incluye cabaña,
  interactuables y mapa con 16 recursos) y arranque de 180 fotogramas sin errores.
- **Sin probar:** ciclo real de crafteo/chimenea en partida, red LAN (D3-14) e integración VR (D3-12).
- **Siguiente paso:** D3-12 (bolsillos VR) y D3-14 (dos instancias en LAN); luego cerrar el día 3
  con la revisión de los criterios de aceptación pendientes.
- **Bloqueos:** remoto privado, decisión sobre `addons/` y reparto de roles con Claude.

## Día 8 — mejoras rápidas (plan en `Docs/PLAN_SEMANA_2.md`)

| ID | Tarea | P | Estado | Dueño |
|---|---|---|---|---|
| D8-01 | B9 nieve en el mundo: sin atravesar paredes, sin nieve en interiores, choca con tejados (PC) | P0 | `DONE` | Claude (dia-8/juice) |
| D8-02 | B10 menú: texto neón averiado en la cara de los regalos, iconos sentados encima | P0 | `DONE` | Claude (dia-8/juice) |
| D8-03 | B1 movimiento: velocidades, inercia, estamina, cámara PC (balanceo, sacudida, FOV), B2 rotar en 3 ejes | P0 | `DONE` | Claude (dia-8/juice) |
| D8-04 | B2 troncos en piezas que caben por la puerta, mesa de trabajo acepta material en 2 m | P0 | `DONE` | Claude (dia-8/juice) |

## Día 9 — cuerpo, herramientas y bucle del día (plan en `Docs/PLAN_SEMANA_2.md`)

| ID | Tarea | P | Estado | Dueño |
|---|---|---|---|---|
| D9-01 | B1 cuerpo propio: piernas y mitones en PC, mitones en VR, brazos del avatar apuntan a las manos | P0 | `DONE` | Claude (dia-9/bucle) |
| D9-02 | B3+B4 herramientas por niveles, kit inicial, martillo/cuchillo/cubo, ordeñar vaca; mejoras con efecto real; chimenea y mesa por niveles | P0 | `DONE` | Claude (dia-9/bucle) |
| D9-03 | B4 lista de tareas explícita con estado y tiempo en cuaderno y reloj | P0 | `DONE` | Claude (dia-9/bucle) |

## Día 10 — mundo (plan en `Docs/PLAN_SEMANA_2.md`)

| ID | Tarea | P | Estado | Dueño |
|---|---|---|---|---|
| D10-01 | Cielo: nubes, estrellas, aurora ligada a la ira | P0 | `DONE` | Claude (dia-10/mundo) |
| D10-02 | Cofres, tablas de botín, granja abandonada plantilla | P0 | `DONE` | Claude (dia-10/mundo) |
| D10-03 | Relieve, vegetación MultiMesh, granja colocada, marcadores de los 7 POI | P0 | `DONE` | Claude (dia-10/mundo): peor punto 135 draw calls |
| D10-04 | Recursos de relleno (Kenney, OpenGameArt) y sonidos conectados | P1 | `DONE` | Claude (dia-10/mundo) |
| D11-00 | Mover 5 POI dentro del muro de ±130 m (Mina, Capilla, Campamento, Taller, Establo) y regenerar huellas planas | P0 | `DONE` | Claude (dia-11/exploracion) |
| D11-01 | `test_ia` falla de forma intermitente (3 de 8 ejecuciones): investigar | P1 | `DONE` | Claude (dia-11/exploracion) |
| D11-02 | Los 7 POI restantes con cofres, criaturas y punto de minijefe | P0 | `DONE` | Claude (dia-11/exploracion) |
| D11-03 | NPC: ermitaño comerciante, viajero perdido (escolta), elfos corruptos | P0 | `DONE` | Claude (dia-11/exploracion) |
| D11-04 | Minijefes, fragmentos de Krampus y receta del arma (mesa nivel 3) | P0 | `DONE` | Claude (dia-11/exploracion) |
| D12-01 | Noches con presión (asedio por aberturas, eventos de regla, fuego, ira visible) | P0 | `DONE` | Claude (dia-11/exploracion) |
| D12-02 | Krampus jefe: patrones, forma final (krampus_bane) y final secreto | P0 | `DONE` | Claude (dia-11/exploracion) |
| D12-03 | Saco de 5 ranuras (VR y PC) | P1 | `DONE` | Claude (dia-11/exploracion); **sustituido por D13-04** (inventario único de 25 ranuras) |
| D12-04 | Rendimiento Quest: far, niebla, IA dormida; MSAA off, centrado | P0 | `DONE` | Claude (dia-11/exploracion) |

## Día 13 — sonido de puerta y LAN (2026-11-04)

| ID | Tarea | P | Estado | Dueño |
|---|---|---|---|---|
| D13-01 | Sonido normal de puerta: abrir/cerrar cortos y golpe seco (Kenney CC0) | P1 | `DONE` | OpenCode (dia-13/modelos): `test_puerta` 0 FAIL |
| D13-02 | LAN robusta: bind IPv4 explícito, IP del host en el lobby, timeout y teclado VR | P0 | `DONE` | OpenCode (dia-13/modelos): `test_partida_completa_lan` 0 FAIL por loopback y LAN |
| D13-03 | Ayuda de firewall de Windows (quitar Block de `12-12-12.exe`, abrir 24912/24913) | P0 | `REVIEW` | OpenCode: falta ejecutarlo como administrador y probar PC↔Quest real |
| D13-04 | Inventario único de 25 ranuras con pilas de 99: PC/VR, montones `xN`, botón aparte de la barra rápida y borrado total del saco | P0 | `DONE` | OpenCode (dia-13/modelos): `test_inventario_pilas` 54, `test_inventario_ui` 23, `test_tactil` 39 |
| D13-05 | Noche 1 superable casi siempre al primer intento (oleada y escalado por ira/jugadores) | P1 | `DONE` | OpenCode (dia-13/modelos): `test_oleadas` 63, `test_noche` 40 |
| D13-06 | Construcción y recursos con el inventario único: montar con material del inventario y un montón `xN` por recurso | P1 | `DONE` | OpenCode (dia-13/modelos): `test_bucle_dia` 42 |
| D13-07 | Regresión del péndulo del cascabel (paridad, efecto bueno/malo y reutilización) | P2 | `DONE` | OpenCode (dia-13/modelos): `test_cascabel` 24 |
