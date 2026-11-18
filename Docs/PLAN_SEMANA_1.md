# Plan de la semana 1: 12/12/12

> Estado: **propuesta, pendiente de aprobación.** No se ha creado nada todavía salvo este documento.
> Las preguntas iniciales ya tienen respuesta (sección 9). Quedan dos interpretaciones por confirmar (sección 9.1).

---

## 0. Resumen

- **Motor:** Godot **4.7.x estable** (salió en junio de 2026; `project.godot` ya declara 4.7 + renderer *Mobile*). GDScript.
- **Objetivo de la semana:** un vertical slice jugable de 15 minutos en PC y VR (PCVR + Quest 2) con multijugador **solo LAN de hasta 4 jugadores**, las 11 reglas con su director de anomalías, el ciclo día/noche, el sistema de frío y fuego, la IA básica, el menú 360, el juego en español e inglés, y el sistema de muerte, reanimación y minijefe.
- **Semana 2 (resumen en la sección 8):** pulido, integración de audio (la carpeta que vas a pasar), animaciones, el jefe Krampus completo y las pruebas en el torneo.
- **Mi rol:** código, organización del proyecto y GUI. **No genero música ni sonido.** El único modelo que genero es el **mapa nevado vacío** (sección 5). El resto va a una lista para diseñadores o para Tripo3D (sección 6).
- **Regla de oro:** todo se puede editar en el editor de Godot (sección 3).

---

## 1. Entorno de desarrollo

### 1.1 Software

> **Verificado el 2026-10-31:** ya está todo instalado. Godot 4.7.2 (Steam), sus plantillas de exportación, Blender 5.2.3, JDK 21, Android SDK, SteamVR y Meta Quest Link. Las rutas están en `CLAUDE.md`. El plugin OpenXR Vendors 5.1.0 se copia de tu proyecto `ocean-vr`, sin descarga.

| Herramienta | Versión | Para qué |
|---|---|---|
| Godot Engine (estándar, no .NET) | 4.7.x stable | Motor |
| Plantillas de exportación de Godot | Las mismas de 4.7.x | Builds de Windows y Android (Quest) |
| OpenJDK | 17 | Build Gradle para Quest |
| Android SDK (cmdline-tools, build-tools 34+, platform-tools) | Reciente | Export a Quest y `adb` |
| Meta Quest Developer Hub + modo desarrollador en el Quest 2 | Reciente | Instalar el APK y medir FPS (Performance HUD / OVR Metrics) |
| SteamVR **o** Meta Quest Link | Reciente | PCVR |
| Blender | 4.x con el add-on MCP conectado (ya está en esta sesión) | Generar el mapa nevado |
| Git | Ya está | Control de versiones (ver la nota de `safe.directory` en la sección 9) |

### 1.2 Plugins de Godot (addons)

