# Contratos técnicos (fuente de verdad para todo agente)

Reglas que cualquier código, escena o asset del proyecto debe respetar. Si algo aquí choca con una tarea, gana este documento; si hace falta cambiarlo, se pide al orquestador.

## Regla de escenas editables
1. Cada entidad es una escena `.tscn` con sus nodos visibles en el árbol (mallas, colisiones, áreas, `Marker3D`, luces, partículas).
2. Prohibido construir nodos visuales por código (`MeshInstance3D.new()`, `add_child` de nodos creados a mano). El código solo instancia escenas existentes.
3. Parámetros con `@export`; datos en Resources `.tres`.
4. `@tool` solo para vistas previas en el editor, nunca para generar contenido oculto.
5. Ningún nodo con `visible = false` sin que su nombre lo explique (`HiddenUntilNight`).
6. Todo texto visible usa `tr("CLAVE")`. Nunca texto literal en escenas ni scripts.

## Motor
- Godot **4.7.2**, renderer **Mobile**, física **Jolt**, GDScript con tipado estático (`var x: int`, `func f() -> void`).
- Unidades en metros. Eje +Y arriba, −Z adelante.
- Nombres: archivos `snake_case`, escenas y nodos `PascalCase`, clases `class_name PascalCase`, señales en pasado (`rule_broken`).
- Tabulaciones para indentar (estilo Godot).

## Carpetas
```
res://addons/        plugins de terceros (no editar)
res://assets/        modelos, texturas, fuentes, UI (assets/map/, assets/characters/, assets/ui/)
res://audio/         vacía hasta que llegue la carpeta del usuario
res://resources/     datos .tres (items/, recipes/, waves/, ai/, themes/, i18n/)
res://scenes/        main/ menu/ world/ player/ items/ interactables/ npc/animals/ npc/enemies/ events/ ui/
res://scripts/       core/ y espejo de scenes/
res://tools/         scripts externos (Blender, etc.), no se cargan en el juego
```

## Autoloads (nombres fijos)
| Nombre | Archivo | Responsabilidad |
|---|---|---|
| `EventBus` | `scripts/core/event_bus.gd` | Señales globales |
| `GameState` | `scripts/core/game_state.gd` | Fase, reloj de 15 min, ira de Krampus |
| `NetManager` | `scripts/core/net_manager.gd` | LAN, jugadores (máx. 4), plataforma |
| `Settings` | `scripts/core/settings.gd` | Idioma, calidad, audio, confort VR |

## Capas de render
1 Mundo · 2 Viewmodel · 3 UI3D · 4 SoloVR · 5 SoloPC

## Capas de física
1 Mundo · 2 Jugador · 3 Animal · 4 Enemigo · 5 Agarrable · 6 Recurso · 7 Interactuable · 8 Trigger

## Mapa (convención del terreno)
- Sistema Blender: X = este, Y = norte, Z = arriba. El exportador glTF lo convierte a Godot (X este, −Z norte, Y arriba).
- Centro del mundo (0,0) = centro de la explanada de la cabaña.
- **Vertex color del terreno:** R = nieve fresca, G = nieve pisada / camino, B = roca / tierra helada. A = oclusión ambiental (1 = sin oclusión).
- UV0 = coordenadas de mundo / 4 m (textura en mosaico). UV2 = desplegado único para LightmapGI.
- Marcadores (empties exportados como `Node3D`): `MK_Cabin`, `MK_Farm`, `MK_Lake`, `MK_PlayerSpawn_1..4`, `MK_KrampusSpawn`, `MK_WaveSpawn_N`, `MK_WaveSpawn_E`, `MK_WaveSpawn_S`, `MK_WaveSpawn_W`.

## IDs de objetos de inventario (14)
`axe`, `pickaxe`, `flashlight`, `cold_armor`, `fruit_veg`, `meat`, `water`, `milk`, `cookies`, `shotgun`, `shotgun_ammo`, `deagle`, `pistol_ammo`, `torch`.
La antorcha (`torch`) es un objeto de inventario equipable como el hacha (decisión del usuario, sustituye a "agarrable del mundo que no ocupa inventario"): se recoge con E, se equipa con tecla/rueda o F (segunda mano) en PC y con mano/bolsillo/inventario en VR; se enciende al equiparla y se apaga al guardarla (guardada no gasta). Su combustible (0..1) se guarda en `Inventory.torch_fuel`. Usar (clic principal; clic derecho en segunda mano; gatillo en VR) con madera en el inventario la recarga: 1 madera = +25 % (`TorchBehavior.wood_refuel_fraction`). Sigue siendo agarrable físico (clic principal sin nada equipado) y la chimenea la recarga. Una antorcha encendida en la mano cuenta como fuego (frío, elfos).

## Claves de traducción
- Formato `MAYUSCULAS_CON_GUIONES_BAJOS`.
- Objetos: `ITEM_<ID>_NAME`, `ITEM_<ID>_DESC` (ID en mayúsculas, p. ej. `ITEM_SHOTGUN_AMMO_NAME`).
- Reglas: `RULE_01` … `RULE_11` (texto de la nota).
- Menú: `MENU_*`; Opciones: `OPT_*`; HUD: `HUD_*`; Muerte: `DEATH_*`; Fases: `PHASE_*`.
- Archivo único: `resources/i18n/textos.csv`, columnas `keys,es,en`.

## Jugador (día 2)

**Escenas**
```
scenes/player/player.tscn        Player (Node3D, scripts/player/player.gd)
  ├─ PCRig      (instancia de scenes/player/pc/pc_rig.tscn,  extiende PlayerRig)
  ├─ VRRig      (instancia de scenes/player/vr/vr_rig.tscn,  extiende PlayerRig)
  ├─ Avatar     (instancia de scenes/player/avatar/avatar.tscn: cuerpo visible para los demás)
  ├─ StateSynchronizer   (netfox: sincroniza el estado de abajo desde la autoridad)
  └─ TickInterpolator    (netfox: suaviza el estado en los peers remotos)
scenes/game/game.tscn            Game: mapa + Players + MultiplayerSpawner
scenes/menu/lobby_debug.tscn     Lobby provisional (el menú 360 llega el día 6)
```

**Reglas**
- El nodo `Player` se llama como el `peer_id` (`"1"`, `"2"`…) y su autoridad multijugador es ese peer.
- `Player` nunca se mueve: queda en el punto de aparición. El rig activo mueve su propio cuerpo dentro de él.
- Solo un rig activo, y solo en el peer local: `PCRig` si OpenXR no está inicializado, `VRRig` si lo está. En los demás peers ambos rigs están desactivados y se ve `Avatar`.
- El rig activo **escribe** el estado sincronizado en `Player` cada `_physics_process`. Nadie más lo escribe.
- `Avatar` **solo lee** el estado sincronizado. En el peer local el avatar se oculta (salvo sombras).
- Los rigs no dependen de los ticks de netfox: deben funcionar sin red (modo un jugador, `OfflineMultiplayerPeer`).

**Estado sincronizado en `Player`** (todo en coordenadas globales salvo que diga *local*)
| Propiedad | Tipo | Significado |
|---|---|---|
| `body_transform` | `Transform3D` | Pies del jugador (origen) y orientación del cuerpo (solo yaw) |
| `head_local` | `Transform3D` | Cabeza relativa a `body_transform` |
| `left_hand_local` / `right_hand_local` | `Transform3D` | Manos relativas a `body_transform` (en PC: posiciones de reposo calculadas) |
| `platform` | `int` | `NetManager.Platform` |
| `crouching`, `sprinting` | `bool` | Postura |
| `noise_level` | `float` 0..1 | Ruido que hace (agachado ≈ 0.15, caminar ≈ 0.5, correr = 1). Lo usa la IA |

**Clase base de los rigs:** `scripts/player/player_rig.gd` (`class_name PlayerRig`, extiende `Node3D`):
- `activate(player: Player) -> void`: toma la cámara, captura la entrada y empieza a escribir el estado.
- `deactivate() -> void`: suelta todo, desactiva procesamiento y oculta lo visual.
- `get_camera() -> Camera3D`.
- `teleport_to(xform: Transform3D) -> void`.

**Agarre (PC y VR)**
- Todo objeto agarrable del mundo es un `XRToolsPickable` (RigidBody3D) en la capa física 5 *Agarrable*. Su `mass` es el peso real en kg.
- VR: lo agarran las manos con `XRToolsFunctionPickup` de XR Tools.
- PC: mantener `primary` sobre el objeto lo sujeta con física (se arrastra hacia un punto frente a la cámara; los objetos pesados siguen más lento y no se pueden levantar del todo por encima de ~25 kg). `rotate_held` (R) + ratón lo gira. Soltar `primary` lo suelta; `secondary` mientras se sujeta lo lanza.
- Mientras un jugador sujeta un objeto, es su autoridad multijugador (se transfiere por RPC al host; el día 3 se completa).

