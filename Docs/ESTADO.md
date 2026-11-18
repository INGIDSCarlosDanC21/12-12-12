# Estado del proyecto

## Handoff de la sesión 2026-11-09 (rama dia-13/modelos, todo integrado)
- **Integrado en dia-13/modelos:** monedas y rarezas (S-01, U-02, S-14), muerte y derribado con límites (S-04, S-05), cofres de pago y estatuas de revivir (S-02, S-03), construcción libre y rompedores de estructuras (S-06 a S-08), animación procedural de manos y cuerpo (Fase 2 parcial), interfaz y controles (Fase 3), enemigos nuevos y modo difícil (S-09, S-12), reglas con consecuencias y boss en estatua con campana ritual (S-11, S-13), armaduras, minerales y horno (S-10), ambiente, Navidad, miedo y sonido (Fase 5 sin música), y lecciones de desastre D1 a D7 (contenido pendiente de revisión).
- **Pruebas (copia principal):** suite de un proceso 64 con código 0 (test_escenas se ejecuta como escena; test_construccion_lan y LAN aparte). LAN: partida, gnomo, IA, muerte y construcción en 0 FAIL. verificar.ps1 OK. Capturas de referencia 6 de 6.
- **Pruebas inestables corregidas:** test_enemigos_nuevos (persecución del merodeador: mira al jugador, espera la percepción) y test_economia_rareza (rareza de la campana).
- **Pendiente por falta de acceso o revisión del usuario:** pruebas en Quest 2 y Android físicos; música (Gemini, pistas en scenes/world/fx/ambiente_sonido.tscn); modelos de Tripo (navegador bloqueado); revisión de contenido de L2, L4, L6 y D1 a D7; juicio de sensación (animación, luz, sonido, controles); VR de cofres, construcción y estatuas sin probar en casco; LAN de armadura visible y de modo difícil para clientes que entran tarde.
- **Deuda conocida:** armadura y modo difícil no probados en partida real; el Krampus no aplica el multiplicador de modo difícil a sus puertas; la confianza en el coste de construcción depende del cliente; hay un stash con reserializaciones de Godot (ver `git stash list`).


## Handoff S-10 (armaduras, minerales y horno) — 2026-11-09, rama worktree-agent-a15dcb3f12b8de2b8
- **Hecho:** armaduras ligera, media y pesada (`resources/items/armor_*.tres`, `scenes/items/item_armor_*.tscn`) con defensa y resistencia al frío en `BalanceData` (`armor_defense`, `armor_cold_resistance`, `armor_tint`). Defensa aplicada en `scripts/combat/health.gd` (daño al jugador vía `ArmorBehavior.damage_taken`; el frío no). La prenda puesta va en la meta `worn_armor` y en `Player.armor_id`; la placa se pinta por prenda (`avatar.gd`). Minerales de hierro y cobre con lingote (`resources/items/{iron,copper}_{ore,ingot}.tres`), vetas `iron_vein.tscn` y `copper_vein.tscn` en `scenes/poi/mina.tscn`. Horno `scenes/interactables/furnace.tscn`, hijo de `workbench.tscn`: se levanta con receta (6 piedra + 2 tablones), funde con leña (30 s por tronco) y sube a nivel 2 (8 piedra + 2 tablones + 2 lingotes de cobre) para fundir hierro, más rápido (12 s → 6 s).
- **Rarezas:** ligera poco común, media rara, pesada épica; minerales y lingotes común.
- **Chamarra antigua:** no usa la resistencia de la tabla; conserva sus pérdidas de calor anteriores (sin cambio de balance).
- **Pruebas (todas PASS, 0 FAIL):** `test_armaduras_horno` 31, `test_crafteo_ui` 34, `test_frio_antorcha` 12, `test_bucle_dia` 42, `test_cuerpo` 23, `test_fase1_base` 51, `test_inventario_pilas` 54, `test_economia_rareza` 23, `test_muerte` 65, `test_muerte_objetos` 49, `test_partida_completa` 26.
- **Entorno:** los `.import` de `addons/` se habían quedado en `valid=false` por la primera importación del worktree; se restauraron desde HEAD y la importación queda limpia (0 errores).
- **Pendiente:** partida de prueba (PC y Quest) para juzgar la sensación del horno y de las armaduras; decidir si la pesada debe ser nivel 3 de mesa; modelos definitivos (hoy cajas).
- **Próximo:** el usuario juega una partida corta y ajusta valores en `resources/game/balance.tres` (todo está expuesto con `@export`).

## Handoff construcción libre (S-06, S-07, S-08) — 2026-11-09, rama worktree-agent-a797df42cf682eb67
- **Qué hay:** rejilla de piezas (suelo, pared, puerta, escalera, techo) con previsualización verde/roja, coste en materiales y vida por pieza (scripts/build/, scenes/build/). BuildSystem vive en game.tscn como Construccion. El host valida geometría, solape, terreno, sostén, alcance y el coste contra un libro de materiales por jugador. Atacantes de estructuras (StructureBreaker) aparecen en 
oche_1.tres y 
oche_final.tres, con cupo en BalanceData (uild_breaker_count, uild_breaker_final_count).
- **Controles:** PC, B modo; clic central o Enter colocan; clic lateral o Supr recogen; Y, U y RePág/AvPág. VR, mano derecha: A entra y coloca en el puntero, B sale. Sin probar en visor.
- **Confianza en el coste:** el libro lo informa cada cliente por RPC. El host no ve el inventario real, así que un cliente modificado puede declarar materiales falsos. Ver Docs/CONTRATOS.md, "Construcción", para el detalle y el límite.
- **Pruebas:** ver el informe de la entrega; 	est_construccion_lan requiere dos procesos.
- **Pendiente:** VR en visor; las puertas de la cabaña no son objetivo del atacante; sin animaciones ni modelos definitivos; decidir el tamaño de las piezas frente a BalanceData.
- **Próximo:** partida de prueba del usuario (PC, Quest, LAN) para juzgar colocación, sensación del fantasma y el cupo de atacantes.