| Addon | Uso en 12/12/12 | Prioridad | Fuente |
|---|---|---|---|
| **Godot XR Tools** | Movimiento VR, mano física, agarre (`XRToolsPickable`), bolsillos/snap zones, interactuables (puertas, botones), teletransporte opcional | P0 | [GodotVR/godot-xr-tools](https://github.com/GodotVR/godot-xr-tools/releases) |
| **Godot OpenXR Vendors** | Export al Quest 2 (manifest de Meta, foveación y refresco) | P0 | [GodotVR/godot_openxr_vendors](https://github.com/godotvr/godot_openxr_vendors) |
| **Godot Meta Toolkit** (opcional) | Configura automáticamente el export de Meta | P2 | [anuncio](https://godotengine.org/article/godot-xr-update-feb-2025/) |
| **netfox** (core + extras) | Sincronización de jugadores, interpolación y RPC ordenados sobre ENet | P0 | [foxssake/netfox](https://github.com/foxssake/netfox) |
| **netfox.noray** | NAT punch / relay para jugar por internet. **No se instala por ahora:** el MVP es solo LAN | — | idem |
| **LimboAI** (GDExtension) | Árboles de comportamiento y máquinas de estado **editables visualmente** para animales, enemigos y Krampus | P0 | [limbonaut/limboai](https://www.github.com/limbonaut/limboai) |
| **ProtonScatter** | Esparcir pinos, rocas y arbustos sobre el mapa de forma editable, con MultiMesh (barato en Quest) | P1 | [HungryProton/scatter](https://zff.dev/malgreen/proton_scatter) |
| **Jolt Physics** | Ya viene integrado en Godot 4.4+. Solo hay que activarlo en Project Settings | P0 | integrado |
| **Terrain3D** | **No para la semana 1.** No encontré pruebas de que funcione en Quest con el renderer Mobile. Uso una malla de Blender y dejo el heightmap listo por si migramos | — | [TokisanGames/Terrain3D](https://github.com/TokisanGames/Terrain3D/releases/tag/v1.0.2-stable) |

> Antes de instalar cada addon verifico que su release declare compatibilidad con Godot 4.7. Si no la declara, lo pruebo en una escena aislada. No encontré una release de XR Tools que mencione 4.6/4.7 de forma explícita, así que eso se valida el día 1.

### 1.3 Herramientas para Claude (skills, MCP)

| Herramienta | Uso |
|---|---|
| **Blender MCP** (ya conectado) | Generar y texturizar el mapa y exportarlo a GLB |
| **Godot MCP** ([Coding-Solo](https://github.com/ee0pdt/Godot-MCP) o [Claude-GoDot-MCP](https://github.com/DaRealDaHoodie/Claude-GoDot-MCP)) | Abrir el editor, ejecutar el proyecto y leer errores de consola sin que tengas que copiarlos. Recomiendo la variante gratuita de "lanzar + depurar". Las escenas se escriben como `.tscn` de texto, que el editor abre normalmente |
| Skill **frontend-design** + **vectorlab typography / colour-palette / motion** | Dirección visual del menú y del HUD (paleta, jerarquía, animaciones) |
| Skill **superpowers:subagent-driven-development** | Ejecutar el plan por tareas con subagentes y revisión entre tareas |
| **Skill propia del proyecto** (se crea el día 1 con skill-creator): `godot-12-scene-rules` | Reglas de escenas editables, convenciones de nombres y capas. Todo subagente la carga, así los modelos baratos no "esconden" nodos en código |

### 1.4 Assets CC0 o libres de apoyo (no comerciales, permitidos)

- **Poly Haven / ambientCG:** texturas PBR de nieve, roca, madera y hielo, y HDRI nocturno.
- **Kenney** (CC0): iconos de engranajes y UI para Opciones, y fuentes de prototipo.
- **Quaternius** (CC0): packs de animales animados (vaca, cerdo, lobo, ciervo) como **plan B** si Tripo se retrasa.
- **Google Fonts:** fuentes para el estilo "cuaderno" de The Forest (ver la sección 7).
- **Mixamo:** animaciones (caminar, correr, atacar, morir) para tus modelos con rig de Tripo.

---

## 2. Orquestación por modelos (ahorro de tokens)

Yo (Opus) actúo como **orquestador**. Primero escribo los **contratos**: autoloads, señales, nombres de nodos, capas y clases base. Después reparto las tareas cerradas a los subagentes según la dificultad. Cada tarea delegada lleva su lista de archivos, su "hecho cuando..." y la skill `godot-12-scene-rules`.

| Nivel | Modelo | Qué hace |
|---|---|---|
| **Pesado** | **Opus 5.5** | Arquitectura y contratos. Jugador híbrido PC/VR. Red (netfox, autoridad, lobby). Mapa en Blender. Krampus (jefe). Shaders y rendimiento en Quest (75 FPS). Revisión final de cada día |
| **Medio** | **Sonnet 5.5** | Sistemas de juego: inventario, crafteo, reglas, ciclo día/noche, IA de animales y enemigos con LimboAI, GUI (menú 360, cuaderno, opciones), muerte, reanimación y minijefe |
| **Ligero** | **Haiku 5.5** | Recursos `.tres` de los 13 objetos y recetas, configuración de import de GLB, Input Map, escenas "tontas" (puertas, ventanas, cuadros, mesa), cablear señales, actualizar `ESTADO.md` y documentación |

**Estimación de reparto:** ~25 % Opus, ~50 % Sonnet, ~25 % Haiku. Las tareas paralelas sin archivos compartidos se lanzan juntas, y una tarea que escribe código nunca comparte archivos con otra al mismo tiempo.

---

## 3. Regla: escenas editables

1. **Cada entidad es una escena `.tscn`** con sus nodos visibles en el árbol: mallas, colisiones, áreas, `Marker3D`, luces y partículas. Nada de `add_child(MeshInstance3D.new())` para construir cosas.
2. **El código solo instancia escenas existentes** (`preload("res://...tscn").instantiate()`) para spawns y oleadas. Los puntos de spawn son `Marker3D` colocados en el mapa.
3. **Parámetros con `@export`** (vida, velocidad, daño, tiempos de reglas) y datos en **Resources `.tres`** (objetos, recetas, oleadas). Se ajustan desde el Inspector.
4. **IA en árboles de LimboAI** (`.tres`), que se editan en su editor visual.
5. **GUI construida con nodos `Control`** dentro de escenas, con un **Theme `.tres`** compartido. Nada de UI generada por código.
6. **`@tool` solo para vistas previas** (por ejemplo, ver el radio de un área en el editor), nunca para generar contenido oculto.
7. Ningún nodo queda con `visible = false` "porque sí". Si algo empieza oculto, lo dice su nombre (`HiddenUntilNight`) y se puede activar desde el editor.

**Capas de render y física** (fijadas el día 1):

- Render: 1 Mundo, 2 Viewmodel (objetos en mano), 3 UI 3D, 4 Solo-VR, 5 Solo-PC.
- Física: 1 Mundo, 2 Jugador, 3 Animal, 4 Enemigo, 5 Agarrable, 6 Recurso, 7 Interactuable, 8 Trigger.

---

## 4. Arquitectura base (contratos del día 1)

**Autoloads:**

- `EventBus`: señales globales (`rule_broken(id)`, `player_died(name, reason)`, `night_started`...).
- `GameState`: fase, tiempo restante (15 min), día/noche, "ira de Krampus".
- `NetManager`: host o unión, lista de jugadores, plataforma de cada uno (PC, PCVR o Quest).
- `RuleManager`: 11 nodos-regla hijos, uno por regla, cada uno en su propia escena.
- `Settings`: idioma (ES/EN), calidad, volumen por bus, sensibilidad, movimiento VR (suave o teletransporte) y giro (por pasos o suave).
- `AnomalyDirector`: escucha `rule_broken` y la "ira de Krampus" y dispara anomalías por niveles (sección 4.1).

**Idiomas:** todo texto visible usa claves de traducción (`tr("RULE_01")`) desde el primer día, en una tabla `resources/i18n/textos.csv` con columnas `es` y `en`. Godot la importa como traducción. Nada de texto escrito directamente en escenas ni scripts.

### 4.1 Consecuencias de romper reglas: director de anomalías

Cada regla rota sube la **ira de Krampus** y dispara anomalías por todo el mapa. Las anomalías son escenas sueltas (`scenes/events/anomalies/`) y la tabla de qué anomalía corresponde a cada nivel es un `.tres` editable.

| Nivel de ira | Ejemplos de anomalías |
|---|---|
| 1, inquietud | Luces que parpadean, cuadros que cambian, huellas en la nieve que llegan a la puerta, árboles que aparecen en otro sitio, la nieve se vuelve roja un instante |
| 2, glitches | Objetos que tiemblan o se teletransportan, mallas que se deforman (shader de vértices), siluetas en la linde del bosque que desaparecen al mirarlas, un doble del jugador a lo lejos |
| 3, amenaza | Aparecen enemigos nuevos (gnomos y fantasmas de día), sube la dificultad de la siguiente oleada y se activa el **péndulo del cascabel** |
| 4, castigo | Krampus se adelanta, el fuego pierde fuerza más rápido, apagón de la cabaña |

**Rendimiento en VR:** un glitch a pantalla completa es caro y marea en VR. En el visor, los glitches ocurren **en el mundo** (vértices, materiales y luces). La distorsión de pantalla queda solo para PC.

### 4.2 Frío y fuego

- **Barra de temperatura** por jugador. Baja fuera de la cabaña y sube cerca de la chimenea o de una fogata. A cero, el jugador queda derribado (cuenta como derribo normal, con reanimación de 10 s).
- **Chamarra (armadura contra el frío):** de **día** permite estar fuera sin congelarse, o casi.
- **De noche** la chamarra no alcanza: para salir **es obligatorio** llevar una **fuente de luz de fuego** (antorcha o farol encendido en la chimenea). Sin ella, la temperatura cae en segundos.
- **Mientras llevas el fuego no puedes usar armas ni objetos.** Propuesta: la antorcha es un objeto **agarrable del mundo** (no ocupa uno de los 13 huecos del inventario). En PC ocupa la mano: para disparar tienes que soltarla. En VR, ver la sección 9.1.
- La antorcha se consume poco a poco y se reaviva en la chimenea o en una fogata.

**Estructura de carpetas:**

```
res://
  addons/            (plugins)
  assets/            (modelos tal como llegan, más textures/, fonts/, ui/)
  audio/             (vacía, para tu carpeta, con buses Master/Music/SFX/Voice/Ambience)
  scenes/
    main/            (Main, Boot/ShaderWarmup, Loading)
    menu/            (MainMenu360, Notebook, Options, Lobby)
    world/           (Map, Cabin, Farm, Lake, DayNight)
    player/          (Player, PCController, VRController, Hands, Viewmodel)
    items/           (una escena por objeto)
    interactables/   (Door, Window, WindowCover, Workbench, Picture, GreenButton, Cake, Fireplace, Tree, MilkTable)
    npc/animals/     (Pig, Bear, Cow, Wolf, WolfAlpha, Chicken, Livestock)
    npc/enemies/     (ZombieReindeer, Rudolph, Gnome, Ghost, Krampus, MiniBoss)
    events/          (BellPendulum, Waves)
    ui/              (HUD, DeathBanner, Subtitles, Inventory)
  scripts/           (espejo de scenes/ y core/)
  resources/         (items/, recipes/, waves/, ai/, themes/)
```

**Jugador híbrido:** una sola escena `Player.tscn` con `XROrigin3D`. Al arrancar, si OpenXR se inicializa, activa `VRController`. Si no, activa `PCController` (CharacterBody3D, ratón y teclado). Las dos variantes están en el árbol y se pueden editar.

**Red:** host autoritativo (el host también juega). Se sincronizan la posición y la rotación de la cabeza y las manos, los objetos agarrados, el estado de las reglas y la IA. La IA corre solo en el host y los clientes interpolan.

---

## 5. Lo que genero yo: el mapa nevado vacío (Blender MCP → GLB)

**Especificación propuesta (se ajusta con tus respuestas):**

- **Área jugable** de unos 260 × 260 m, compacta para partidas de 15 min. Muros invisibles de colisión en el borde, disimulados con niebla y pendiente.
- **Anillo de montañas** decorativo de unos 600 m de radio. Malla aparte, low-poly, sin colisión, con silueta marcada contra el cielo nocturno.
- **Zonas planas preparadas:** explanada de la cabaña de 30 × 30 m en el centro, granja de 40 × 30 m al oeste y lago no congelado de unos 50 × 35 m al noreste, con orilla suave, hueco para el agua y un `Marker3D` del centro. Caminos de nieve pisada que conectan las zonas.
- **Sin objetos:** solo terreno y montañas. Los árboles y las rocas vienen después con ProtonScatter.
- **Texturizado:** splat por *vertex color* (R nieve fresca, G nieve pisada o camino, B roca o tierra helada). Shader de terreno en Godot de 3 capas con triplanar solo en roca, más barato que el triplanar completo. Texturas de Poly Haven/ambientCG empaquetadas en **ORM** (AO + rugosidad + metálico), 2K en PC y 1K en Quest con override de import.
- **Listo para iluminación:** UV2 para LightmapGI, normales suavizadas, AO baked en vertex alpha como respaldo y escala real en metros.
- **Rendimiento:** terreno en 4 × 4 chunks con LOD automático de Godot (unos 60k triángulos en total en PC, ~25k visibles en Quest) y colisión simplificada aparte.
- **Archivos:** `assets/map/terrain.blend` (fuente), `assets/map/terrain.glb`, `assets/map/mountains.glb` y `assets/map/heightmap.png` (16 bits, por si migramos a Terrain3D). En Godot queda `scenes/world/Map.tscn` con todo visible y editable: materiales como `.tres`, `WorldEnvironment`, `DirectionalLight3D` (luna/sol) y marcadores de zona.

---

## 6. Lista de modelos 3D

> **Fáciles:** instrucciones para tus diseñadores 3D. **Difíciles:** prompt para Tripo3D, en inglés porque Tripo responde mejor así.
> **Presupuesto Quest 2:** props 300–1.5k tris, armas 2–4k, animales 3–6k, enemigos 5–8k, Krampus ≤ 12k. Texturas de 1K (2K solo Krampus y armas en mano). GLB con PBR (BaseColor, ORM y normal), escala en metros, pivote en la base (props) o en el agarre (objetos de mano), eje +Y arriba y −Z adelante.

### 6.1 Ya entregados (revisión)

| Modelo | Estado | Acción |
|---|---|---|
| `player.glb` (Tripo) | Rig de 29 huesos, ~17k tris, 4 materiales, **sin animaciones** | Bajar a ~8k tris para avatares remotos en Quest, unir en 1–2 materiales y añadir animaciones de Mixamo (idle, caminar, correr, agacharse, morir, ser reanimado) |
| `krampus demon 3d model.glb` (Tripo) | Rig de 65 huesos, ~19k tris, **sin animaciones**, 11 MB | Bajar a ~12k tris y texturas a 2K. Animaciones de Mixamo o a mano: caminar pesado, correr a cuatro patas, golpe, agarrar, rugido, comer pastel y fase jefe |

### 6.2 Fáciles: instrucciones para diseñadores

| # | Modelo | Instrucciones |
|---|---|---|
| 1 | **Cabaña modular** | Kit modular en rejilla de 2 m: pared lisa, pared con ventana, pared con puerta, esquina, suelo, techo inclinado y chimenea exterior. Troncos de madera oscura con nieve en los bordes del techo. Cada pieza menor de 800 tris. Atlas de texturas compartido de 1K. Las ventanas y la puerta son **huecos**: los paneles van aparte |
| 2 | **Puerta** | 1 × 2.1 m de madera con bisagras de hierro. Pivote **en la bisagra**. Pomo como objeto separado. ~400 tris |
| 3 | **Ventana y contraventana** | Marco de 1 × 1 m más hoja de cristal separada. "Cubierta" de tablones clavados con 3 variantes de mejora (madera, madera reforzada, metal) |
| 4 | **Mejoras de crafteo** | Barricada de troncos, pinchos de madera, refuerzo metálico de pared y puerta reforzada. Cada una con versión "fantasma" (misma malla, la uso con material translúcido) |
| 5 | **Mesa de trabajo** | Banco de madera con tornillo de banco y herramientas colgadas. ~1.2k tris |
| 6 | **Chimenea interior** | Hogar de piedra con hueco para leña y repisa. El fuego **no** va en la malla (es partícula). Hace falta un `socket` de troncos |
| 7 | **Pastel** | Pastel navideño de 3 pisos con cerezas, sobre plato. Versión entera y versión mordida |
| 8 | **Botón verde** | Caja metálica antigua con un botón verde grande tipo "seta" y la tapa abierta. El botón es un objeto aparte para animarlo |
| 9 | **Mesa de leche y galletas** | Mesita, vaso (malla aparte, con un "nivel de leche" como objeto separado) y plato con 4 galletas sueltas |
| 10 | **Cuadros (×4)** | Marco ornamentado de 0.6 × 0.8 m. Lienzo como malla aparte para cambiar la textura (versión normal y "cambiada" perturbadora). Pivote en el centro para voltearlo |
| 11 | **Árbol de Navidad** | Pino decorado de 2.5 m con 3 materiales: hojas, adornos (con emisión) y estrella. Adornos colocables sueltos (6 variantes) |
| 12 | **Regalos del menú (×3)** | Cajas con lazo, tapa separada para animarla al abrir: rojo (Multijugador), verde (Jugar) y dorado (Opciones). Cada una con un detalle temático (dos figuras, un muñeco y un engranaje) |
| 13 | **Tren de juguete y vías** | Locomotora de vapor más 3 vagones planos (cada uno lleva un regalo) y un círculo de vías de 4 m de radio. Estilo juguete antiguo de madera pintada |
| 14 | **Engranajes (×5)** | Engranajes de latón y hierro de distintos tamaños para la escena de Opciones. Pivote centrado en el eje para rotarlos |
| 15 | **Hacha** | Mango de madera de 70 cm y hoja de hierro. Pivote en el agarre. ~1.5k tris |
| 16 | **Pico** | Igual que el hacha, cabeza de hierro doble |
| 17 | **Linterna** | Linterna metálica antigua de mano. La lente es un material emisivo aparte. Pivote en el agarre |
| 18 | **Comida y bebida** | Manzana, zanahoria, papa, filete crudo, filete cocido, botella de agua, botella de leche y caja de galletas. ~300 tris cada uno |
| 19 | **Munición** | Caja de cartuchos de escopeta y caja de balas de pistola, con versión "suelto" (1 cartucho) |
| 20 | **Recursos** | Tronco cortado (madera), 3 rocas, veta de mineral (roca con cristales) y tocón de árbol |
| 21 | **Pinos (×3) y arbustos (×2)** | Pinos nevados low-poly de 6, 9 y 12 m con LOD (alto, medio y billboard). Clave para el rendimiento |
| 22 | **Granja** | Cerca de madera modular de 2 m, puerta de cerca, comedero y establo pequeño abierto |
| 23 | **La nota** | Hoja de papel envejecido (plano curvado). La textura la hago yo con las 11 reglas |
| 24 | **Cascabel de evento** | Cascabel dorado grande colgado de una cadena. Pivote en el extremo superior de la cadena (péndulo) |
| 25 | **Auto del vendedor** (trailer) | Sedán viejo low-poly. Opcional, P2 |
| 26 | **Antorcha y farol de fuego** | Antorcha de madera con trapo enrollado en la punta y farol de aceite de hierro. La llama **no** va en la malla (es partícula), solo un `socket` en la punta. Pivote en el agarre. ~600 tris cada uno |
| 27 | **Fogata exterior** | Círculo de piedras con troncos cruzados y ceniza. La llama es partícula. Versión apagada (troncos quemados). ~1k tris |

### 6.3 Difíciles: prompts para Tripo3D

> Tras generarlos: **auto-rig de Tripo (opción esqueleto Mixamo)**, exportar GLB con "Export Skeleton" y "Bottom-Center Pivot" activados y pedir una versión **low-poly** (objetivo de tris entre paréntesis). Las animaciones se sacan de Mixamo o de las animaciones predefinidas de Tripo.

1. **Cerdo** (×3 tamaños por escala, 4k)
   `Stylized realistic wild boar-pig hybrid, frantic aggressive expression, muddy pink-gray skin with dark bristly hair, small tusks, winter dirt and snow on legs, quadruped standing pose, game-ready low poly, PBR textures, horror Christmas game`
2. **Oso** (6k)
   `Large brown grizzly bear with frost on its fur, scarred face, long claws, heavy slow posture, quadruped neutral standing pose, realistic stylized, game-ready low poly, PBR`
3. **Vaca** (4k)
   `Highland-style cow with thick shaggy winter coat, docile expression, brown and white, small horns, quadruped standing pose, game-ready low poly, PBR`
4. **Lobo** (4.5k) y **Lobo alfa** (5k)
   Lobo: `Gray timber wolf, lean and hungry, snow on fur, quadruped neutral pose, game-ready low poly, PBR`
   Alfa: `Massive black alpha wolf, larger and scarred, glowing pale eyes, torn ear, thick mane, quadruped neutral pose, game-ready low poly, PBR`
5. **Gallina** (1.5k)
   `Plump farm hen, white and brown feathers, slightly menacing beady eyes, standing pose, stylized realistic, game-ready low poly, PBR`
6. **Reno zombificado distorsionado** (7k)
   `Undead zombie reindeer, emaciated body with exposed ribs, broken twisted antlers, rotting gray fur, unnaturally long legs, distorted bent neck, blank white eyes, horror creature, quadruped neutral pose, game-ready low poly, PBR`
7. **Rodolfo zombificado, alfa** (8k)
   `Zombie Rudolph the reindeer, larger alpha version, glowing infected red nose, torn festive harness with rusty bells, rotting flesh and fur, massive cracked antlers, menacing horror creature, quadruped neutral pose, game-ready low poly, PBR`
8. **Gnomo explosivo** (3k, **sin rig**: no se mueve mientras lo miras)
   `Creepy porcelain Christmas garden gnome, cracked ceramic face, wide unsettling painted grin, red pointed hat, holding a small lit present like a bomb, horror, standing still, game-ready low poly, PBR`
9. **Fantasma de Navidad** (3k, malla para shader translúcido)
   `Victorian ghost of Christmas past, floating tattered robes fading into wisps at the bottom, hollow dark eyes, long thin arms reaching forward, ethereal, horror, T-pose arms, game-ready low poly`
10. **Minijefe: gnomo de jardín musculoso (jugador muerto reencarnado)** (8k). Debe distinguirse del gnomo explosivo: más grande, con cuerpo humanoide y rig completo.
    `Hulking muscular garden gnome brute, huge bodybuilder arms and chest, tiny legs, cracked painted ceramic skin with exposed rough clay underneath, long dirty white beard, torn red pointed hat, furious grin, broken garden fence post as a weapon, horror Christmas miniboss, T-pose, game-ready low poly, PBR`
11. **Escopeta** (3.5k)
    `Old double-barrel pump shotgun, worn wooden stock, scratched dark metal, realistic, first-person game weapon, game-ready low poly, PBR`
12. **Desert Eagle** (3k)
    `Desert Eagle pistol, worn silver-gray finish with scratches, realistic, first-person game weapon, game-ready low poly, PBR`
13. **Armadura contra el frío** (5k, objeto de inventario más prenda visible)
    `Heavy winter survival coat with fur-lined hood, leather straps and patches, worn wool, laid flat pickup item, game-ready low poly, PBR`
14. **Krampus fase jefe** (opcional: variante de tu modelo)
    `Krampus giant boss form after eating a cake, goat-demon, black fur, enormous curled horns, rusty chains, sack on back, long red tongue, frosting smeared on face, burning eyes, T-pose, game-ready, PBR`

---

## 7. GUI: dirección visual

- **Estética:** "cuaderno de supervivencia" estilo **The Forest**: papel envejecido, tinta, cinta adhesiva, fotos Polaroid y bocetos a lápiz, con acentos navideños desvaídos (rojo apagado, verde pino y dorado viejo). Fuentes propuestas: *IM Fell English* (títulos), *Special Elite* (máquina de escribir para reglas y subtítulos) y *Caveat* (notas a mano).
- **Menú de inicio 360 (VR y PC iguales):** un tren de juguete trae los 3 regalos en vagones y se detiene. Árbol de Navidad al centro. Regalos en el suelo: izquierda Multijugador, centro Jugar, derecha Opciones. Fondo **difuminado a medias** sin post-proceso caro: panorama 360 pre-difuminado más niebla de profundidad, y nieve cayendo alrededor. En PC se apunta con el ratón y la cámara gira un poco con él. En VR se toca el regalo con la mano o se apunta con el rayo. Al elegir, la tapa se abre.
- **Opciones:** escena 3D con engranajes girando detrás de un panel de cuaderno. Los controles se ven como piezas técnicas (diales y palancas) y cada cambio gira un engranaje.
- **VR, mínimo de menús:** inventario = bolsillos físicos (cadera ×2, pecho, espalda para el arma larga). Las reglas se leen en la nota física. Sin HUD: la vida se ve en un reloj de muñeca diegético.
- **PC:** HUD mínimo (vida, frío y objeto en mano), inventario en cuaderno con la tecla Tab, pausa con Esc (cuaderno) y opciones.
- **Muerte:** viñeta roja, animación del jugador cayendo y **banner con subtítulo para todos**: "*Carlos* fue arrastrado por Krampus". El sonido queda conectado a un `AudioStreamPlayer` vacío hasta que llegue tu carpeta.
- **Derribo:** el jugador cae, aparece un anillo de 10 s sobre su cuerpo y un compañero mantiene "interactuar" para reanimarlo. Si se acaba el tiempo, muere y **reencarna en el minijefe** (gnomo de jardín musculoso). Derrotarlo lo revive.
- **Temperatura:** en PC, un icono de escarcha que se va cubriendo junto a la vida. En VR, el reloj de muñeca más escarcha en los bordes de la vista (un cristal con textura, no un post-proceso).
- **Idioma:** selector ES/EN en Opciones y en el primer arranque.

---

## 8. Calendario de la semana

Cada día termina con una build que se puede ejecutar en PC, una revisión de Opus, `ESTADO.md` actualizado y un commit en su rama. **P0** = imprescindible para el MVP, **P1** = importante, **P2** = se corta si vamos tarde.

### Día 1: entorno, contratos y mapa
| Tarea | Modelo | Prioridad |
|---|---|---|
| Verificar Godot 4.7, plantillas, JDK y SDK. Export de Windows y Quest vacío probado en el visor | Haiku (guía) + tú | P0 |
| Instalar addons (XR Tools, Vendors, netfox, LimboAI, ProtonScatter) y activar Jolt | Haiku | P0 |
| Contratos: autoloads, capas, Input Map, estructura y skill `godot-12-scene-rules` | **Opus** | P0 |
| Sistema de traducción ES/EN: tabla CSV, claves para las 11 reglas y para los textos del menú | Haiku | P0 |
| Ajustes de rendimiento: Mobile, MSAA 2x, sin SSAO/SSR/glow caro en Quest y física a 72/90 Hz | **Opus** | P0 |
| **Mapa nevado en Blender**, export e integración en `Map.tscn` | **Opus** | P0 |
| Importar `player.glb` y `krampus.glb` (escala, materiales y esqueleto) | Haiku | P0 |

### Día 2: jugador híbrido y red
| Tarea | Modelo | Prioridad |
|---|---|---|
| `Player.tscn`: detección VR/PC, movimiento, correr, agacharse (ruido) y salto | **Opus** | P0 |
| Agarre: VR con manos (XR Tools). PC con click mantenido, con peso y rotación con R | **Opus** | P0 |
| Lobby LAN de hasta 4 jugadores: host, unirse por IP y descubrimiento automático por broadcast. Sincronización con netfox | **Opus** | P0 |
| Avatar remoto (cabeza y manos para VR, cuerpo completo con IK simple) | Sonnet | P1 |
| Cámara de viewmodel más nieve (ver la nota técnica abajo) | Sonnet | P0 |

### Día 3: objetos, recursos y crafteo
| Tarea | Modelo | Prioridad |
|---|---|---|
| Recursos `.tres` de los 13 objetos y escenas de objeto | Haiku | P0 |
| Inventario: bolsillos VR más inventario en cuaderno para PC | Sonnet | P0 |
| Hacha y pico contra árboles y rocas (vida del recurso y soltar materia) | Sonnet | P0 |
| Linterna (SpotLight barata más cono de detección para el fantasma) | Sonnet | P0 |
| Frío y fuego: barra de temperatura, chamarra, antorcha y farol (bloquean armas y objetos), fogata y chimenea como fuentes de calor | Sonnet | P0 |
| Escopeta y Desert Eagle: disparo por raycast, munición, recarga y sincronización en red | Sonnet | P1 |
| Puertas, ventanas, cubiertas y mesa de trabajo. Crafteo de mejoras (recetas `.tres`) | Sonnet (sistema) + Haiku (escenas) | P0 |

### Día 4: reglas, ciclo de 15 min y ambiente
| Tarea | Modelo | Prioridad |
|---|---|---|
| `RuleManager` y las 11 reglas, cada una con su escena, su verificación y su consecuencia (sube la "ira de Krampus" o activa un evento) | Sonnet | P0 |
| Ciclo día/noche de 15 min (Día 1, 4 min → Noche 1, 3 min → Día 2, 3 min → Noche final con Krampus, 5 min) y luna que se pone (regla 9) | Sonnet | P0 |
| Director de anomalías: niveles de ira y primeras anomalías (luces, cuadros, huellas, glitches de vértices, siluetas) | Sonnet (sistema) + Haiku (anomalías sencillas) | P0 |
| Cascabel péndulo: izquierda evento malo, derecha bueno. Tabla de eventos `.tres`. También lo activa el director de anomalías | Sonnet | P0 |
| Iluminación falsa: LightmapGI baked, emisivos y tarjetas de luz para fuego y ventanas, una sola sombra direccional corta | **Opus** | P0 |
| Precarga de shaders: escena `ShaderWarmup` que dibuja cada material y partícula una vez detrás de la pantalla de carga | **Opus** | P0 |

### Día 5: IA
| Tarea | Modelo | Prioridad |
|---|---|---|
| Base de IA con LimboAI, percepción por ruido y vista, y sincronización en red | **Opus** (base) | P0 |
| Vaca (huye), gallinas (grupo vengativo que ralentiza) y ganado (comer) | Haiku | P0 |
| Cerdo (zigzag frenético), oso (lento, empuja) y lobos (manada que rodea con alfa) | Sonnet | P0/P1 |
| Gnomo (se mueve si no lo miras y explota) y fantasma (se disipa con la linterna) | Sonnet | P0 |
| Renos zombis y Rodolfo alfa. Oleadas nocturnas que atacan la cabaña y buscan apagar el fuego | Sonnet | P0 |
| **Krampus:** roba el pastel. Si se lo come, entra en fase jefe con patrones aleatorios | **Opus** | P0 (base) / semana 2 (patrones) |

### Día 6: GUI y ciclo de muerte
| Tarea | Modelo | Prioridad |
|---|---|---|
| Theme `.tres` del cuaderno, fuentes y paleta (con las skills de diseño) | Sonnet | P0 |
| Menú 360: tren, árbol, 3 regalos, fondo difuminado y navegación PC/VR | Sonnet | P0 |
| Opciones con engranajes (calidad, audio por bus, controles y confort VR) | Sonnet | P0 |
| HUD de PC, reloj de muñeca VR, nota de reglas e inventario en cuaderno | Sonnet + Haiku | P0 |
| Derribo, reanimación de 10 s, banner de muerte para todos, minijefe y revivir | Sonnet | P0 |
| Lista de nombres de archivos de audio esperados, más buses y `AudioStreamPlayer` vacíos | Haiku | P0 |

### Día 7: integración, Quest y prueba de 15 min
| Tarea | Modelo | Prioridad |
|---|---|---|
| Build en Quest 2: perfilar con OVR Metrics hasta ≥ 75 FPS estables (foveación, LOD, draw calls < 150) | **Opus** | P0 |
| Partida completa de 15 min con 2–4 jugadores (PC + Quest) y lista de bugs | Tú + Opus | P0 |
| Corrección de bugs repartida por dificultad | Sonnet/Haiku | P0 |
| `ESTADO.md`, `CLAUDE.md` (cómo compilar y probar), commit y PR de la semana | Haiku | P0 |

### Semana 2 (resumen)
Integrar tu carpeta de audio. Animaciones de Mixamo. Patrones completos del jefe Krampus. Escena del trailer (vendedor y auto). Pulido de la GUI. Equilibrio de la partida de 15 min. Pruebas en el torneo.

### Notas técnicas clave
- **Cámara de viewmodel con nieve:** en **PC**, una segunda cámara en un `SubViewport` transparente superpuesto, con `cull_mask` de la capa 2 (los objetos en mano no atraviesan paredes) y `GPUParticles3D` de nieve local y barata en esa misma cámara. En **VR** no existe el apilado de cámaras: los objetos en mano usan un material con `no_depth_test`/render priority, la nieve va como partículas pegadas al `XRCamera3D` y las manos físicas de XR Tools ya evitan atravesar paredes.
- **75 FPS en Quest 2:** renderer Mobile, sin HDR 2D, MSAA 2x, foveación fija o dinámica del plugin Vendors, LOD y MultiMesh para la vegetación, máximo 1 luz dinámica con sombra, niebla de profundidad en lugar de volumétrica y texturas de 1K con compresión ETC2/ASTC.
- **Godot 4.7:** cachea pipelines y tiene ubershaders, lo que ya reduce los tirones. La escena `ShaderWarmup` cubre el resto.

---

## 9. Decisiones confirmadas

| # | Tema | Decisión |
|---|---|---|
| 1 | Multijugador | **Solo LAN** por ahora |
| 2 | Jugadores | **Máximo 4** |
| 3 | Equipo de prueba | Hay visor VR y PC disponibles |
| 4 | Estructura de 15 min | Día 1 (4 min) → Noche 1 (3 min, primera oleada) → Día 2 (3 min) → Noche final (5 min, Krampus viene por el pastel). Se gana sobreviviendo hasta el amanecer con el fuego encendido |
| 5 | Romper reglas | Anomalías por todo el mapa: glitches, ambiente aterrador, enemigos nuevos, más dificultad y activación del péndulo (sección 4.1) |
| 6 | Minijefe | **Gnomo de jardín musculoso** |
| 7 | Idiomas | **Español e inglés** |
| 8 | Modelos de Tripo3D | Los genera el usuario con los prompts de la sección 6.3 |
| 9 | Git `safe.directory` | Lo configura el usuario. Mientras tanto se usa `-c safe.directory` |
| 10 | Frío | Sí. La chimenea y las fogatas calientan. La chamarra basta de día. De noche hace falta llevar fuego para salir, y con él en la mano no se pueden usar armas ni objetos (sección 4.2) |

### 9.1 Interpretaciones (aprobadas el 2026-10-31)

| # | Duda | Propuesta |
|---|---|---|
| A | La regla 11 dice "Jamás salgas de noche", pero el frío permite salir de noche con fuego. ¿Las dos conviven? | Sí. Salir de noche **con** fuego te salva del frío, pero **igual rompe la regla 11**: sube la ira y dispara anomalías. Salir de noche es posible, pero caro |
| B | En VR hay dos manos. ¿El fuego bloquea también la otra mano? | Con el fuego en una mano, la otra **puede agarrar e interactuar** (puertas, objetos sueltos) pero **no usar armas ni objetos del inventario**. Así se respeta la regla y la otra mano sigue siendo útil |

---

## Fuentes consultadas
- [Godot 4.7 lanzado (GamingOnLinux)](https://www.gamingonlinux.com/2026/06/godot-engine-4-7-is-out-bringing-a-new-asset-store-hdr-support-steam-frame-support/comment_id=296481) · [Novedades XR en 4.7 (VR.org)](https://vr.org/articles/godot-4-7-xr-steam-frame-android-xr-open-source-2026)
- [Godot XR Tools](https://github.com/GodotVR/godot-xr-tools/releases) · [OpenXR Vendors](https://github.com/godotvr/godot_openxr_vendors) · [Meta Toolkit](https://godotengine.org/article/godot-xr-update-feb-2025/)
- [netfox](https://github.com/foxssake/netfox) · [LimboAI](https://www.github.com/limbonaut/limboai) · [ProtonScatter](https://zff.dev/malgreen/proton_scatter) · [Terrain3D 1.0.2](https://github.com/TokisanGames/Terrain3D/releases/tag/v1.0.2-stable)
- [Claude-GoDot-MCP](https://github.com/DaRealDaHoodie/Claude-GoDot-MCP) · [Godot-MCP](https://github.com/ee0pdt/Godot-MCP)
- [Tripo: animaciones y export](https://www.tripo3d.ai/blog/apply-preset-animations-to-3d-character) · [Flujo de animación en Godot (foro)](https://forum.godotengine.org/t/3d-character-animation-workflow/105583)
- [Optimización Quest 2 (Meta)](https://developers.meta.com/horizon/blog/showdown-on-quest-part-2-how-we-optimized-the-pc-vr-demo-for-meta-quest-2/)