**Interacción**
- Interactuables (puertas, mesas) en la capa física 7 con un script que implemente `interact(player: Player) -> void`.
- PC: rayo desde la cámara, 2.5 m, acción `interact` (E). VR: tocar o apuntar con la mano + gatillo (día 3).

## Objetos, inventario y mundo (día 3)

**Escena de objeto** (`scenes/items/item_<id>.tscn`, una por cada ID del inventario, `torch` incluida):
```
Item<Id>        XRToolsPickable (RigidBody3D), capa física 5, máscara 1; mass = ItemData.weight_kg
  ├─ Modelo         Node3D con las mallas (provisionales: primitivas) — capa de render 1
  ├─ Colision       CollisionShape3D
  ├─ Comportamiento ItemBehavior o subclase; item_id = &"<id>" (data se resuelve con ItemDB)
  ├─ RedSync        NetPickable (sincroniza en red; ver abajo)
  └─ (según tipo)   PuntaHerramienta (Area3D, golpes VR) · Canon (Marker3D) · Luz (SpotLight3D) · Calor (HeatSource)
```
- `ItemData.world_scene` apunta a esta escena. La escena **no** referencia su `.tres` directamente:
  el `item_id` del `Comportamiento` se resuelve con `ItemDB` al cargar. Así se evita la referencia
  circular `ItemData.world_scene → escena → ItemData`, que deja `data` en null.
- La misma escena sirve para el mundo, para la mano VR y para el viewmodel de PC: `ItemBehavior.prepare_as_viewmodel(root)` la congela, le quita colisión y red y pasa sus mallas a la capa 2; `prepare_as_display(root)` igual pero en capa 1 (para el avatar remoto).

**`ItemBehavior`** (`scripts/items/item_behavior.gd`): `data`, `holder: Player`, `in_viewmodel: bool`;
`on_equipped(player, in_viewmodel)`, `on_unequipped()`, `use_pressed()`, `use_released()`, `reload()`, `can_use() -> bool` (falso si el jugador lleva fuego en la otra mano o agarrado, salvo la propia antorcha). En VR, el gatillo del pickable sujeto (`action_pressed/released`) llama a `use_pressed/use_released` automáticamente.

**`ItemDB`** (`scripts/items/item_db.gd`, estático): `ItemDB.get_item(id) -> ItemData`, `ItemDB.all_ids()`.

**`Inventory`** (`scripts/inventory/inventory.gd`, nodo `Inventario` hijo de `Player`): solo lo modifica su dueño.
`add(id, n) -> int` (sobrante), `remove(id, n) -> bool`, `count(id)`, `has(id, n)`, `holdable_ids() -> Array[StringName]`, señal `changed`. Máximo por objeto = `ItemData.max_stack`.

**Materias primas = objetos físicos** (no ocupan inventario): troncos (`scenes/items/materials/log.tscn`, 15 kg), piedras (`stone.tscn`, 4 kg), mineral (`ore.tscn`, 6 kg). Son `XRToolsPickable` con `RedSync` y un nodo `Material` (`MaterialTag`, `@export var tag: StringName` = `&"wood"|&"stone"|&"ore"`). Se cargan y arrastran como cualquier objeto; la mesa de trabajo y la chimenea los consumen. Los productos fabricados también son físicos: `board.tscn` (`&"board"`, 3 kg) y `metal_sheet.tscn` (`&"metal"`, 6 kg).

**Objetos del mundo en red**
- `game.tscn` tiene `ObjetosMundo` (Node3D) + `SpawnerObjetos` (MultiplayerSpawner). Script `scripts/game/world_items.gd` en el nodo `ObjetosMundo`, grupo `world_items`.
- `WorldItems.spawn(scene_path: String, xform: Transform3D)` (cualquier peer: el host lo crea y replica), `WorldItems.despawn(node)` (cualquier peer: el host lo borra). Acceso: `get_tree().get_first_node_in_group("world_items")`.
- **`NetPickable`** (`scripts/net/net_pickable.gd`, nodo `RedSync`): autoridad del host por defecto; al agarrar pide autoridad al host y la recibe el que agarra; los demás peers congelan el cuerpo (cinemático) y siguen la transformación sincronizada. VR: se engancha solo a `picked_up/dropped`. PC: el rig llama `notify_grabbed(player)` / `notify_released()`.

**`Health`** (`scripts/combat/health.gd`, nodo `Salud`): `max_health`, `health`, señales `damaged(amount, source_peer, reason_key)` y `died(source_peer, reason_key)`; `apply_damage(amount, source_peer, reason_key)` desde cualquier peer (se resuelve en el host). `Health.find_in(node) -> Health` busca en el nodo y sus ancestros.

**`ResourceNode`** (`scripts/world/resource_node.gd`): árbol / roca / veta en capa física 6 *Recurso*; `@export harvest_tag` (`&"wood"`/`&"stone"`), `hits_to_break`, `drop_scene`, `drop_count`; `hit(tool_tag: StringName, from_peer: int)` (se resuelve en el host; al romperse suelta materias con `WorldItems.spawn`).

**Calor y frío**
- `HeatSource` (`scripts/world/heat_source.gd`, grupo `heat_source`): `@export heat_per_second`, `radius`, `is_fire: bool`, `active: bool`; `get_heat_at(pos) -> float`.
- Zona interior: `Area3D` en el grupo `indoor_zone` (la cabaña). Dentro: sin pérdida de frío exterior y sin nieve.
- `Temperature` (nodo `Temperatura` hijo de `Player`): 0–100; fuera baja (día lento con chamarra; noche rápido salvo que lleve fuego); a 0 emite `EventBus.player_downed`.
- `Player.is_holding_fire()` y `Player.held_item_id` (sincronizado) — ver `player.gd`.

**Cabaña** (`scenes/world/cabin/cabin.tscn`, instanciada en `map.tscn/Cabana`): provisional con primitivas hasta tener los modelos. Interior de 10×8 m, paredes de 3 m con huecos reales para puerta y ventanas, techo a dos aguas. Nodos:
- `Puerta` (`Door`, `scripts/interactables/door.gd`): capa 1|7; `interact` abre/cierra (giro de `Bisagra`); `locked` la bloquea (regla 10) y avisa con `DOOR_LOCKED`; estado replicado por RPC.
- `VentanaEste` / `VentanaOeste` (`Window`, `window.gd`): `interact` abre/cierra; si el jugador lleva un `board` o `metal` en la mano, cubre la ventana (regla 5) y consume el objeto. `state_changed(is_open, is_boarded)`.
- `Chimenea` (`Fireplace`, `fireplace.gd`): `max_fuel_s`/`log_fuel_s`, luz y partículas; el host descuenta combustible y lo replica cada `sync_interval_s`; `interact` añade leña si el jugador lleva `wood`/`board`; emite `EventBus.fire_strength_changed` al cambiar la proporción.
- `MesaTrabajo` (`Workbench`, `workbench.gd`): `Area3D` encima de la mesa (`material_area`) detecta materias primas; el host consume el coste de la primera receta posible (en orden de `recipes`) y suelta el resultado con `WorldItems.spawn`; avisa al peer que fabricó (`WORKBENCH_CRAFTED` / `HUD_NOT_ENOUGH`).
- `ZonaInterior` (`Area3D`, grupo `indoor_zone`), `LuzInterior`, `BuildSlots` para mejoras y marcadores para las reglas del día 4 (`MK_Pastel`, `MK_BotonVerde`, `MK_Leche`, `MK_Arbol`, `MK_Cuadro_1..4`).

**Recetas** (`resources/recipes/*.tres`, `scripts/crafting/recipe.gd`): `id`, `name_key` (`CRAFT_*`), coste (`wood_cost`, `stone_cost`, `ore_cost`, `board_cost`), `output_scene` y `output_count`. `Recipe.can_craft(available)` y `Recipe.cost()`. Crafteo de prueba: cubierta de madera (2 madera) y de metal (1 madera + 1 mineral), barricada (4 madera → 2 tablones), pinchos (2 madera), refuerzo de pared (3 madera + 2 piedra) y de puerta (2 madera + 1 mineral).

## Reglas, ciclo y ambiente (día 4)

**Todo lo de reglas y anomalías se decide en el host**; los clientes solo reciben avisos por RPC.