## Handoff Fase 3 (rama `worktree-agent-a8f063823f7b45198`): interfaz y controles
### Handoff — 2026-11-09 — Claude Code (agente worktree)
- **Hecho:** U-01 cuadrícula de inventario en el cuaderno de PC (`scripts/ui/inventory_grid.gd`, `inventory_slot_view.gd`): arrastrar mueve o fusiona, clic derecho divide, clic elige; VR: el panel `inventory_panel.gd` permite arrastrar con el puntero (`drag_slot_to`). U-03 HUD sin pistas repetidas; munición `cargador / reserva`. U-04 aviso de 30 s (`RuleTaskList.DUE_SOON`, `rule_warning_banner.gd`) con cuenta atrás, sonido de entrada y tic en los últimos 10 s. U-05 ficha de receta en la mesa (`crafting_panel.gd`, `detail_text`). U-06 `look_*` en `InputRemap.ACTIONS`. U-07 tamaño y opacidad táctiles en Opciones > Controles (`ui_prefs.gd`). U-08 ayuda de controles en el reloj de muñeca (`wrist_watch.gd`, aparece al mirar la cara del reloj). U-09 teclado VR (`vr_keyboard.gd`) conectado al campo de nombre del lobby (`lobby_debug.gd`). U-10 `Docs/CONTROLES.md`.
- **Archivos tocados:** `scripts/ui/{inventory_grid,inventory_slot_view,rule_warning_banner,ui_prefs,vr_keyboard,hud_pc,inventory_pc,inventory_panel,crafting_panel,crafting_row,rule_task_list,touch_controls}.gd`, `scripts/player/wrist_watch.gd`, `scripts/menu/{lobby_debug,controls_remap}.gd`, `scripts/core/input_remap.gd`, escenas en `scenes/ui/` (inventory_pc, hud_pc, crafting_panel, inventory_slot_view, rule_warning_banner, vr_keyboard, vr_keyboard_key), `scenes/player/vr/wrist_watch.tscn`, `scenes/menu/controls_remap.tscn`, `resources/i18n/textos.csv` (filas nuevas), `Docs/CONTROLES.md` (nuevo), `scripts/test/test_interfaz_nueva.gd` (nuevo) y ajustes de `test_gui`, `test_inventario_pilas`, `test_mando`, `test_lista_tareas`.
- **Test_escenas:** como escena (`scenes/test/test_escenas.tscn`) pasa 30/30 en `main` y en esta rama. La variante `--script` no compila en `main` (`Identifier not found: NetManager`) y no termina; es previa a esta fase.
- **En curso / sin terminar:** nada en código. Quedan por verificar en el visor y en el móvil (ver abajo). Los sonidos del aviso son provisionales.
- **Siguiente paso:** partida de prueba en Quest 2 y en Android (U-01 arrastre, U-04 aviso, U-07 botones, U-08 ayuda de muñeca, U-09 teclado). Después decidir si la mesa VR necesita desplazamiento por arrastre (hoy se pagina con Anterior/Siguiente).
- **Bloqueos:** sin visor ni teléfono en este entorno. Los nombres de botón VR de XR Tools (agarre, salto) están por confirmar en Quest.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`; `godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/test_interfaz_nueva.gd`; las pruebas listadas en `Docs/TAREAS.md`.
## Handoff plan v2, Fase 0 y Fase 1 (rama `dia-13/modelos`)
- **Decisiones del usuario (2026-11-08):** el juego principal sigue nevado; orden Fase 1 y luego sistemas (Fase 4); seis rarezas tipo Fortnite; modo difícil por pastel aprobado; tiene Quest 2; aprueba el monstruo que abre la puerta y se lleva a un compañero; Tripo permitido para armaduras, bosses y horno, con preferencia por CC0 cuando existan.
- **Fase 0 hecha (`tools/captura/`):** `ejecutar_capturas.ps1` corre las 6 escenas de referencia (objetos, inventario en mano, viewmodel de cada herramienta, HUD, menú, mundo) sin errores. Secuencias a tiempo fijo con `--fixed-fps` + `--write-movie` (verificado). Detalle en `tools/captura/README.md`.
- **Fase 1:** B-01 mineral con vetas doradas (`resources/world/materials/mat_veta_mineral.tres`, asignado en `ore_vein.tscn`). B-02 colisión de cabeza en los dos picos y centrado del hacha de hierro (el modelo estaba 0.34 m a la izquierda). B-03 la pose en mano pasa de −35° a −6° y los recipientes/antorcha van derechos (`viewmodel_hands.gd`). B-04 56 recursos nuevos repartidos en 16 zonas (`tools/mundo/generar_recursos.gd` → `scenes/world/recursos/recursos_zonas.tscn`, instanciado en `map.tscn`). B-05 los 15 cofres de los POI están apoyados en el suelo real del mapa (sin defecto de altura; las "estructuras bugueadas" no se reproducen: falta el caso concreto). M-01 valores de referencia en `Docs/MOVIMIENTO_REFERENCIA.md`; el ajuste fino lo decide el usuario jugando.
- **Corrección colateral:** `present.gd` (salto de regalos del menú) fallaba con `Basis.slerp` por una base sin normalizar; ahora ortonormaliza antes de interpolar.
- **Pruebas:** `test_fase1_base` 51 PASS, `test_fase1_pois` 16 PASS, `test_escenas` OK (72 recursos), `test_mapa_piezas` 21, `test_edu_lecciones` 26, `test_edu_leccion1` 30, `verificar.ps1` solo con el aviso conocido de `PaginaViewport`.
- **Pendiente:** Quest 2 y PC, partida corta para juzgar M-01 y B-03 en sensación; el rendimiento de 56 recursos más (draw calls) hay que medirlo en Quest con `tools/perf/medir_draw_calls.gd`; la linterna en mano apunta hacia delante (no vertical) y se ve pequeña: revisar con el usuario; `dbg_shot.gd` sigue como diagnóstico temporal sin versionar.
- **Próximo:** Fase 2 (manos y animación A-01 a A-07), que necesita decidir el rig: clips CC0 de Quaternius/KayKit o modelos de Tripo aprobados.

## Handoff día 13 (rama `dia-13/modelos`): sonido de puerta y LAN
- **Puerta:** `door.tscn` usa ahora sonidos cortos CC0 de Kenney: abrir 0,92 s (`doorOpen_1`), cerrar 0,68 s (`doorClose_1`) y golpe/embestida 0,33 s (`impactWood_medium_000`). Los MP3 antiguos de 17 s siguen en disco pero ya no se referencian. `test_puerta` 0 FAIL. Créditos en `assets/externos/CREDITOS.md`; detalle en `Docs/AUDIO.md`.
- **LAN:** `NetManager.host()/join()` fijan `set_bind_ip("0.0.0.0")`; `local_ipv4()` nueva. El lobby enseña la IP del host mientras se hospeda, tiene timeout de conexión (10 s) y un teclado numérico en VR para escribir la IP (el teclado de Android no se ve dentro del visor). Nuevo `tools/lan_firewall.ps1` que quita las reglas Block de `12-12-12.exe` y abre TCP/UDP 24912 y UDP 24913.
- **Verificado:** `test_menu` (incluye teclado y `local_ipv4`), `test_partida_completa_lan` host+cliente por `127.0.0.1` y por `192.168.10.103` (0 FAIL), descubrimiento UDP OK, `verificar.ps1` OK.
- **Pendiente:** ejecutar `tools/lan_firewall.ps1` como administrador en el PC que hospeda; probar PC↔PC y PC↔Quest reales (la prueba headless es en el mismo PC); reconstruir e instalar el APK de Quest.

## Handoff día 12 (rama `dia-11/exploracion`, sin PR)
- Hecho: noches con presión (`siege_director.gd`: aberturas por puerta/ventana, eventos de regla, fuego apagado = refuerzos y frío, ira visible); Krampus jefe con 6 patrones + forma final (solo `krampus_bane`) + final secreto (`scenes/ui/ending`, `scripts/core/endings.gd`); saco de 5/8/12 ranuras (VR: botón B/Y y puntero; PC: tecla B); rendimiento Quest (far 95 m, niebla de profundidad 20–88 m, IA de animales dormida a >45 m, 72 Hz; MSAA apagado en Quest).
- Pruebas headless todas en verde (test_noche 40, test_krampus_jefe 90, test_saco 71, test_oleadas 62, test_partida_completa 26, etc.).
- Pendiente: probar en visor el saco (panel, B/Y, gesto de hombro), el reno por la ventana, cadenas de Krampus (equilibrio) y confirmar 72 Hz; sonidos (cadenas, cascos, cofres, vaca); montañas desaparecen en Quest por far 95; solo los renos usan las aberturas asignadas; no hay receta del saco; `dbg_shot.gd` es temporal y no se versiona.
- Próximo (día 13): modelos de Tripo, juice de golpes y audio que falta. Día 14: equilibrio, partida 2–4 jugadores, bugs, PR.

## Handoff día 11 (rama `dia-11/exploracion`)
- Hecho: 7 POI colocados dentro de ±115 m con cofres/fauna/minijefe (`tools/mundo/generar_pois.gd`; reejecutarlo pisa ediciones a mano); minijefes (gnomo minero, coro de fantasmas, muñeco de nieve, oso alfa jefe); fragmento de Krampus + arma `krampus_bane` (daño etiquetado, receta en la mesa nivel 3); NPC ermitaño (trueque), viajero (escolta) y elfos corruptos; `test_ia` estable.
- Pruebas: test_animales 96, test_minijefes 88, test_npc 31, test_ia, test_cofres, test_partida_completa 26, test_escenas 29/29, verificar.ps1 OK. Peor punto de draw calls: 136 (<150).
- Pendiente: bala/trampas/bengala no existen como ítem (no están en loot); `test_animales` "el cerdo muerde" es intermitente; sin sonido para cadenas/cascos/cofres/bramido de vaca; prueba en PCVR y LAN sin hacer; `snow_local.tscn` obsoleto.
- Próximo (día 12): noches con presión, jefe Krampus con forma final restringida a `krampus_bane` (cambio en health.gd) y final secreto.
## Fase actual
Semana 1, **día 7 (Quest, integración y bugs) cerrado en código** (rama `dia-7/quest`; antes, día 6 en `dia-6/gui`). Falta probar días 2–4 con visor y con ventanas reales en LAN.

## Hecho
- Diseño: `Docs/GDD.md` con decisiones confirmadas (LAN 4 jugadores, ES/EN, frío y fuego, anomalías, minijefe gnomo musculoso).
- Contratos técnicos: `Docs/CONTRATOS.md` y skill `.claude/skills/godot-12-scene-rules`.
- Entorno verificado: Godot 4.7.2 (Steam), Blender 5.2.3, JDK 21, Android SDK, SteamVR/Link (rutas en `CLAUDE.md`).
- Addons: XR Tools 4.5.2, OpenXR Vendors 5.1.0, netfox 1.35.3, LimboAI 1.8.1, ProtonScatter 4.2.0.
- `project.godot`: autoloads, Input Map (22 acciones), capas, Jolt a 90 Hz, renderer Mobile, OpenXR con foveación, traducciones, buses de audio.
- Autoloads: `EventBus`, `Settings`, `NetManager` (LAN, ENet, host/unirse), `GameState` (4 fases de 15 min e ira de Krampus; duraciones en `resources/game/match_config.tres`).
- Datos: 13 objetos y antorcha como `ItemData` en `resources/items/`; textos ES/EN en `resources/i18n/textos.csv`.
- Mapa: terreno de 400×400 m en 16 chunks, lago, granja, explanada, caminos, montañas, colisiones y 12 marcadores (`assets/map/`, generado por `tools/blender/generate_snow_map.py`).
- Shaders de terreno (splat de 3 capas), montañas (bruma) y agua (opaca); `scenes/world/map.tscn` con cielo nocturno, luna y niebla.
- Modelos del usuario movidos a `assets/characters/player/player.glb` y `assets/characters/krampus/krampus.glb`.
- Día 2: jugador híbrido. `Player` elige rig PC o VR; netfox sincroniza cuerpo, cabeza, manos, postura y ruido.
- Rig PC: ratón y teclado, correr, agacharse, saltar, interactuar (E), agarre físico con peso, rotar (R), lanzar, viewmodel en cámara aparte con nieve.
- Rig VR (XR Tools): manos, agarre, movimiento suave o teletransporte, giro por pasos o suave, correr, saltar, agacharse físico, viñeta.
- Lobby LAN provisional (escena de inicio): descubrimiento por broadcast, unirse por IP, máximo 4, jugar solo; en VR se une solo.
- Avatar remoto con el modelo del jugador y nombre; nieve local barata (1200 PC / 350 Quest).
- Probado con dos instancias en el mismo PC: el cliente ve moverse al host.

## Día 3 en curso
Commiteado en `9626806`:
- Contratos de objetos, inventario y mundo; `ItemData`, `ItemDB`, `ItemBehavior`, `Inventory`, `Health`, `HeatSource`, recursos `.tres` de los 14 objetos, claves i18n.

Sin commitear (sesión de Claude, verificado sin errores con Godot headless):
- Escopeta y Deagle con `GunBehavior`, cajas de munición, FX de disparo (`scenes/fx/`, `scripts/fx/`) y cambios en `Health`.
- Inventario: nodo `Inventario` en `Player`, UI de cuaderno para PC (`scenes/ui/`, `scripts/ui/`), bolsillo VR (`scenes/player/vr/vr_pocket.tscn`, `scripts/player/vr/vr_belt.gd`).
- Frío: `scripts/player/temperature.gd`, `scripts/world/heat_source.gd`, antorcha y linterna como comportamientos.
- Recursos: `scripts/world/resource_node.gd`, `ToolBehavior`, escena de prueba `scenes/combat/test/diana.tscn`.
- `NetPickable` ampliado y `WorldItems` con spawn/recogida en red.

Falta para cerrar el día 3:
- Cabaña provisional: puertas, ventanas, tablones, chimenea y mesa de trabajo.
- Crafteo con recetas `.tres`.
- Integración de inventario en PC (equipar/usar) y VR (bolsillos, comer, vestir chamarra).
- HUD de frío conectado al rig y fuentes de calor en el mundo.
- Prueba LAN de agarrables, disparos y recogida de munición.
- Checkpoint y limpieza de `addons/`.

### Añadido el 2026-10-31 (OpenCode)
- `AGENTS.md`, `Docs/TAREAS.md` y `tools/verificar.ps1` (import + 20 escenas + arranque).
- 10 escenas de objeto (`axe`, `pickaxe`, `flashlight`, `cold_armor`, `fruit_veg`, `meat`,
  `water`, `milk`, `cookies`, `torch`) con modelos provisionales, colisión, comportamiento y
  `RedSync`; `world_scene` asignado en los 14 `.tres`.
- Materias primas `log.tscn` (15 kg), `stone.tscn` (4 kg) y `ore.tscn` (6 kg).
- Escenas de recurso `tree.tscn`, `rock.tscn` y `ore_vein.tscn` con `ResourceNode`, tocón y drops.
- Nodo `Temperatura` añadido a `Player.tscn`.
- Corregida la referencia circular `ItemData.world_scene → escena → ItemData`, que dejaba
  `Comportamiento.data` en null: ahora la escena usa `item_id` y `ItemDB` resuelve el dato.
- Prueba `scenes/test/test_escenas.tscn`: 20/20 escenas cargan e instancian con datos válidos.

### Cierre del día 3 (2026-11-01, Claude)
- Red de objetos conectada en `game.tscn` (`ObjetosMundo`, `SpawnerObjetos`, `ShotRelay`): agarrar con traspaso de autoridad, aparecer/borrar, recoger sin duplicados, disparos y puertas probados con 2 procesos.
- VR: 4 bolsillos filtrados por `vr_pocket`, munición al inventario, comer acercando a la boca, chamarra al pecho, fuego en una mano bloquea la otra, nieve apagada en interior.
- Aceptación PASS en un jugador: armas, inventario (recoger con E en PC), frío, recursos, linterna, antorcha (se reenciende en la chimenea) y crafteo de tablón → ventana cubierta.

### Cierre del día 4 (2026-11-01, Claude)
- Reglas 1–11 (`scenes/rules/`, objetos en `scenes/interactables/rules/`) colocadas: pastel, botón verde, mesa de leche, 4 cuadros, árbol y caja de adornos en la cabaña; corral con comedero en `map.tscn/Granja`. Prueba integrada: las 11 reglas se registran, los eventos 6/7/8 se disparan y al anochecer se rompen las reglas que tocan.
- Ciclo día/noche (`scenes/world/day_night.tscn`): presets editables, crepúsculo de 20 s, luna en el shader del cielo que se pone al 75 % del día, estrellas, una sola luz direccional.
- Luz falsa: tarjetas de luz, ventanas que brillan de noche, `cabin_lights.gd` responde al apagón; `LightmapGI` configurado sin hornear.
- Precarga (`scenes/main/precarga.tscn`) tras pantalla de carga en PC y VR.
- Director de anomalías (16 anomalías por niveles de ira) y péndulo del cascabel con efectos buenos/malos; probado en LAN de 2 procesos.
- Cabaña: hastiales cerrados y suelo elevado 4 cm (parpadeo con el terreno). Debanding activado. Lista de sonidos en `Docs/AUDIO.md`.

### Día 5 — IA (2026-11-01, Claude, rama `dia-5/ia`)
- Base de IA con LimboAI (D5-01): `Creature`, percepción, aparición en red con `CreatureSpawner`, navegación horneada.
- Fauna (D5-02/03): vaca, gallina, cerdo, oso y lobos en `scenes/ai/animals/`, puestos en `map.tscn/Fauna` (16 animales al empezar); el ganado del corral usa el comedero.
- Enemigos (D5-04/05): gnomo explosivo, fantasma que teme la linterna, renos zombis y Rodolfo; `Oleadas` (`wave_director.tscn`) reparte `resources/waves/noche_1.tres` y `noche_final.tres`.
- Krampus (D5-06): `Krampus` (`krampus_director.tscn`) lo hace llegar en la noche final, roba el pastel y pasa a fase jefe. Mensajes con claves `KRAMPUS_*`.
- Integración: `game.tscn` instancia `Oleadas` y `Krampus`. Pruebas headless: `test_ia` 29, `test_ia_lan` OK (host + cliente), `test_animales` 96, `test_gnomo_fantasma` 56 (+ LAN OK), `test_oleadas` 62, `test_krampus` 52, `test_dia5_integrado` 22; todas sin FAIL. Falta probar con visor y con clientes reales para Krampus.

### Día 6 — GUI y ciclo de muerte (2026-11-01, Claude, rama `dia-6/gui`)
- Tema del cuaderno, fuentes y paleta (D6-01): `resources/ui/`, `scenes/ui/tema_*.tres`, `assets/fonts/`, `scripts/ui/ui_palette.gd`.
- Menú 360 (D6-02): `main_menu.tscn` con tres regalos (Multijugador, Jugar, Opciones), selector de idioma, tren y árbol; es la escena principal de `project.godot`. Lobby con paneles 2D (PC) y 3D (VR).
- Opciones y confort VR (D6-03): `Settings` ampliado (calidad, ángulo del giro, altura sentado); `vr_rig.gd` aplica `vr_snap_angle` a `movement_turn.step_turn_angle` y `vr_height_offset` a `player_body.player_height_offset` sin acumular.
- HUD de PC, reloj de muñeca VR, nota de reglas, pausa e inventario (D6-04): `scenes/ui/`, `scenes/player/vr/wrist_watch.tscn`, `scenes/interactables/rules_note.tscn`.
- Derribo, reanimación de 10 s, espectador, banner de muerte, minijefe gnomo musculoso y pantalla final (D6-05): `scripts/player/downed.gd`, `scripts/game/death_manager.gd`, `scenes/ai/miniboss/`, `scenes/ui/death/`; `game.tscn` instancia `Muerte`.
- Buses de audio y lista de sonidos esperados (D6-06): `Docs/AUDIO.md`.
- Pruebas headless sin FAIL: `test_gui` 57, `test_menu` (incluye Jugar -> `game.tscn` con `Muerte` y HUD), `test_muerte` 65, `test_muerte_lan` (host + cliente), `test_dia5_integrado` 22, `test_ia` 29, `tools/verificar.ps1` OK. Capturas en `tools/logs/` (`hud_*.png`, `menu_*.png`).

## Infraestructura de trabajo
- `AGENTS.md`: instrucciones para OpenCode (equivalente a `CLAUDE.md`).
- `Docs/TAREAS.md`: tablero compartido con dueño, rama, estado y criterio de aceptación.
- `tools/verificar.ps1`: import + arranque headless con filtro de errores.
- Regla de convivencia: un solo agente escribe un archivo a la vez y se cierra el editor antes de tocar un `.tscn`.

## Próximo paso
1. Día 7: integración y Quest (probar todo lo de VR con visor).
2. Probar con visor y con clientes reales (Krampus y oleadas).
3. Sustituir los modelos provisionales de IA por los de Tripo cuando estén.
4. Remoto de git y limpieza de `addons/`.

## Problemas abiertos
- Draw calls ~260 en la vista de aparición (objetivo Quest <150) (D4-08, día 7).
- `LightmapGI` sin hornear; puede que al hornear el terreno pierda el cambio de ambiente día/noche (D4-07).
- `GameState` solo descuenta el tiempo en el host; los clientes llevan su propio reloj desde `phase_changed` (bien mientras nadie entre a mitad de fase).
- Rule 4 cumplida emite `rule_event_ended(4, true)` sin evento previo; tenerlo en cuenta en el HUD.
- El estado de pastel, mesa, cuadros y árbol no se envía a quien entre a mitad de partida (no se permite entrar a mitad).
- Solo la ventana tiene flujo de mejora (tablón/chapa + E). Barricada, pinchos y refuerzos de pared/puerta se fabrican pero no se montan (D3-18); los `BuildSlot` de la cabaña son solo marcadores.
- La antorcha no replica en red su estado (encendida y combustible) (D3-19).
- El retroceso de PC no se recupera solo (D3-20).
- `consumed` no tiene receptor: no hay sistema de hambre ni sed.
- VR sin HUD: `show_message` y la escarcha de `freezing` aún no se ven.
- **No hay remoto de git**: el proyecto solo existe en `D:\12-12-12`.
- El checkpoint `8ca9075` está hecho en local; falta subirlo cuando haya remoto.
- `addons/godot-xr-tools/.../hand_blend_tree.tres` aparece modificado por reserialización del editor (no funcional). Pendiente decidir si se revierte.
- Probar en visor (PCVR y Quest 2): seguimiento, botones de Touch, 90 Hz, calibración de altura y FPS.
- La nieve del viewmodel de PC se dibuja sobre todo, también a través de paredes; apagarla dentro de la cabaña.
- Los objetos agarrables de prueba no se han probado aún en red de punta a punta.
- Cada avatar remoto son ~17k triángulos (sombrero y bufanda pesan 13.8k); reducir si Quest no llega a 75 FPS.
- Entre PCs distintos puede hacer falta permitir en el firewall de Windows UDP 24912 (ENet) y 24913 (descubrimiento).
- En Quest algunos dispositivos filtran el broadcast; respaldo: unirse por IP (no hay UI de IP en VR todavía).
- Ajuste visual del mapa: camino demasiado azul/saturado, borde dorado en la orilla del lago, manchas de roca planas, cielo sin estrellas ni disco de luna visible.
- Las texturas son de 2K; en Quest puede hacer falta bajarlas a 1K (se mide el día 7).
- Los modelos del jugador y Krampus no tienen animaciones (Mixamo pendiente) y deben reducirse de polígonos para Quest.
- El Android SDK vive dentro de `ocean-vr/.godot/`; si se limpia esa caché se pierde.
- Día 5: modelos provisionales con muchas draw calls (reno 7 mallas, Krampus ~27); Tripo está generando los definitivos (primero el reno).
- Día 5: los cuernos de Krampus atraviesan el dintel de la puerta.
- Día 5: si amanece mientras Krampus lleva el pastel, el pastel se queda robado.
- Día 5: la transformación del viewmodel de la linterna de PC puede quedar desfasada tras un teletransporte (la sonda del fantasma lo rodea).
- Día 5: nada se ha probado con visor ni con clientes reales para Krampus (solo headless y LAN de 2 procesos con la IA base y gnomo/fantasma).
- Día 5: la malla de navegación se hornea con la puerta cerrada; renos y Krampus cruzan el umbral de la cabaña en línea recta (`move_to(..., ignore_navigation := true)`). Si se rehornea con la puerta abierta, revisar esas rutas.
- Día 6: nada de VR probado con visor: desfase del reloj de muñeca, tamaño del texto de la nota en VR, paneles y anclajes de los menús VR, viñeta y espectador en VR.
- Día 6: el lobby VR no tiene teclado virtual.
- Día 6: en PC, E reanima y también interactúa (puede chocar si hay un interactuable junto al compañero caído). **Resuelto en día 7.**
- Día 6: quien se une tarde no recibe el estado derribado/muerto de jugadores existentes. **Resuelto en día 7** (y NetManager no admite uniones tras empezar).
- Día 6: `tools/menu/construir_menu.gd` sobrescribe las ediciones a mano de las escenas del menú y debe ejecutarse con ventana.
- Día 6: las capturas están en `tools/logs/` (`hud_*.png`, `menu_*.png`).

### Handoff — 2026-11-02 — Claude (versión jugable, parte 1)
- **Hecho:** F = segunda mano y mando (PC), objeto en la mano en PC, VR (agarre pegado a la mano, pausa 3D, mesa de crafteo, kit inicial), maquillaje PC por calidad, modelos CC0 con clips conectados. Plan en `Docs/PLAN_JUGABLE.md`. Commits 72c1a5e y 42f3d7d en `dia-13/modelos`.
- **Falta:** oso y jefe alfa (primitivas), vaca Tripo sin clips (hay vaca CC0 sin integrar), linterna sin modelo; draw calls 161 (meta 150); licencia de Holiday Bits; probar con visor todo lo VR; LAN 2–4 jugadores; balance final; capacidad inicial del saco.

### Handoff — 2026-11-02 — Claude (día 10)
- **Hecho:** cielo con nubes, estrellas y aurora (ira la vuelve magenta), cofres con rareza y tablas de botín que se rellenan cada día, granja abandonada (plantilla de POI, `scripts/poi/README.md`) colocada en (110, 85), relieve suave con huellas planas, vegetación en 88 MultiMesh con material de nieve (peor punto 135 draw calls), recursos de relleno en `assets/externos/` (créditos en `CREDITOS.md`, algunos no CC0) y sonidos conectados (talar, minar, impactos, mesa, ventanas, chimenea, armas, comer, viento).
- **En curso / sin terminar:** 5 POI quedan fuera del muro de ±130 m (D11-00). `test_ia` intermitente (D11-01). El granero sale azulado por la paleta de Kenney. Sin sonido: cadenas, pezuñas, cofre, aguijones de regla rota/amanecer/anochecer.
- **Siguiente paso:** día 11 (resto de POI, NPC, minijefes, fragmentos), empezando por D11-00.
- **Cómo verificar:** `tools/verificar.ps1`; pruebas `test_cielo`, `test_cofres`, `test_animales`, `test_partida_completa`; `tools/perf/medir_draw_calls.gd`. El generador de vegetación (`tools/mundo/generar_vegetacion.gd`) se ejecuta con ventana.

### Handoff — 2026-11-02 — Claude (día 9)
- **Hecho:** cuerpo propio (PC ve torso/piernas y mitones en viewmodel; VR mitones; IK de brazos en avatares VR remotos; objeto en mano en avatares PC), herramientas por nivel (piedra/hierro), martillo, cuchillo, cubo (agua y leche ordeñando), kit inicial, mejoras con efecto (barricada con vida, pinchos, refuerzos, trampa para osos), renos eligen la abertura más débil, chimenea niveles 1–4 y mesa 1–3, `resources/game/balance.tres`, lista de tareas viva en cuaderno y reloj con aviso de regla rota, SFX de Héctor (menú, muerte, latido, Krampus, gnomo, gallina, cerdo), Krampus animado (`krampus_animado.glb`, 11,8k tris, 12 clips; usa "walk"/"run"/"attack" si existen).
- **En curso / sin terminar:** mugido de la vaca sin conectar; `gnomo_movimiento` sin uso; el minijefe no reproduce su sonido de aparición; Krampus sin clips de caminar/atacar (se están añadiendo). No probado en LAN ni VR.
- **Siguiente paso:** día 10.
- **Cómo verificar:** `tools/verificar.ps1`; pruebas `test_bucle_dia`, `test_cuerpo`, `test_lista_tareas`, `test_krampus`, `test_partida_completa`.

### Handoff — 2026-11-02 — Claude (día 8)
- **Hecho:** nieve en el mundo (`scenes/fx/nieve_mundo.tscn`; se apaga en interiores, choca con tejados en PC), menú con neón averiado en los regalos e iconos sentados (`scripts/menu/neon_label.gd`), movimiento (andar 2,2 / correr 4,5 / agachado 1,2 m/s, inercia en PC, estamina con barra en HUD y reloj), cámara PC (balanceo con cadencia, inclinación, sacudida, hundimiento, FOV al correr; opción "Balanceo de cámara"), rotar el objeto agarrado en 3 ejes (R + ratón, rueda cambia eje), mesa de trabajo con radio de 2 m.
- **En curso / sin terminar:** nada. `scenes/player/fx/snow_local.tscn` y su script quedan sin uso (borrar solo con confirmación). Regenerar el menú con `construir_menu.gd` con el editor cerrado y comparar. La colisión de la nieve con el suelo y los tejados no se ha visto en el mapa real.
- **Siguiente paso:** día 9 según `Docs/PLAN_SEMANA_2.md` (cuerpo propio, herramientas, mejoras con efecto, chimenea por niveles).
- **Cómo verificar:** `tools/verificar.ps1`; pruebas `test_movimiento`, `test_troncos`, `test_menu`, `test_bugs_dia7`, `test_partida_completa`.

### Handoff — 2026-11-02 — Claude (día 7)
- **Hecho:** rendimiento Quest (peor punto 440→145 draw calls; fusión de mallas en árbol, roca, veta y cabaña; rangos de visibilidad; oclusores; MSAA 4x y ajustes solo Android) y `export_presets.cfg` (Quest 2 y Windows; el APK debug se exporta headless). Bugs: huecos de montaje (D3-18), antorcha en red (D3-19), retroceso PC (D3-20), E reanima con prioridad, estado de derribo al entrar, tarta devuelta al irse Krampus, linterna tras teletransporte, Krampus se agacha en la puerta. Modelos Tripo decimados (~5–6k tris, sin rig: animación procedural) en oso, lobo, gnomo, reno y Rudolph; cuerpo del jugador nuevo con mitones (`scenes/player/player_body.tscn`). Audio del equipo en `assets/audio/` (créditos en `CREDITOS.md`) conectado a puertas, chimenea, recursos, armas, jugador y partida. Iconos del menú de Nevid. Cuadros con las 17 fotos (`assets/textures/cuadros/`, se usan 4); al cambiar pasan a blanco y negro.
- **Archivos tocados:** `project.godot`, `export_presets.cfg`, `.gitignore`, `tools/perf/`, `tools/blender/personajes/`, `scenes/world/**`, `cabin.tscn`, `scenes/ai/**`, `scenes/crafting/build_slot.tscn`, `scripts/crafting/`, `scripts/player/` (pc_rig, downed, player_body, player_sounds), `scripts/items/behaviors/`, `scripts/ai/krampus/krampus.gd`, `scripts/interactables/`, `scenes/player/avatar/`, `scenes/menu/`, `game.tscn`/`game.gd`, `textos.csv`, `assets/{audio,characters,textures/cuadros,ui/menu,world}/`.
- **En curso / sin terminar:** barricada, pinchos y refuerzo de pared solo se ven (el refuerzo de puerta suma 100 a `bash_health`); una malla placeholder para los 4 huecos. En VR los demás ven mitones y además las manos XR flotantes (falta IK). El objeto en mano no se cuelga de `AnclaObjeto`. Sonidos de recoger, lanzar, hallazgos, nota, ropa y recarga solo los oye el dueño. `wolf_alpha.glb` del usuario no terminó de descargarse (el alfa usa el lobo oscuro). Quedan 13 fotos sin cuadro.
- **Siguiente paso:** instalar el APK en el Quest 2 y medir FPS (vigilar coste de occlusion culling en CPU y que cargue `liblimboai` en Android); partida con visor y 2–4 jugadores; hornear LightmapGI (D4-07).
- **Bloqueos:** no hay remoto de git (sin PR). Decisión sobre los `.tres` reserializados de `addons/`.
- **Cómo verificar:** `tools/verificar.ps1`; pruebas `test_partida_completa`, `test_partida_completa_lan` (`-- host`, cliente ~6 s después), `test_bugs_dia7`, `test_krampus`, `test_cuadros`, `test_muerte`, `test_menu`, `test_gui`, `test_oleadas`, `test_dia5_integrado`. Medir draw calls: `tools/perf/medir_draw_calls.gd`.

### Handoff — 2026-11-01 — Claude (día 6)
- **Hecho:** GUI completa (tema, menú 360, opciones, HUD de PC, reloj VR, nota, pausa) y ciclo de muerte (derribo, reanimación, espectador, minijefe, final). `game.tscn` instancia `Muerte`; `vr_rig.gd` aplica ángulo de giro y altura sentado; claves nuevas en `textos.csv` (MENU_BACK_TO_MENU no se añade: el panel final usa MENU_QUIT_TO_MENU).
- **Archivos tocados:** `assets/fonts/`, `assets/ui/`, `assets/menu/`, `resources/ui/`, `scenes/ui/`, `scripts/ui/`, `scenes/menu/`, `scripts/menu/`, `tools/ui/`, `tools/menu/`, `scripts/core/settings.gd`, `scripts/player/` (`player.gd`, `pc_rig.gd`, `vr_rig.gd`, `temperature.gd`, `downed.gd`, `wrist_watch.gd`), `scenes/player/player.tscn`, `scenes/player/vr/`, `scenes/ai/miniboss/`, `scripts/ai/miniboss/`, `scripts/game/death_manager.gd`, `scenes/world/cabin/cabin.tscn`, `project.godot` (escena principal = `main_menu.tscn`), `Docs/`. Calientes: `game.tscn`, `player.tscn`, `player.gd`, `project.godot`, `textos.csv`.
- **En curso / sin terminar:** nada. Falta probar todo lo VR con visor (ver problemas abiertos del día 6).
- **Siguiente paso:** día 7: integración y Quest; probar con visor y clientes reales; teclado virtual en el lobby VR.
- **Bloqueos:** no hay remoto de git; decisión sobre los `.tres` reserializados de `addons/` y otros `.tres` sueltos sin commitear.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`; pruebas `test_gui`, `test_menu`, `test_muerte`, `test_muerte_lan` (`-- host` y `-- client`), `test_dia5_integrado` y `test_ia` con `--script res://scripts/test/<nombre>.gd`.

### Handoff — 2026-11-01 — OpenCode (cierre de D5-06 y verificación del día 5)
- **Hecho:** escena `scenes/ai/krampus/krampus.tscn` completa con primitivas (cuerpo, cuernos, ojos emisivos, cadena, saco y pastel oculto), `Salud`, percepción, navegación, BTPlayer y 8 sonidos; `game.tscn` instancia `Oleadas` y `Krampus`; marcador `MK_KrampusSpawn` del terreno; claves `KRAMPUS_*` en `textos.csv`; `Creature.move_to` acepta `ignore_navigation` y los renos cruzan la puerta en línea recta para llegar a la chimenea. `test_krampus` 52 PASS y `test_oleadas`/`test_animales` sin FAIL.
- **Archivos tocados:** `scenes/ai/krampus/krampus.tscn`, `scenes/ai/krampus/krampus_director.tscn`, `resources/ai/krampus/`, `scripts/ai/krampus/`, `scripts/ai/tasks/krampus/`, `scripts/ai/creature.gd`, `scripts/ai/tasks/enemies/reindeer/bt_drain_fire.gd`, `scripts/ai/wave_director.gd`, `scripts/test/test_krampus.gd`, `scripts/test/test_oleadas.gd`, `scripts/test/test_animales.gd`, `scripts/test/test_gnomo_fantasma_lan.gd`, `resources/i18n/textos.csv`, `Docs/TAREAS.md`, `Docs/CONTRATOS.md`, `Docs/ESTADO.md`. Calientes: `game.tscn` (ya traía los directores), `textos.csv`, `map.tscn` (sin cambios nuevos: los marcadores están en `terrain.glb`).
- **En curso / sin terminar:** nada. Modelos de Tripo (reno primero) pendientes de sustituir los provisionales.
- **Siguiente paso:** día 6 (GUI y ciclo de muerte); probar con visor y clientes reales; hornear `LightmapGI`.
- **Bloqueos:** no hay remoto de git; decisión sobre los `.tres` reserializados de `addons/`.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`; pruebas `test_ia`, `test_ia_lan` (host + cliente), `test_animales`, `test_gnomo_fantasma` (+ `_lan`), `test_oleadas`, `test_krampus` y `test_dia5_integrado`; todas en verde (96, 56, 62, 52 y 22 PASS respectivamente).

### Handoff — 2026-11-01 — Claude (día 5)
- **Hecho:** día 5 completo en código (animales, gnomo, fantasma, renos, Rodolfo, oleadas y Krampus) e integrado en `game.tscn` (`Oleadas`, `Krampus`) y `map.tscn` (`Fauna`). Claves `KRAMPUS_*` en `textos.csv`. Prueba integrada `test_dia5_integrado`.
- **Archivos tocados:** `scripts/ai/`, `scenes/ai/`, `resources/ai/`, `resources/waves/`, `tools/ai/`, `scenes/fx/gnome_explosion.tscn`, `scripts/test/test_*.gd`, `livestock_idle.gd`, `livestock_pen.tscn`, `Docs/AUDIO.md`, `Docs/CONTRATOS.md`. Calientes: `game.tscn`, `map.tscn`, `textos.csv`.
- **En curso / sin terminar:** nada en curso. Se están generando los modelos de Tripo (primero el reno) para sustituir los provisionales.
- **Siguiente paso:** día 6 (GUI y ciclo de muerte); probar con visor y con clientes reales; sustituir modelos provisionales.
- **Bloqueos:** remoto de git; decisión sobre los `.tres` de `addons/`.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1` y `godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/test_dia5_integrado.gd` (las demás pruebas, igual con su nombre; las `_lan` se lanzan con `-- host` y `-- client`).

### Handoff — 2026-11-01 — Claude (día 4)
- **Hecho:** día 4 completo en código (reglas, ciclo, luz falsa, precarga, anomalías, péndulo) e integrado en `game.tscn`, `cabin.tscn` y `map.tscn`.
- **Archivos tocados:** `scripts/rules/`, `scenes/rules/`, `scripts/interactables/rules/`, `scenes/interactables/rules/`, `scripts/events/`, `scenes/events/`, `resources/events/`, `resources/world/`, `scripts/world/day_night*.gd`, `cabin_lights.gd`, `scenes/main/`, `assets/shaders/`, `door.gd/.tscn`, `event_bus.gd`, `Docs/AUDIO.md`. Calientes: `project.godot` (debanding), `game.tscn`, `map.tscn`, `cabin.tscn`, `textos.csv`.
- **En curso / sin terminar:** nada en curso; D4-07 y D4-08 pendientes.
- **Siguiente paso:** probar con visor y LAN real; día 5 (IA con LimboAI).
- **Bloqueos:** remoto de git; decisión sobre `.tres` de `addons/`.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`; en el editor, `game.tscn` + F6.

### Handoff — 2026-11-01 — Claude
- **Hecho:** commit del trabajo pendiente de OpenCode; D3-14 (red en `game.tscn`, 2 procesos OK), D3-12 (VR, headless OK) y aceptación de D3-01..05 con arreglos en `pc_rig.gd`, `fireplace.gd`, `hud_pc.gd`, `game.gd`, `world_items.gd`, `item_behavior.gd`, `consumable_behavior.gd`, `armor_behavior.gd`, `temperature.gd`, `vr_rig.gd`.
- **Archivos tocados:** los anteriores, `scenes/game/game.tscn`, `scenes/interactables/test/caja_prueba.tscn`, `tronco_pesado.tscn`, `resources/i18n/textos.csv`, `Docs/TAREAS.md`, `Docs/ESTADO.md`. Calientes tocados: `game.tscn`, `textos.csv`.
- **En curso / sin terminar:** nada en curso. Pendientes D3-18, D3-19, D3-20.
- **Siguiente paso:** probar con visor (PCVR/Quest) y LAN con ventanas; luego día 4 (reglas, ciclo, anomalías).
- **Bloqueos:** remoto de git, decisión sobre los `.tres` de `addons/`.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`; en el editor, `game.tscn` + F6.

### Handoff — 2026-10-31 — OpenCode
- **Hecho:** diagnóstico del repo, `AGENTS.md`, `Docs/TAREAS.md`, `tools/verificar.ps1`, prueba de escenas, 10 escenas de objeto, 3 materias primas, 3 escenas de recurso, `world_scene` en los 14 `.tres`, nodo `Temperatura` en `Player.tscn` y corrección de la referencia circular `ItemData` ↔ escena.
- **Archivos tocados:** `AGENTS.md`, `CLAUDE.md`, `.gitignore`, `Docs/TAREAS.md`, `Docs/ESTADO.md`, `Docs/CONTRATOS.md`, `tools/verificar.ps1`, `scenes/test/`, `scripts/test/`, `scenes/items/` (10 nuevas + 14 modificadas), `scenes/items/materials/`, `scenes/world/resources/`, `scripts/items/item_behavior.gd`, `scenes/player/player.tscn`, `resources/items/*.tres`.
- **En curso / sin terminar:** colocar recursos en el mapa, cabaña/crafteo e integración de inventario/HUD.
- **Siguiente paso:** D3-08 en `map.tscn` y después D3-09/10.
- **Bloqueos:** remoto privado y decisión sobre los `.tres` de `addons/`.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1`.

### Handoff — 2026-11-03 — Claude (versión jugable, parte 2)
Plan: `Docs/PLAN_JUGABLE2.md` (flujos A–G, todos hechos). Verificado: `verificar.ps1` OK, tests clave y LAN host+cliente en verde (test_oleadas intermitente una vez).
- Crafteo con lista de recetas e iconos; frío letal solo de noche; partida x2 (~30 min); pilas de 5 en el saco.
- Antorcha equipable con recarga por madera; medidor de la fogata; madera y piedra guardables.
- Linterna real de bolsillo (Q, sin slot, desde el inicio; VR botón X izquierdo).
- Defensas exteriores (anillo de vallas/pinchos/trampas/braseros, fusión de mallas, 148 draw calls).
- Audio: pasos por distancia, jadeo de estamina, péndulo con sonido (SFX CC0 de OpenGameArt).
- Combate: fantasmas matables (luz o daño), barras de vida y feedback de daño en todos, 4 recetas de armas de fuego (costes propuestos, no del GDD).
- Mundo: puerta abrible (la colisión estaba mal anidada), texturas Poly Haven en la cabaña, gnomos en oleadas, galletas diarias, modelos Kenney en madera/piedra/mineral/vallas.
- PC: objeto fijo a la cámara; VR: snap suave al agarrar; auras de color por jugador.
- Sin probar en ventana real ni con visor: modelos/texturas nuevas, linterna, aura, puerta VR, sonido a oído.
- Pendiente: primitivas restantes (oso, oso alfa, miniboss_gnome, gnomo minero, coro de fantasmas, cake, workbench, etc.); peor punto de draw calls ~148–160 en cabana_lenador; antorcha tirada por cliente nace llena; linterna de bolsillo no se ve en avatar remoto PC; Tripo T1–T8 esperan aprobación.

### Handoff — 2026-11-07 — OpenCode (inventario único, construcción, noche 1 y cascabel)
- **Hecho:**
  - Inventario único de 25 ranuras (0–4 barra rápida, 5–24 mochila) con pilas de 99; saco eliminado por completo (nodos, scripts, escena, `.tres`, acción y textos).
  - Botón de inventario separado de la barra rápida; cuaderno PC y panel VR comparten la rejilla 5×5; munición y materias primas apilables.
  - Montones del suelo: un objeto físico con meta `item_amount` y etiqueta `xN`; al soltar una pila se crea un solo montón. Los recursos (árbol/roca/veta) también sueltan un único montón.
  - Construcción y chimenea consumen el material del inventario si no está en la mano (el objeto físico sigue valiendo en VR).
  - Noche 1 más asequible: 3 renos + 0,5 por ira + 1,0 por jugador extra, gnomos fijos, 12 s de margen y 14 s entre apariciones.
  - Regresión nueva del péndulo del cascabel (paridad, efectos bueno/malo y reutilización).
  - `Docs/BALANCE.md`, `Docs/TAREAS.md` y `Docs/CONTRATOS.md` actualizados; D12-03 marcado como sustituido por D13-04.
- **Archivos tocados:** `scripts/inventory/inventory.gd`, `scripts/game/world_items.gd`, `scripts/world/resource_node.gd`, `scripts/crafting/{build_slot,workbench}.gd`, `scripts/interactables/fireplace.gd`, `scripts/items/behaviors/torch_behavior.gd`, `scripts/player/{pc_rig,vr_rig}.gd`, `scripts/player/vr/{inventory_vr,pause_vr,bench_vr}.gd`, `scripts/ui/{hud_pc,inventory_pc,inventory_panel,crafting_ui_pc,hud_slot,touch_button,touch_controls}.gd`, `scripts/items/{item_behavior,item_db,starter_kit,pocket_flashlight}.gd`, `scripts/core/input_remap.gd`, escenas y `.tres` de inventario, `project.godot`, `resources/i18n/*`, `resources/waves/noche_1.tres`, `scripts/test/test_cascabel.gd` y pruebas tocadas, `Docs/{BALANCE,TAREAS,CONTRATOS,ESTADO}.md`.
- **En curso / sin terminar:** nada de esta sesión. Queda probar en visor la rejilla VR y una partida real de Noche 1 con 2–4 jugadores.
- **Siguiente paso:** commit por temas (inventario; construcción/recursos; dificultad Noche 1; cascabel) sin incluir `12-12-12.pck`, `.import`, `addons/**`, `resources/waves/noche_final.tres`, `scenes/world/cabin/cabin.tscn` ni cambios personales previos.
- **Bloqueos:** ninguno.
- **Cómo verificar:** `powershell -ExecutionPolicy Bypass -File tools/verificar.ps1 -SkipImport`; tests `test_inventario_pilas` (54), `test_inventario_ui` (23), `test_bucle_dia` (42), `test_cascabel` (24), `test_oleadas` (63), `test_noche` (40), `test_fogata_madera` (23), `test_vr_flujo` (47), `test_mando` (57), `test_tactil` (39).

### Handoff — 2026-11-09 — Claude (animaciones en primera persona e interfaz)
Plan: `Docs/PLAN_ANIMACIONES_UI.md`.
- **Hecho:**
  - Hacha, hacha de hierro, pico, pico de hierro y martillo miran al frente en mano (`item_model_turn_deg` en `viewmodel_hands.gd`, gira el modelo y no la raíz).
  - Manos del PC con una fase por mano (la izquierda desfasada media vuelta), tomada de la fase de pasos de la cámara (`PCRig.get_bob_phase`); amplitud mayor al andar y al correr; la mano que sujeta algo se amortigua (`hold_damp`).
  - Manos de Quest 2 con el mismo balanceo, solo visual: los controladores y el agarre no cambian (`vr_rig.gd`, `_update_gait`).
  - Barra rápida: nombres cortos `ITEM_X_HUD` (con respaldo al nombre completo), quinta casilla "Mochila", valor en los títulos de Vida y Temperatura, casilla vacía más visible.
- **Sin terminar:**
  - Revisión de los controles táctiles en Android (Reno 11) con APK; no se ven en PC.
  - Sentir el balanceo de las manos de Quest 2 en visor.
  - Escopeta inclinada en mano (no pedida).
- **Archivos tocados:** `scripts/player/{viewmodel_hands,hand_animator,pc_rig,vr_rig}.gd`, `scripts/ui/{hud_pc,hud_slot}.gd`, `resources/i18n/textos.csv` (+ `.translation`), `scripts/test/{test_manos_animacion,test_inventario_ui}.gd`, `Docs/PLAN_ANIMACIONES_UI.md`.
- **Cómo verificar:** `test_manos_animacion` (43), `test_equipado_manos` (46), `test_inventario_ui`, `test_vr_flujo` (47), `test_mando` (57), `test_tactil`; `test_escenas` 32/32. Capturas con `tools/captura/captura_mano.gd`.
- **Bloqueos:** ninguno. `verificar.ps1` marca como error el aviso "4 resources still in use at exit" (no comparado con la versión anterior).

### Handoff — 2026-11-09 — Claude (controles táctiles para móvil)
- **Hecho:**
  - Los toques se reparten por posición (`TouchControls._input`): un dedo sobre un botón o el stick lo tiene hasta soltarlo; cualquier otro dedo gira la cámara, también mientras se mantiene un botón.
  - Mantener Atacar, Usar (Interactuar) o Saltar repite la pulsación (0,25 / 0,35 / 0,45 s). Mantener Correr 0,4 s lo bloquea; un toque posterior lo quita (aparece "Fijo" bajo el botón).
  - Empujar el stick arriba 1,2 s activa el avance automático ("Auto" sobre el stick, en verde); tirar hacia atrás lo cancela. Con Correr bloqueado, el avance corre.
  - Autosalto en el móvil: si se choca de frente con algo bajo (rayo a la altura de la cabeza libre) y se está en el suelo, salta solo.
  - Escala de interfaz para móvil (`UiPrefs.apply_window_scale`, base 1280×720 con expansión): los menús y los textos crecen con la pantalla. Solo Android sin visor; lo llama el menú principal.
  - Distribución: joystick abajo a la izquierda, sobre la barra de salud; Atacar grande abajo a la derecha; Saltar y Correr a su izquierda; Inventario e Interactuar arriba.
- **Pruebas:** `test_tactil` 50/50 (incluye mirar con botón, repetición, bloqueo y avance automático), `test_mando` 57, `test_movimiento` 32, `test_manos_animacion` 43, `test_vr_flujo` 47 (de la vez anterior). Capturas con escala de móvil en `tools/logs/captura/tactil_*.png` (`tools/captura/captura_tactil.gd`).
- **Sin probar en el teléfono:** el autosalto, el tacto real de los multitoques y el tamaño de los textos. Hay que instalar `builds/android-flat/12-12-12-tactil.apk`.
- **Bloqueos:** el teléfono no apareció en `adb devices` tras exportar el APK; instalarlo a mano o reconectar por USB.

### Handoff — 2026-11-09 — Claude (toques en la interfaz, inventario táctil y balanceo de las manos)
- **Hecho:**
  - Los toques que no son de los controles (casillas de la barra, mochila, inventario) pasan a la GUI y se convierten en clics. Antes `TouchControls` los consumía y la barra no respondía.
  - Tocar una casilla de la barra equipa ese objeto (`HudSlot.tapped` → `PCRig._equip_slot`).
  - Inventario en móvil: la X se agranda, aparece un botón "Soltar" (tira una unidad del objeto marcado) y los textos de ayuda son táctiles.
  - El objeto en mano ya no se balancea de lado: el viewmodel usa la cámara sin el vaivén lateral ni el balanceo de cabeza (`PCRig._steady_view`). Al andar, lo que llevan las manos se mueve hacia delante y atrás.
  - Las herramientas con cabeza se inclinan 18° hacia delante en la mano (era 6°). Capturas en `tools/logs/captura/mano_*.png`.
- **Pruebas:** `test_tactil` 54/54 (incluye tocar una casilla de la barra y del inventario con toques reales), `test_manos_animacion` 43, `test_equipado_manos` 46, `test_mando` 57, `test_movimiento` 32, `test_inventario_ui` 23. `test_interfaz_nueva` 61 (12 errores de compilación en modo script, ya existían).
- **Sin probar en el teléfono:** la sensación del balanceo y el tamaño del botón Soltar. El APK `builds/android-flat/12-12-12-tactil.apk` se exportó, pero el Reno 11 no estaba conectado para instalarlo.
- **Bloqueos:** ninguno en el código; falta instalar el APK.

### Handoff — 2026-11-09 — Claude (coro de fantasmas: se puede morir)
- **Causa:** el núcleo solo recibía daño si los 3 fantasmas estaban iluminados a la vez. Un jugador solo con una linterna no consigue eso a distancia de golpe (en pruebas, máximo 2 a la vez a 2 m), así que el núcleo era invulnerable en la práctica.
- **Cambio (decisión de diseño, pendiente de confirmar):** cada fantasma recuerda cuándo lo iluminaron (`marks_window_s`, 4 s). El núcleo se abre cuando los 3 han brillado dentro de esa ventana y queda abierto `open_duration_s` (5 s). Un jugador solo puede barrerlos con la linterna y golpear el núcleo; en cooperativo sigue valiendo repartirse. Los halos muestran los fantasmas marcados.
- **Pruebas:** `test_minijefes` 90/90 (incluye el barrido de una sola linterna y el golpe). Diagnóstico con una linterna: barrido de 4 s a 2 m abre el escudo; un golpe con el hacha baja 25 de 260.
- **Pendiente:** confirmar la ventana de 4 s y los 5 s abierto; si el núcleo se siente demasiado fácil o difícil, ajustar esas dos exportaciones en `coro_fantasmas.tscn`.

### Handoff — 2026-11-11 — Claude (análisis de pendientes para la versión final)
- **Hecho:** `Docs/PENDIENTES_VERSION_FINAL.md` con lo que falta del juego principal (sin el modo educativo), ordenado por prioridad. Batería de un proceso: 63 pruebas en verde. `test_fase1_base` tenía una comprobación desactualizada (cabeceo en mano de 10°; hoy 18°) y se corrigió.
- **Sin ejecutar:** pruebas LAN de dos procesos y pruebas del modo educativo.
- **Siguiente paso:** remoto de git; exportar e instalar los paquetes de Quest 2, Android y Windows; sesión de prueba por plataforma.
- **Bloqueos:** las seis decisiones del usuario listadas al final del documento.

### Handoff — 2026-11-11 — Claude (decisiones para la versión final)
- **Decisiones del usuario:** plataformas finales PC, PCVR, Quest 2 y Android táctil; regalo Educativo oculto; partida de 30 minutos definitiva; coro de fantasmas más fácil; los 8 modelos de Tripo aprobados; música CC0 o generada, con aprobación previa.
- **Hecho:** `main_menu.gd` con `show_educativo` (apagado: el regalo se quita al arrancar) y los tres regalos de vuelta a su posición original en `main_menu.tscn`; coro con ventana de 6 s y núcleo abierto 8 s; GDD al día (menú, duración, plataformas, coro); tabla de decisiones en `Docs/PENDIENTES_VERSION_FINAL.md`.
- **Pruebas:** `test_menu` en verde, `test_minijefes` 90, `test_gui` 57. Captura `tools/logs/menu_inicio.png` revisada: tres regalos centrados.
- **Siguiente paso:** generar los modelos de Tripo (requiere la sesión abierta en el navegador), buscar la música y exportar los paquetes nuevos de las cuatro plataformas.

### Handoff — 2026-11-11 — Claude (modelos de Tripo en enemigos, jefe, horno y estatuas)
- **Hecho:** 8 modelos generados con Tripo (200 créditos, quedan 1425), procesados con `tools/blender/tripo/a_juego.py` (textura 1K en WebP, brazos bajados en los humanoides) y conectados en `miniboss_gnome`, `boss_estatua`, `statue_boss`, `merodeador`, `vigilante`, `visitante`, `structure_breaker`, `statue` y `furnace`. Detalle en `Docs/TRIPO_REGISTRO.md`.
- **Comprobado:** capturas `tools/logs/captura/modelos_tripo_*.png` (textura, orientación y escala). Las pruebas de estas escenas se lanzaron; revisar su resultado antes de dar el cambio por cerrado.
- **Sin hacer:** música (OpenGameArt no carga por certificado, FreePD cerró; en Free Music Archive el filtro CC0 no se aplica y hay que verificar la licencia pista por pista; candidatos: HoliznaCC0 y Komiku). T7 (Krampus forma final) no se generó.
- **Siguiente paso:** elegir y aprobar pistas de música; partida de prueba para ver los modelos nuevos en movimiento.