**`Rule`** (`scripts/rules/rule.gd`, base de cada regla; una escena por regla en `scenes/rules/rule_NN.tscn`):
- `@export rule_id` (1–11), `wrath_amount`, `cooldown` (segundos mínimos entre dos roturas).
- `break_rule(culprit_peer := 0)`: solo en el host; respeta el cooldown y avisa al `RuleManager`.
- `start_event()`: para reglas con evento (6 cuadro, 7 cascabeles, 8 árbol, 10 golpes): lo dispara el propio programador aleatorio de la regla o el director de anomalías.
- `_host_tick(delta)`: lógica periódica; el `RuleManager` lo llama solo en el host con la partida en marcha.
- Utilidades: `get_cabin()`, `get_players()`, `get_marker(name)` (marcadores `MK_*` de la cabaña o del mapa).

**`RuleManager`** (`scripts/rules/rule_manager.gd`, nodo `Reglas` en `game.tscn`, grupo `rule_manager`): las reglas son sus hijos. Al romperse una: `GameState.add_wrath(wrath_amount)` y RPC a todos → `EventBus.rule_broken(rule_id, culprit_peer)`. Expone `get_rule(id)`, `trigger_event(id)`.

**Comprobación de cada regla**
| # | Cuándo se rompe |
|---|---|
| 1 | Alguien come el pastel (`MK_Pastel`). |
| 2 | Alguien pulsa el botón verde (`MK_BotonVerde`). Consecuencia inmediata: apagón (`EventBus.lights_out`). |
| 3 | La chimenea se queda sin combustible. |
| 4 | Al empezar cada noche no hay vaso lleno de leche **y** plato con galletas en la mesa (`MK_Leche`). |
| 5 | Al empezar cada noche hay una puerta abierta o una ventana abierta sin cubrir. |
| 6 | Un cuadro cambia (evento) y nadie lo voltea en `flip_time` (60 s). |
| 7 | Suenan cascabeles o risas (evento) y algún jugador se mueve durante `freeze_time` (6 s): `noise_level` > umbral. |
| 8 | El árbol deja de brillar (evento) y nadie lo decora en `decorate_time` (60 s). |
| 9 | La luna se pone (75 % de cada fase de día, `EventBus.moon_set`) sin que se haya alimentado al ganado en esa fase. |
| 10 | Tocan la puerta (evento) y alguien la abre en los `knock_time` (20 s) siguientes. |
| 11 | Un jugador está fuera de noche (`Temperatura.is_outside_at_night()`), una vez por jugador y noche. |

**Señales nuevas en `EventBus`:** `moon_set`, `moon_rose`, `lights_out(on: bool)`, `rule_event_started(rule_id, seconds)`, `rule_event_ended(rule_id, resolved)`, `daylight_changed(factor: float)` (0 noche – 1 día).

**Ciclo día/noche** (`scenes/world/day_night.tscn` en `map.tscn`): sigue a `GameState` (fase y tiempo restante); mueve sol/luna (una sola `DirectionalLight3D` con sombra corta), cambia cielo, niebla y luz ambiente entre presets editables (`.tres`), crepúsculo en los últimos 20 s de cada fase y luna que se pone al 75 % de cada día.

**Director de anomalías** (`scenes/events/anomaly_director.tscn`, en `game.tscn`): escucha `krampus_wrath_changed` y `rule_broken`; dispara anomalías de `scenes/events/anomalies/` según una tabla `.tres` por nivel; en VR los glitches son en el mundo, nunca a pantalla completa.

**Péndulo del cascabel** (`scenes/events/bell_pendulum.tscn`): aparece de vez en cuando (y en el nivel 3 de ira), oscila y se detiene a la izquierda (evento malo) o a la derecha (bueno) según el host; eventos en `resources/events/pendulum_table.tres`.

**Audio:** cada sonido es un `AudioStreamPlayer`/`AudioStreamPlayer3D` con nombre claro y `stream` vacío hasta que llegue la carpeta del usuario; los nombres de archivo esperados se listan en `Docs/AUDIO.md`.

## IA, animales y enemigos (día 5)

**Toda la IA corre en el host.** Los clientes no ejecutan árboles de comportamiento: solo reciben
posición, giro y un estado de animación, e interpolan. Sin red (`OfflineMultiplayerPeer`) el peer
local es el host y todo funciona igual.

**Escena de criatura** (`scenes/ai/<tipo>/<nombre>.tscn`, una por criatura):
```
<Nombre>       Creature (CharacterBody3D, scripts/ai/creature.gd o subclase)
               capa física 3 Animal o 4 Enemigo; máscara 1 (Mundo)
  ├─ Modelo        Node3D con mallas provisionales (primitivas) — capa de render 1; materiales .tres compartidos
  ├─ Colision      CollisionShape3D
  ├─ Salud         Health (los disparos y herramientas ya dañan las capas 3 y 4)
  ├─ Percepcion    CreaturePerception (oído y vista)
  ├─ Navegacion    NavigationAgent3D
  ├─ IA            BTPlayer de LimboAI; behavior_tree = resources/ai/<nombre>.tres; solo activo en el host
  ├─ RedSync       MultiplayerSynchronizer (autoridad = host)
  └─ Sonidos       AudioStreamPlayer3D vacíos con nombre claro (ver Docs/AUDIO.md)
```
- Los parámetros (velocidad, daño, radios, comida que suelta) son `@export` en la escena; los árboles de
  comportamiento son `.tres` de LimboAI editables con su editor. Tareas propias en `scripts/ai/tasks/`
  (`BTAction`/`BTCondition`, prefijo de clase `BT`).
- Los modelos definitivos llegan después (Tripo/diseñadores): `Modelo` se sustituye sin tocar el script.

**`Creature`** (`scripts/ai/creature.gd`, grupo `creatures` y además `animals` o `enemies`):
- `@export`: `move_speed`, `run_speed`, `turn_speed`, `attack_damage`, `attack_range`, `attack_cooldown`,
  `death_reason_key` (`DEATH_REASON_*`), `food_scene` + `food_count` (lo que suelta al morir con
  `WorldItems.spawn`), `is_alpha`, `despawn_after_death_s`.
- Estado de red: `anim_state: StringName` (`&"idle"`, `&"walk"`, `&"run"`, `&"attack"`, `&"hurt"`, `&"dead"`…),
  replicado junto con la transformación. El script del modelo lo lee para animar.
- Movimiento (host): `move_to(pos, running := false, ignore_navigation := false)`, `stop()`, `face(pos)`, `has_arrived() -> bool`;
  usa el `NavigationAgent3D` si hay malla de navegación y, si no, dirección directa con gravedad. Con
  `ignore_navigation` va siempre en línea recta (para cruzar umbrales que la malla no cubre, p. ej. la puerta).
- Objetivo: `target: Node3D` (normalmente un `Player`), `set_target(node)`, `get_target_player() -> Player`.
- Ataque (host): `try_attack(player) -> bool` respeta rango y cooldown; daña con
  `Health.find_in(player).apply_damage(attack_damage, 0, death_reason_key)` y puede aplicar efectos con
  `player.apply_effect(...)`.
- Señales: `target_changed(node)`, `attacked(player)`, `died(source_peer)`.
- Al morir (host): `anim_state = &"dead"`, suelta comida, emite `died` y se borra a los
  `despawn_after_death_s` con `CreatureSpawner.despawn`.

**`CreaturePerception`** (`scripts/ai/creature_perception.gd`, nodo `Percepcion`): solo en el host.
- `@export`: `hearing_radius` (se multiplica por `Player.noise_level`), `sight_range`, `sight_fov_deg`,
  `sight_mask` (por defecto 1, Mundo, para la línea de visión), `memory_s` (cuánto recuerda al objetivo perdido).
- `get_best_target() -> Player`, `can_see(player) -> bool`, `can_hear(player) -> bool`.
- Señales `player_noticed(player)` y `player_lost(player)`. Al recibir daño la criatura pasa a perseguir al
  jugador que la dañó (`source_peer`).
- No percibe a jugadores derribados o muertos (`Health.is_dead()`).

**Aparición en red**
- `game.tscn` tiene `Criaturas` (Node3D, `scripts/ai/creature_spawner.gd`, `class_name CreatureSpawner`,
  grupo `creature_spawner`) y `SpawnerCriaturas` (MultiplayerSpawner). Rutas permitidas: `res://scenes/ai/`.
- `CreatureSpawner.spawn(scene_path: String, xform: Transform3D, params := {}) -> Creature` (solo host),
  `despawn(node)` (solo host). `params` se aplica a propiedades exportadas antes de entrar al árbol
  (por ejemplo `{"is_alpha": true}`).
- **Puntos de aparición en el mapa:** `CreatureSpawnPoint` (`Marker3D`, `scripts/ai/creature_spawn_point.gd`)
  colocados en `map.tscn` bajo `Fauna`: `@export scene`, `count`, `radius`, `alpha_count`, `spawn_at_start`,
  `respawn_s` (0 = no reaparece), `only_at_night`. Al empezar la partida el host aparece lo que toque.
- Los clientes que entran tarde reciben las criaturas vivas gracias al `MultiplayerSpawner`.

**Navegación:** `map.tscn` tiene `NavegacionTerreno` (`NavigationRegion3D`) con la malla horneada del terreno
y la cabaña (la puerta cuenta como paso). Se vuelve a hornear desde el editor si cambia el mapa.

**Efectos sobre el jugador** (`Player.apply_effect(kind: StringName, value: float, seconds: float)`): lo llama
el host y llega por RPC al dueño del jugador, cuyo rig aplica el efecto.
- `&"slow"`: multiplica la velocidad por `value` durante `seconds` (gallinas, fantasma).
- `&"knockback"`: empuje horizontal de `value` m/s en la dirección `Player.last_effect_dir` (oso, Krampus).
- En VR solo se aplica `slow` al movimiento por stick y un empuje suave del `XRToolsPlayerBody`.

**Oleadas** (`scripts/ai/wave_director.gd`, nodo `Oleadas` en `game.tscn`, grupo `wave_director`): solo host.
- Datos en `resources/waves/*.tres` (`WaveData`: fase, entradas `scene` + `count`, intervalo entre apariciones,
  extra por nivel de ira y por jugador). Aparecen en `MK_WaveSpawn_N/E/S/W`.
- Escucha `EventBus.night_started`, `AnomalyDirector.threat_requested(kind)` (`&"gnome"`, `&"ghost"`…) y
  `EventBus.krampus_wrath_changed`.
- Al amanecer los enemigos de oleada que sigan vivos huyen y desaparecen.

**Objetivos de los enemigos en la cabaña**
- Los renos van a la cabaña: si la puerta está cerrada la golpean (`Door.bash(damage)`, al llegar a 0 se abre
  sola); dentro, buscan la chimenea y le quitan combustible (`Fireplace.drain(seconds)`, solo host).
- **Krampus** (`scenes/ai/krampus/krampus.tscn`): aparece en `MK_KrampusSpawn` en la noche final y va a por el
  pastel (`MK_Pastel`). Si lo toca: `Cake.set_stolen()` + `EventBus.cake_stolen` y huye hacia su punto de
  aparición. Con suficiente daño retrocede aturdido y vuelve tras un tiempo. Si llega a su punto con el pastel
  se lo come y entra en **fase jefe** (persigue y ataca a los jugadores hasta el amanecer).

**Detalle de la base implementada (D5-01)** — completa lo de arriba; los nombres son exactos.
- Escena de referencia: `scenes/ai/test/criatura_prueba.tscn` (cópiala para empezar una criatura nueva) con
  su árbol `resources/ai/criatura_prueba.tres`. Hay un `CreatureSpawnPoint` de prueba en `map.tscn/Fauna/SpawnCriaturaPrueba`
  (25, 0, −30): quitarlo cuando haya fauna real.
- `Creature`, además de lo del contrato:
  - `@export faction` (`Faction.ANIMAL` → grupo `animals`, `Faction.ENEMY` → `enemies`); nodos `health`, `perception`,
    `nav_agent`, `bt_player`, `red_sync`; `acceleration`, `arrive_distance`, `repath_interval_s`, `floor_max_angle_deg`;
    efecto al golpear `attack_effect` (`&"slow"`/`&"knockback"`/vacío) + `attack_effect_value` + `attack_effect_seconds`;
    `home_position` (centro de paseo; por defecto donde aparece); `snap_distance`; sonidos `sound_alert`, `sound_attack`,
    `sound_hurt`, `sound_death` (suenan en todos los peers al cambiar `anim_state` a `alert`/`attack`/`hurt`/`dead`).
  - Replicado por `RedSync`: `net_position`, `net_yaw` (siempre, 20 Hz) y `anim_state` (al cambiar). El cliente interpola
    entre las dos últimas instantáneas. `RedSync` lleva un filtro de visibilidad: la criatura solo se envía a peers que ya
    cargaron la partida (`WorldItems.loaded_peers`); `CreatureSpawner` refresca la visibilidad cuando cargan.
  - Métodos: `move_to(pos, running := false, ignore_navigation := false)`, `stop()`, `face(pos)`, `has_arrived(distance := -1.0)`, `is_moving()`,
    `flat_distance_to(pos)`, `static target_position(node) -> Vector3` (pies del `Player` vía `body_transform`; el nodo
    `Player` no se mueve), `set_target(node)`, `get_target_player()`, `is_target_valid(node)`, `is_in_attack_range(node)`,
    `can_attack_now()`, `try_attack(player) -> bool`, `set_anim_state(state, hold_s := 0.0)` (host; `hold_s` evita que el
    estado automático idle/walk/run lo pise), `find_player_by_peer(peer)`, `is_host()`.
  - Estado de host: `target`, `last_attacker: Player` (último que la dañó), `dead`.
  - Para subclases (sobrescribir y llamar a `super` si procede): `_on_attack_hit(player)` (por defecto daño + efecto),
    `_on_damaged_host(amount, source_peer, reason_key)` (por defecto `hurt` + persigue al atacante),
    `_on_died_host(source_peer)` (por defecto suelta comida y programa el borrado), `_on_anim_state_changed(old, new)`.
  - Movimiento: con malla de navegación (`map_get_regions` no vacío) usa `NavigationAgent3D` y recalcula la ruta como mucho
    cada `repath_interval_s`; si no hay malla o la ruta no avanza, dirección directa. Siempre con gravedad y `move_and_slide`.
- `CreaturePerception` es un `Node3D` (su posición = los ojos). Extra: `proximity_radius` (nota a quien esté muy cerca),
  `check_interval_s` (0,25 s; reparte la fase al azar entre criaturas), `enabled`; `notice(player)`, `is_aware_of(player)`,
  `get_known_players()`, `is_perceivable(player)`.
- `CreatureModel` (`scripts/ai/creature_model.gd`, opcional en `Modelo`): lee `anim_state`; reproduce la animación del mismo
  nombre en su `animation_player` (o `animation_names[state]`) o, sin AnimationPlayer, una animación provisional barata.
- `CreatureSpawner`: `static get_instance(from)`, `get_creatures()`, señal `creature_spawned(creature)`; `params` solo admite
  tipos serializables (no `PackedScene`). `CreatureSpawnPoint`: además `params: Dictionary` (propiedades extra por criatura),
  `ground_probe_height` (apoya cada criatura en el suelo con un rayo a la capa 1) y `spawn_one(extra_params := {})`.
- **Tareas de LimboAI** (`scripts/ai/tasks/`; el agente es la `Creature`; `target` por defecto):

| Tarea | Tipo | Blackboard | Resultado |
|---|---|---|---|
| `BTFindTarget` | acción | escribe `target_var` | SUCCESS si la percepción tiene objetivo (mantiene el actual si `keep_current`); llama a `set_target` |
| `BTHasTarget` | condición | lee `target_var` | SUCCESS si el objetivo existe y está vivo |
| `BTTargetInRange` | condición | lee `target_var` | SUCCESS si está a `distance` m (0 = `attack_range`) |
| `BTMoveToTarget` | acción | lee `target_var` (Player o cualquier Node3D) | RUNNING acercándose; SUCCESS a `arrive_distance` (0 = 90 % de `attack_range`); `running`, `max_time` |
| `BTMoveToPosition` | acción | lee `position_var` (`Vector3`, por defecto `destination`) | RUNNING / SUCCESS al llegar; `arrive_distance`, `running`, `max_time` |
| `BTAttackTarget` | acción | lee `target_var` (Player) | SUCCESS al golpear; RUNNING esperando el cooldown; FAILURE fuera de rango |
| `BTWander` | acción | — | pasea a un punto al azar en `radius` de `home_position`; `running`, `max_time` |
| `BTFlee` | acción | lee `threat_var` (si no, `last_attacker`) | RUNNING huyendo hasta `safe_distance`; `flee_distance`, `jitter_deg` |
| `BTIsHealthBelow` | condición | — | SUCCESS si vida < `ratio` |
| `BTIsNight` | condición | — | SUCCESS de noche (`invert` = de día) |
| `BTSetAnimState` | acción | — | fija `anim_state` durante `hold_s` |
| `BTStopMoving` | acción | — | `stop()` |

  Con LimboAI: `BTWait`, `BTRandomWait`, `BTCooldown`, `BTDynamicSelector`/`BTDynamicSequence` (reevalúan las ramas de
  más prioridad cada tick: úsalos para que perseguir interrumpa pasear).
- Árboles `.tres`: se generan con `tools/ai/build_bt.gd` (ver cabecera; sobrescribe los que tiene registrados) o se crean
  a mano en el editor de LimboAI.
- **Jugador:** `player.tscn` tiene ahora `Salud` (`Health`, 100). `Player.apply_effect(kind, value, seconds, dir := Vector3.ZERO)`:
  `dir` opcional actualiza `last_effect_dir` antes de enviar; señal `effect_applied(kind, value, seconds)` en el dueño.
  PC: `PCRig.apply_effect(...)`, `get_speed_multiplier()`; el empuje quita el frenado en suelo `knockback_control_s` s.
  VR: `slow` baja `movement_direct.max_speed` y la velocidad agachado; `knockback` suma `knockback_scale_vr` (0,4) del
  empuje a la velocidad del `XRToolsPlayerBody`.
- **Puerta:** `Door.bash(damage)` (host) resta de `bash_left` (`@export bash_health` = 100); cada golpe sacude la hoja y suena
  `SonidoGolpes` (sin emitir `knocked`); a 0 se abre con `last_actor_peer = 0`; señal `bashed(remaining)`. Al cerrarla
  cualquiera, `bash_left` vuelve a `bash_health`. Ojo: si un enemigo la abre durante el evento de la regla 10, la regla lo
  cuenta como apertura (culpable 0).
- **Chimenea:** `Fireplace.drain(seconds)` (host) quita combustible y lo replica.
- **Navegación:** `resources/ai/navmesh_terreno.tres` se hornea con `tools/ai/bake_navmesh.gd` (celda 0,3 m, radio de agente
  0,6 m, área ±130 m, colisiones de las capas 1 y 6 de los nodos del grupo `navegacion_fuente`: `Terreno`, `Cabana`,
  `Recursos`, `Granja`). La herramienta quita la colisión de la puerta para que cuente como paso; el botón de hornear del
  editor usa los mismos ajustes pero con la puerta cerrada tapa el hueco.
- Pruebas: `scripts/test/test_ia.gd` (sin red) y `scripts/test/test_ia_lan.gd` (host + cliente en el mismo PC).

### Detalle día 5 (escenas, scripts y herramientas de árboles)

Fauna y enemigos reales sustituyen a la criatura de prueba: el punto `Fauna/SpawnCriaturaPrueba` ya no está en `map.tscn`
(ahora hay `SpawnVacasGranja`, `SpawnGallinasGranja`, `SpawnCerdosOeste`, `SpawnCerdoSureste`, `SpawnOsoNorte` y
`SpawnManadaLobos`); las pruebas que necesitan la criatura de prueba la hacen aparecer con `spawn()`.
`game.tscn` instancia además `Oleadas` (`scenes/ai/waves/wave_director.tscn`) y `Krampus` (`scenes/ai/krampus/krampus_director.tscn`).

| Criatura | Escena | Script / clase | Árbol (`resources/ai/`) | Herramienta |
|---|---|---|---|---|
| Vaca, gallina, oso | `scenes/ai/animals/cow.tscn`, `chicken.tscn`, `bear.tscn` | `scripts/ai/animals/animal.gd` (`Animal`) | `animals/vaca.tres`, `gallina.tres`, `oso.tres` | `tools/ai/build_bt_animals.gd` |
| Cerdo | `scenes/ai/animals/pig.tscn` | `scripts/ai/animals/pig.gd` (`Pig`) | `animals/cerdo.tres` | `tools/ai/build_bt_animals.gd` |
| Lobo | `scenes/ai/animals/wolf.tscn` | `scripts/ai/animals/wolf.gd` (`Wolf`) | `animals/lobo.tres` | `tools/ai/build_bt_animals.gd` |
| Gnomo explosivo | `scenes/ai/gnome/gnome.tscn` (+ `scenes/fx/gnome_explosion.tscn`) | `scripts/ai/enemies/gnome.gd` (`Gnome`) | `enemies/gnome.tres` | `tools/ai/build_bt_gnome_ghost.gd` |
| Fantasma | `scenes/ai/ghost/ghost.tscn` | `scripts/ai/enemies/ghost.gd` (`Ghost`, con `GhostLightProbe`) | `enemies/ghost.tres` | `tools/ai/build_bt_gnome_ghost.gd` |
| Reno zombi | `scenes/ai/reindeer/zombie_reindeer.tscn` | `scripts/ai/enemies/reindeer.gd` (`Reindeer`, objetivos en `ReindeerObjectives`) | `enemies/reindeer_zombi.tres` | `tools/ai/build_bt_reindeer.gd` |
| Rodolfo | `scenes/ai/reindeer/rudolph.tscn` | `scripts/ai/enemies/reindeer_rudolph.gd` (`Rudolph`) | `enemies/reindeer_rudolph.tres` | `tools/ai/build_bt_reindeer.gd` |
| Krampus | `scenes/ai/krampus/krampus.tscn` | `scripts/ai/krampus/krampus.gd` (`Krampus`) | `krampus/krampus.tres` | `tools/ai/build_bt_krampus.gd` |
| Director de Krampus | `scenes/ai/krampus/krampus_director.tscn` | `scripts/ai/krampus/krampus_director.gd` (`KrampusDirector`, grupo `krampus_director`) | — | — |
| Director de oleadas | `scenes/ai/waves/wave_director.tscn` | `scripts/ai/wave_director.gd` (`WaveDirector`), datos `WaveData`/`WaveEntry`, `resources/waves/noche_1.tres` y `noche_final.tres` | — | — |

Tareas propias de cada familia: `scripts/ai/tasks/animals/`, `scripts/ai/tasks/enemies/`, `scripts/ai/tasks/krampus/`.
Pruebas: `test_animales`, `test_gnomo_fantasma` (+ `_lan`), `test_oleadas`, `test_krampus` y `test_dia5_integrado` en `scripts/test/`.
Claves i18n de Krampus: `KRAMPUS_VISIT`, `KRAMPUS_ARRIVED`, `KRAMPUS_STOLE_CAKE`, `KRAMPUS_DROPPED_CAKE`, `KRAMPUS_BOSS`,
`KRAMPUS_DEFEATED`, `KRAMPUS_RETREATED`.

## GUI (día 6)

Estética "cuaderno de supervivencia" (papel envejecido, tinta, cinta, acentos navideños desvaídos). Todo se genera sin
descargas salvo las fuentes (`assets/fonts/`: IM Fell English y Caveat con `OFL.txt`; Special Elite con su `LICENSE.txt`
**Apache 2.0**, no OFL).

- **Tema:** `resources/ui/tema_cuaderno.tres` (páginas y menús) y `resources/ui/tema_ui.tres` (HUD; hoy idéntico). Las rutas
  antiguas `scenes/ui/tema_cuaderno.tres` y `scenes/ui/tema_ui.tres` son copias con el mismo UID para no romper escenas.
  No se editan a mano: se regeneran con `tools/ui/generar_tema.gd` (que necesita las texturas de `tools/ui/generar_texturas.gd`
  importadas). Orden: `generar_texturas.gd` → `--import` → `generar_tema.gd`. Capturas: `tools/ui/capturar_hud.gd`.
- **Fuentes (todas con fallback de sistema):** títulos IM Fell English (`TituloLabel`, `TituloCursivaLabel`), máquina de escribir
  Special Elite (por defecto, `SubtituloLabel`, `ReglaLabel`, `PequenoLabel`), mano Caveat (`NotaLabel`, `NotaRojaLabel`).
  Texto sobre el mundo: `EtiquetaHud` y `NumeroHud` (con contorno). Paneles de papel: `PanelContainer` por defecto,
  `PanelCompacto`, `PanelPagina` (rayado), `PanelCarton`, `CasillaHud`, `CasillaHudSeleccionada`. Botones: lápiz en reposo,
  tinta al pasar el ratón, tinta rellena al pulsar (`Button`, `BotonGrande`). También `CheckBox`, `HSlider`, `LineEdit`,
  `ScrollBar`, `PopupMenu`, `RichTextLabel`.
- **Paleta:** constantes en `scripts/ui/ui_palette.gd` (`UiPalette`: `PAPEL`, `TINTA`, `ROJO_APAGADO`, `VERDE_PINO`,
  `ORO_VIEJO`, `ESCARCHA`, `CALOR`, `CINTA`, `TEXTO_SOBRE_MUNDO`...) y los mismos valores en el tipo `Paleta` del tema
  (`theme.get_color(&"tinta", &"Paleta")`). Ayudas: `UiPalette.health_color(ratio)`, `UiPalette.format_time(seg)`.
- **Uso:** poner `theme = tema_cuaderno.tres` en la raíz de la escena y `theme_type_variation` en los nodos. Tamaños base
  legibles en VR (botones 24, títulos 40+); para paneles 3D (`SubViewport`) escalar el `Quad`, no bajar la fuente.
  Texturas en `assets/ui/` (`papel_*`, `cinta`, `casilla*`, `boton_*`, `icono_*`, `mira`).
- **HUD de PC (`HudPC`, `scenes/ui/hud_pc.tscn`):** el rig llama a `setup(rig, player)` / `clear()` / `show_message(key, seg, params)`
  como antes. Nuevo: `show_rules()` (nota de reglas a pantalla completa), `open_pause()` / `close_pause()`, `HudPC.action_key_text`.
  Muestra vida (`Health`), temperatura con icono de escarcha que se llena de abajo arriba por debajo de `frost_start` (70),
  fase (`PHASE_*`) + tiempo restante + icono sol/luna, objeto equipado (+ icono si `ItemData.icon`), munición, barra rápida,
  aviso de interacción y mensajes. `PhaseClock` (`scripts/ui/phase_clock.gd`) cuenta el tiempo en los clientes (solo el host
  decrementa `GameState.phase_time_left`).
- **Pausa (Esc):** `scenes/ui/pause_menu.tscn` (`PauseMenu`) dentro del HUD: Reanudar / Opciones / Salir al menú. El HUD la abre
  si el ratón está capturado y deja pasar el evento al rig (que suelta o recaptura el ratón); Reanudar simula la pulsación de
  Esc. Opciones carga perezosamente `res://scenes/menu/options_menu.tscn` si es un `Control` (si no, solo emite
  `options_requested`; escuchan `closed`/`back_requested` del hijo). Salir llama a `NetManager.leave()` y cambia a
  `scenes/menu/main_menu.tscn` (o `lobby_debug.tscn` si no existe).
- **Inventario (Tab):** `inventory_pc.tscn` es un libro de dos páginas (objetos con cantidad y reglas); API de `InventoryPC` sin cambios.
- **Nota de reglas:** `scenes/interactables/rules_note.tscn` (`RulesNote`, `XRToolsPickable` en capa 5 con `RedSync`). La hoja
  (`scenes/ui/rules_page.tscn`, `RulesPage`, 11 reglas `RULE_01..11`) se pinta una vez en un `SubViewport` 640x896
  (`UPDATE_ONCE`, se repinta al cambiar el idioma) sobre un quad de 0,22x0,31 m. PC: `interact(player)` abre
  `HudPC.show_rules()` (se cierra con E, Esc, Tab, clic o caminando). VR: se agarra y se lee de cerca. Está en `cabin.tscn`
  (`MesaNota/NotaReglas`, 2 m a la derecha de la puerta, sobre una cajonera).
- **Reloj de muñeca VR:** `scenes/player/vr/wrist_watch.tscn` (`WristWatch`, `scripts/player/wrist_watch.gd`) bajo
  `VRRig/XROrigin3D/LeftHand/RelojMuneca`, capa de render 4 (SoloVR). Se activa solo si `Player.active_rig` es su `VRRig`
  (si no, oculto y sin proceso). Cuatro actualizaciones por segundo: barra de vida (`Health.get_ratio`), barra de
  temperatura (calor/escarcha), icono de escarcha, icono de fase y `m:ss`. La posición/orientación en la muñeca
  (`transform` de `RelojMuneca`, `Esfera`) está sin probar en visor: ajustar en el editor.
- **Claves nuevas (pendientes de pasar a `textos.csv`):** `PAUSE_TITLE`, `MENU_QUIT_TO_MENU`, `NOTE_SCRIBBLE`, `NOTE_CLOSE_HINT`.
- Prueba: `godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/test_gui.gd`
  (usa `root.get_node` y empieza en el primer fotograma porque los autoloads no existen al compilar).

## Menú y opciones (día 6)

Escena principal: `res://scenes/menu/main_menu.tscn` (`MainMenu`, `scripts/menu/main_menu.gd`). `lobby_debug.tscn` sigue
funcionando como sala de pruebas independiente.

- **Menú 360°:** árbol de Navidad (`christmas_tree.tscn`) con tren de juguete (`train.tscn`, `MenuTrain`) sobre una vía
  cerrada (`Path3D`). El tren llega, se para y los tres regalos (`present_multi/play/options.tscn`, `MenuPresent`) saltan del
  vagón al suelo: izquierda Multijugador (`multiplayer`), centro Jugar (`play`), derecha Opciones (`options`). Cada regalo
  tiene un `MenuPick` (`StaticBody3D`, capas de física 7 y 21, señal `pointer_event` de XR Tools). Al elegirlo se reproduce
  la animación `abrir` (tapa) y se emite `MenuPresent.selected(action)`. Fondo: panorama 360 ya difuminado
  (`assets/menu/panorama_nevado.png`) más niebla de profundidad, sin post-proceso; nieve `GPUParticles3D` (700 partículas).
- **Acciones:** Jugar = `NetManager.play_offline()`; Multijugador = página lobby; Opciones = página opciones.
  Primer arranque (`Settings.language_confirmed == false`): primero aparece `language_picker.tscn` (ES/EN), luego la intro.
- **PC:** ratón con rayo físico (capa 7), la cámara (`VistaPC`) se mece con el ratón; flechas + Intro también eligen.
  **VR:** `VRRig` (`XROrigin3D`) con manos de XR Tools, rayo `function_pointer.tscn` (máscara capas 7 y 21) y un `Area3D`
  táctil por mano (`Tacto`, máscara 7). Se detecta VR con `NetManager.local_platform() != PC`; la vista que sobra se libera.
- **Páginas:** `MainMenu.open_page(&"language"|&"options"|&"lobby")` / `close_page()`. En PC se instancian dentro de la
  `CanvasLayer` `InterfazPC`; en VR son paneles 3D (`options_vr.tscn`, `lobby_vr.tscn`, `language_vr.tscn`, `MenuPageVR`)
  con un `panel_3d.tscn` (copia de `XRToolsViewport2DIn3D` sin su `ViewportTexture` vacío) que lleva el panel 2D dentro del
  `SubViewport`. Toda página emite `closed`.
- **Opciones:** panel `options_panel.tscn` (`OptionsPanel`, señales `closed` y `control_changed(index)`): pestañas Idioma,
  Gráficos, Audio, Controles y Confort VR con piezas técnicas (`DialKnob`, `LeverSelector`). Los cambios se aplican al
  momento y se guardan 0,6 s después y al cerrar (`Settings.save_settings()`). Cada cambio da un tirón a los engranajes
  (`options_gears.tscn`, `OptionsGears.kick(i)`). **Desde la pausa:** instanciar `res://scenes/menu/options_menu.tscn`
  (`OptionsMenu`, `Control` a pantalla completa con `SubViewport` propio, `process_mode = ALWAYS`), añadirlo a una `CanvasLayer`
  y conectar `closed`; quien lo abre lo libera.
- **`Settings` ampliado (`scripts/core/settings.gd`):** señal `changed` (se emite en `apply()` y en cada setter), `apply()` =
  `apply_language()` + `apply_audio()` + `apply_quality()`. Campos nuevos: `language_confirmed`, `vr_snap_angle` (15/30/45/60/90),
  `vr_height_offset` (m, -0,3..0,6). Calidad (`QUALITY_PRESETS`): MSAA 0/2x/4x, atlas de sombra 512/1024/2048, filtro de
  sombra, escala 3D con FSR 1 (0,7 en Baja, solo PC) y, con OpenXR, `render_target_size_multiplier` 0,8/1,0/1,1 y foveación
  3/2/1. `path` permite probar con otro archivo. **El `VRRig` debe leer:** `vr_smooth_move`, `vr_snap_turn`, `vr_vignette`
  (ya lo hace) y además `vr_snap_angle` → `movement_turn.step_turn_angle` y `vr_height_offset` → sumar a la altura del
  origen (`xr_origin.position.y` o `XRToolsUserSettings.player_height`), todo en su `_apply_settings` (ya conectado a `changed`).
  El rig de PC lee `mouse_sensitivity` e `invert_y`.
- **Herramientas:** `tools/menu/generar_texturas.gd` (panorama y papel), `tools/menu/construir_menu.gd` (genera las escenas del
  menú: ejecutar **sin** `--headless`, ver cabecera), `tools/menu/capturar_menu.gd` (capturas en `tools/logs/`).
  Prueba: `scripts/test/test_menu.gd`.
- **Claves i18n nuevas (pendientes de pasar a `textos.csv`):** `MENU_TITLE`, `MENU_CHOOSE_LANGUAGE`, `MENU_LANG_NATIVE_ES`,
  `MENU_LANG_NATIVE_EN`, `MENU_HINT_PC`, `MENU_HINT_VR`, `OPT_QUALITY`, `OPT_SNAP_ANGLE`, `OPT_VR_HEIGHT`, `OPT_VR_MOVE_MODE`,
  `OPT_VR_TURN_MODE`, `OPT_VR_NOTE`.

## Derribo, muerte y minijefe (día 6)

**Todo se decide en el host** y se replica por RPC; el dueño de cada jugador solo aplica efectos locales.

**Estados** (`Downed`, `scripts/player/downed.gd`, nodo `Derribo` de `player.tscn`, `Node3D` con `top_level` que sigue al cuerpo):
```
ALIVE  --(Salud a 0: golpe, explosión o frío)-----------------------------------> DOWNED
DOWNED --(un compañero mantiene "interactuar" revive_hold_s junto al cuerpo)-------> ALIVE (revive_health, temperatura revive_temperature)
DOWNED --(se acaba revive_window_s = 10 s)-----------------------------------------> DEAD  (banner para todos)
DEAD   --(los compañeros derrotan a su minijefe: revive_host(true, posición))--------> ALIVE (vida completa, donde cayó el minijefe)
```
- Mientras está `DOWNED` o `DEAD`, `Health.dead` es true: la IA lo ignora (`CreaturePerception`, `Creature.is_target_valid`) y no recibe
  más daño. Si otro sistema llama a `Health.revive()` directamente, `Derribo` vuelve a `ALIVE` solo.
- **Frío:** `Temperature` a 0 ya no emite `player_downed`: vacía la Salud con `cold_reason_key` (`DEATH_REASON_COLD`) y sigue el mismo flujo.
- `Player.downed_state: Downed` (export) y `Player.is_incapacitated() -> bool` (derribado o muerto). `Downed.find_for(player)`.
- API de host: `down_host(reason)`, `revive_host(full := false, at := Vector3.INF)`, `die_host()` (acaba un derribo ya). Consulta:
  `state`, `is_alive()`, `is_downed()`, `is_dead()`, `time_left`, `revive_progress`, `reviving`, `reason_key`. Señal `state_changed(new, old)`.
  `force_revive_hold(on)` simula la entrada de reanimar del jugador local (pruebas; el host sigue validando la distancia).
- Exports: `revive_window_s` (10), `revive_hold_s` (3), `pause_while_reviving` (la cuenta atrás se para mientras alguien reanima),
  `revive_decay_scale`, `revive_health` (40), `revive_temperature` (50), `revive_distance` (2 m entre pies), `revive_view_angle_deg` (PC, 50°),
  `revive_hand_reach` (VR, 1,1 m de una mano al pecho), `revive_grip_threshold`, `host_distance_tolerance`, `fall_time_s`, `lie_angle_deg`,
  `chest_height`, `sync_interval_s` (0,1 s).
- **Red:** `_rpc_state(state, time_left, reason, at, has_at)` (fiable, host → todos), `_rpc_progress` (no fiable, cada `sync_interval_s`
  mientras está derribado), `_rpc_hold(holding)` (quien reanima → host). Son `any_peer` (la autoridad del `Player` es su dueño) y se valida
  el remitente como en `Health`. El host valida que quien reanima esté vivo y a `revive_distance + host_distance_tolerance` con el estado
  sincronizado.
- **Entrada para reanimar** (la lee el `Derribo` del caído en el peer de quien reanima, sin tocar los rigs): PC = mantener `interact` (E) a
  menos de 2 m mirando al cuerpo; VR = mantener grip o gatillo con una mano cerca del pecho.
- **Señales `EventBus`** (las emite `Derribo` en todos los peers, con el estado ya aplicado): `player_downed(peer)`, `player_revived(peer)`
  (reanimado o vuelto del minijefe) y `player_died(peer, nombre, reason_key)`. `player_reincarnated(peer)` lo emite `DeathManager` cuando
  aparece el minijefe de ese jugador.

**Efectos locales**
- PC (`pc_rig.gd`): derribado = sin moverse (solo gravedad y frenado), sin objetos ni inventario, suelta lo agarrado; la cámara cae a
  `downed_eye_height` con `downed_roll_deg` en `downed_fall_time`. Muerto = `Derribo` desactiva el rig (`deactivate()`) y `DeathManager`
  pone la cámara de espectador; al revivir se reactiva y, si toca, se teletransporta.
- VR (`vr_rig.gd`): derribado o muerto = `enabled = false` en movimiento directo, sprint, teletransporte y agarre (girar sigue); suelta lo
  de las manos. La cámara XR no se toca (confort). Muerto: el espectador VR teletransporta el cuerpo detrás del compañero observado, a saltos.
- Remotos: el avatar se tumba hacia atrás mientras está derribado y desaparece mientras está muerto (reencarnado).

**Escenas de interfaz** (`scenes/ui/death/`, scripts en `scripts/ui/death/`; textos solo con `tr()`):

| Escena | Clase | Qué es |
|---|---|---|
| `revive_ring.tscn` | `ReviveRing` | Quad con `revive_ring.gdshader` (billboard, parámetros de instancia): arco exterior = tiempo, interior = progreso; `Label3D` con los segundos y el aviso `HUD_REVIVE` / `HUD_REVIVING`. Lo ven todos menos el caído. |
| `downed_vignette_pc.tscn` | `DownedVignettePC` | `CanvasLayer` con degradado radial rojo que late, `HUD_DOWNED` / `HUD_REVIVING` y `HUD_WAIT_FOR_REVIVE`. |
| `downed_vignette_vr.tscn` | `DownedVignetteVR` | Esfera de 0,2 m pegada a la cámara XR con `downed_vignette_vr.gdshader` (solo tiñe los bordes; capa 4 SoloVR) + `Label3D`. No es post-proceso. |
| `death_banner.tscn` | `DeathBanner` | Banner para todos: `DEATH_BANNER` + `DEATH_REASON_*` con `{name}`; en VR, `Label3D` colgado de la cámara. Encola varios. |
| `spectator.tscn` | `SpectatorCamera` | Cámara orbital (PC: ratón gira, clic izq./der. cambia de jugador) o saltos de teletransporte (VR: gatillo cambia). `HUD_SPECTATING`. |
| `end_panel.tscn` | `EndPanel` | Hoja de cuaderno con `VICTORY_*` / `GAMEOVER_*` y botón `MENU_BACK_TO_MENU` (VR: `Label3D` + gatillo). Señal `back_requested`. |

Los paneles de PC usan el tema `scenes/ui/tema_cuaderno.tres`.

**`DeathManager`** (`scripts/game/death_manager.gd`, escena `scenes/game/death_manager.tscn`, nodo `Muerte` en `game.tscn`, grupo
`death_manager`): solo escucha `EventBus`.
- Todos los peers: banner en `player_died`; espectador del jugador local muerto (se apaga en `player_revived`); panel final en
  `match_ended(victory)`; `go_to_menu()` (cierra la red con `NetManager.leave()` y carga `main_menu_scene` o, si no existe, `lobby_debug.tscn`).
- Host: al morir un jugador, si queda alguien en pie, crea su **minijefe** junto al cuerpo con
  `CreatureSpawner.spawn(miniboss_scene, xform, { "linked_peer": peer, "linked_name": nombre })` y guarda el vínculo
  (`get_miniboss(peer)`); RPC `_rpc_reincarnated` → `EventBus.player_reincarnated` + banner `MINIBOSS_RISEN`. Al morir el minijefe:
  `Derribo.revive_host(true, posición del minijefe)` + banner `MINIBOSS_DEFEATED` / `PLAYER_BACK_TO_LIFE`. Si su jugador se desconecta,
  el minijefe se queda como enemigo normal.
- Host: **fin de partida** cuando no queda ningún jugador `ALIVE` (nadie puede reanimar ni pelear): los derribados mueren ya (banner),
  no aparecen minijefes y a los `game_over_delay_s` (5 s) `GameState.end_match(false)`. **Un jugador solo:** derribo → muerte inmediata →
  banner → derrota (sin minijefe ni anillo). La victoria (`DAWN_WIN` al acabar la noche final) ya la hace `GameState`; aquí solo se muestra.
- Al empezar una partida nueva (`DAY_1`) se olvida la derrota pendiente y se oculta el panel.
- Exports: `miniboss_enabled`, `miniboss_scene`, `miniboss_spawn_offset` (2,5 m), `ground_probe_height`, `game_over_delay_s`,
  `main_menu_scene`, `fallback_menu_scene`. Señales `miniboss_spawned(peer, miniboss)`, `game_over_scheduled`.

**Minijefe** (`scenes/ai/miniboss/miniboss_gnome.tscn`, `MinibossGnome` en `scripts/ai/miniboss/miniboss_gnome.gd`, extiende `Creature`):
gnomo de jardín musculoso con primitivas (torso enorme, brazos gigantes, piernas diminutas, gorro rojo puntiagudo y poste de valla en la
mano derecha; materiales compartidos `gnomo_*.tres` + `resources/ai/miniboss/materials/`). Enemigo (capa 4), 300 de vida, 30 de daño,
alcance 2,1 m cada 1,6 s, `knockback` de 9 m/s, `DEATH_REASON_MINIBOSS`, no suelta comida. Exports propios `linked_peer`, `linked_name`
(llegan en los `params` del spawn) y un `Label3D` `Nombre` (`MINIBOSS_NAME` + nombre del jugador). Árbol LimboAI
`resources/ai/miniboss/miniboss_gnome.tres` (perseguir y golpear; si no ve a nadie, merodea), generado con `tools/ai/build_bt_miniboss.gd`.

**Pruebas:** `scripts/test/test_muerte.gd` (sin red) y `scripts/test/test_muerte_lan.gd` (host + cliente; ver cabeceras). Ambas añaden
`death_manager.tscn` a `game.tscn` si aún no lo trae.

**Claves nuevas (pendientes de pasar a `textos.csv`):** `HUD_REVIVING`, `HUD_WAIT_FOR_REVIVE`, `HUD_REVIVED`, `HUD_SPECTATING`,
`HUD_SPECTATING_NOBODY`, `HUD_SPECTATE_HINT`, `HUD_SPECTATE_HINT_VR`, `MINIBOSS_RISEN`, `MINIBOSS_DEFEATED`, `PLAYER_BACK_TO_LIFE`,
`GAMEOVER_TITLE`, `GAMEOVER_SUBTITLE`, `VICTORY_TITLE`, `VICTORY_SUBTITLE`, `MENU_BACK_TO_MENU`, `END_VR_HINT`. Ya existían y se usan:
`HUD_REVIVE`, `HUD_DOWNED`, `DEATH_BANNER`, `DEATH_REASON_*` (incl. `DEATH_REASON_MINIBOSS`), `MINIBOSS_NAME`, `MINIBOSS_REVIVE_HINT`.

## Construcción libre (S-06, S-07, S-08 del plan v2)

**Archivos**
```
scripts/build/build_grid.gd         BuildGrid: geometría de la rejilla (sin estado: celdas, niveles, claves, origen, giro).
scripts/build/build_piece.gd        BuildPiece (StaticBody3D): una pieza. Vida en `Salud`. La puerta se abre con `interact`.
scripts/build/build_system.gd       BuildSystem: validación (host y previsualización), colocar y recoger en red, libro de materiales.
scripts/build/build_controller.gd   BuildController (nodo `Controles`): modo construcción del jugador local y fantasma.
scenes/build/build_system.tscn      Instanciada en game.tscn como `Construccion`: BuildSystem + SpawnerPiezas + Controles.
scenes/build/pieza_{suelo,pared,puerta,escalera,techo}.tscn   Una escena por tipo (mallas y colisión simples).
scripts/ai/enemies/structure_breaker.gd, scenes/ai/structure_breaker/structure_breaker.tscn,
resources/ai/enemies/structure_breaker.tres                   Atacante de estructuras (S-08).
scripts/ai/tasks/structure/bt_find_breach.gd, bt_bash_breach.gd   Tareas BT del atacante.
```

**Rejilla.** Celda `build_cell_size` (2 m), nivel `build_level_height` (2,5 m), niveles `0..build_max_level`. Dos familias:
`h` (suelo, techo, escalera: ocupa una celda en un nivel) y `w` (pared, puerta: ocupa un borde de celda en un nivel; giro 0 = borde a lo largo de X, giro 1 = a lo largo de Z). El origen de cada pieza está en una esquina de celda, así que cae en múltiplos de la celda; la escalera gira alrededor del centro de su celda y su origen sigue en esquina. Clave única `familia:ix:nivel:iz:giro` (las `h` ignoran el giro).

**Validación.** `BuildSystem.plan_place` es la misma función en el host y en la previsualización. Orden: tipo válido; nivel en rango; sondeo del suelo (centro y cuatro puntos cerca de las esquinas, ignorando piezas; desnivel máximo `build_max_slope_m`); solape (misma clave); sostén (`h` a nivel > 0: muros del nivel de abajo en algún borde de la celda o una escalera vecina que suba; `w` a nivel > 0: suelo de ese nivel en una de las dos celdas que toca); alcance (host: pies del jugador a `build_reach_m` + 1,5 m en planta); coste (libro del host).

**Coste: confianza y límites.** El host no ve el inventario de los clientes (`Inventory` no se replica por RedSync; no se cambió). Cada jugador informa su recuento de materiales al host con RPC (`_rpc_report_counts`) al enlazarse su inventario y en cada `changed`. El host guarda ese libro por peer (`BuildSystem._ledger`) y:
1. El cliente comprueba el coste con su inventario y pide la colocación (`_rpc_request_place`).
2. El host valida el coste contra el libro de ese peer, lo descuenta del libro y crea la pieza (si no, `BUILD_NO_MATERIALS`).
3. El cliente cobra su inventario al recibir la aprobación. Si no puede cobrar, pide cancelar (`_rpc_request_cancel`): el host quita la pieza, devuelve el coste al libro y solo lo permite al peer que la colocó.
4. Recoger (host): quita la pieza, suma el coste al libro y el cliente lo recibe en su inventario.
**Límite honesto:** el libro lo alimenta el propio cliente. Un cliente modificado puede informar cantidades falsas; el host solo impide gastar más de lo que el cliente informó. Para impedirlo del todo hace falta que el inventario sea autoritativo en el host (cambio grande, fuera de esta fase).

**Vida (S-07).** `Salud` con `build_piece_health[tipo]`. A 0 el host quita la pieza sin devolver coste. Los atacantes usan `Health.apply_damage`.

**Puerta.** `interact` (E) pide al host abrir o cerrar; el estado se replica con `_apply_open` (call_local). Abierta: la hoja gira 90 grados y la colisión se desactiva.

**Atacantes (S-08).** `StructureBreaker` extiende `Creature`; no percibe ni ataca a jugadores. Árbol: BTFindBreach (pared o puerta viva más cercana, `anchor_world()`) + BTBashBreach (camina hasta `reach_m` y golpea con `bash_structure()` cada `structure_interval_s`); sin objetivos pasea (BTWander). Solo el host decide. El asedio no le asigna aberturas de la cabaña (`SiegeDirector._on_creature_spawned`).
Aparición: entradas en `resources/waves/noche_1.tres` y `noche_final.tres` con `WaveEntry.balance_count` = `build_breaker_count` (2) y `build_breaker_final_count` (3). Topes, refuerzos y amanecer (huyen) los gestiona `WaveDirector`.

**Controles.**
- PC: B activa o desactiva el modo; clic central o Enter colocan; clic lateral (atrás) o Supr recogen; Y gira; U cambia el tipo; RePág/AvPág cambian el nivel. Los clics izquierdo y derecho siguen con agarrar y lanzar (`pc_rig.gd` sin cambios).
- VR (mano derecha): A entra en modo construcción apuntando con ese mando; ya dentro, A coloca en el punto del puntero y B sale (`vr_rig.gd`, `_handle_build_button`). No actúa con el inventario abierto ni derribado. Sin probar en visor.

**Balance (`BalanceData`, grupo Construcción).** `build_cell_size`, `build_level_height`, `build_max_level`, `build_max_slope_m`, `build_reach_m`, `build_piece_costs` (tipos `floor`, `wall`, `door`, `stairs`, `roof`), `build_piece_health`, `build_breaker_count`, `build_breaker_final_count`. Las mallas de las escenas `pieza_*.tscn` tienen el tamaño de los valores por defecto.

**Previsualización.** Verde = colocación válida; rojo = no válida (`scenes/build/mat_fantasma_valido.tres`, `mat_fantasma_invalido.tres`). Captura: `tools/captura/captura_construccion.gd` -> `tools/logs/captura/construccion_fantasmas.png`.

**Pruebas.** `scripts/test/test_construccion.gd` (sin red). `scripts/test/test_construccion_lan.gd` (dos procesos: `-- host` y luego `-- client 127.0.0.1`).

**Pendiente.** Las puertas de la cabaña no son objetivo del atacante (solo las piezas construidas). Una pieza se puede recoger aunque otra se apoye en ella. Sin animaciones ni modelos definitivos. VR sin probar en visor.
