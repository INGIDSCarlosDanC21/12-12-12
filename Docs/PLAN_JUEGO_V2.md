# Plan v2: juego principal (rework de sistemas, visual, sonido, controles y miedo)

Fecha: 2026-11-08. Estado: **Fase 0 hecha; Fase 1 hecha salvo M-01 (ajuste) y B-05 (caso concreto pendiente)**. Ver `ESTADO.md` (handoff del plan v2) y `tools/captura/README.md`.
Alcance: el juego principal (PC, PCVR Quest 2 y móvil) con todo lo pedido en la lista del usuario. El modo educativo sigue su propio plan (`PLAN_MAESTRO.md`) y no se mezcla aquí.

## 0. Realidad del alcance

La lista pedida equivale a varios juegos: sistema de economía, construcción, IA de enemigos que destruyen estructuras, bosses, rareza, armaduras visibles, animación completa, VR y móvil. Cada bloque depende de otros. Para no entregar algo a medias:

- Se trabaja por **fases con criterio de salida**. Una fase no empieza hasta que la anterior pasa sus pruebas y tiene captura revisada.
- Las pruebas de "sensación" (juice, comodidad de control, miedo) **requieren que el usuario juegue** en cada plataforma. Yo puedo probar lógica, carga y capturas, pero no decidir si algo se siente bien.
- Cada sistema nuevo entra con su prueba automática y su apagado (feature flag) para no romper la partida base.

## 1. Herramientas para ver la pantalla (respuesta a la petición)

Ya existe un método que funciona: un script de Godot de tipo `SceneTree` que carga la escena, espera frames, y guarda `get_viewport().get_texture().get_image()` en PNG. Así se han revisado el menú, la cabaña y las lecciones.

Lo que falta para "ver el juego como un jugador":
- **Entrada simulada completa** (ratón, teclado, mando, táctil) con eventos reales. Ya probado para el menú y la lección 1.
- **Grabación de secuencias** (varios PNG en tiempo fijo) para revisar animaciones. Godot tiene `--write-movie` (fixed FPS), que sirve sin instalar nada.
- **Skill o plugin:** no es imprescindible para esto. Si quieres una herramienta externa de capturas o de pruebas visuales, dime cuál; instalar plugins requiere tu aprobación porque cambian el entorno.

Entregable: `tools/captura/` con scripts reutilizables (escena, cámara, pasos) y un informe por fase con capturas.

## 2. Fases

### Fase 0 · Base de pruebas (1 día)
- Scripts de captura y de entrada simulada por plataforma.
- Lista de escenas de referencia: menú, cabaña de día, cabaña de noche, exterior, combate, interfaz de mejoras.
- Criterio: una ejecución genera capturas de todas las escenas de referencia sin errores.

### Fase 1 · Bugs de base y sensación de movimiento (3-4 días)
| ID | Tarea | Archivos principales | Aceptación |
|---|---|---|---|
| B-01 | Roca y mineral con el mismo modelo | `scenes/world/resources/rock.tscn`, `ore_vein.tscn`, `assets/world/` | Dos modelos distintos; el mineral con vetas |
| B-02 | Objetos que flotan y colisiones que no coinciden | `scripts/player/pc_rig.gd` (agarre), `scripts/items/`, colisiones de escenas de objeto | Prueba de altura del objeto en mano y en suelo; sin separación visual |
| B-03 | Animaciones de objetos de lado (deben ir derechas) | `scenes/items/*.tscn`, pose de `viewmodel_hands.gd` | Captura: el hacha, el cubo y la linterna en la mano vertical |
| B-04 | Recursos repartidos por todo el mapa (no en una sola zona) | `scenes/world/map.tscn` (recursos), generador de `tools/mundo/` | Distribución por cuadrícula de 16 zonas; prueba de reparto |
| B-05 | Estructuras bugueadas y cofres mal colocados | `scenes/world/cabin/`, POI en `scenes/poi/` | Prueba de colisión en cada POI y de cofres sobre suelo |
| M-01 | Movimiento suave y cómodo en PC, Quest y móvil (aceleración, inercia, giro) | `pc_rig.gd`, `vr_rig.gd`, `touch_controls.gd` | Valores de referencia por plataforma; prueba de caminar, correr, saltar |

### Fase 2 · Manos, personajes y animación (5-6 días)
| ID | Tarea | Notas |
|---|---|---|
| A-01 | Manos con animación: reposo, andar, correr, saltar, agarrar, usar herramienta | Clips propios o CC0 (Quaternius Universal Animations, KayKit). Mixamo requiere cuenta: no se usa |
| A-02 | Estados corporales: frío, miedo, respirar, agachado, derribado, atacar | Mezcla por capas (cuerpo + manos); respiración sin mover el cuerpo |
| A-03 | Armaduras visibles en todos los jugadores | Depende de A-01 y del rig; se sincroniza el ítem equipado por red |
| A-04 | Vista desde abajo: ver bufanda, torso y piernas (no solo la cara) | Ajuste de cámara y de la malla del avatar local |
| A-05 | Variantes de color y textura por jugador (4 colores de bufanda y gorro) | Material por jugador; se sincroniza en red |
| A-06 | Barra de vida sobre el nombre de otros jugadores | Label3D + barra; visible a distancia corta |
| A-07 | Muñecos con miedo constante (postura, temblor, mirada) | Idle de miedo por capas; sin cambio de IA |

Dependencia: A-01 antes de A-02 y A-03. Si no hay rig de calidad, se usan modelos CC0 con animaciones, aprobados por el usuario.

### Fase 3 · Interfaz y controles (4-5 días)
| ID | Tarea | Aceptación |
|---|---|---|
| U-01 | Inventario estilo Minecraft: cuadrícula con icono y cantidad | Abre con Tab; arrastrar, dividir y soltar; funciona en PC, VR y móvil |
| U-02 | Rareza de objetos (Común, Poco común, Raro, Épico, Legendario, Mítico) con colores de Fortnite | Color del borde y del nombre; prueba de orden de valor |
| U-03 | HUD simple: vida, temperatura, munición y objeto activo | Sin lista de textos; prueba de legibilidad a 1 m en Quest |
| U-04 | Avisos de reglas 30 segundos antes de que se rompan | Cuenta atrás visible y sonora; se apaga si se cumple |
| U-05 | Mesa de mejoras nueva: vista de receta, requisitos y coste claros | Diseño basado en menús de crafteo de juegos de supervivencia |
| U-06 | Controles PC: mapa completo, remapeo y textos de ayuda | Todas las acciones con tecla visible |
| U-07 | Controles móvil: botones grandes, zonas de pulgar, ajuste de tamaño | Prueba en pantalla de teléfono y tableta |
| U-08 | Controles VR: mapa de botones por mano, ayuda en pantalla, giro y teletransporte | Prueba en Quest 2 |
| U-09 | Teclado virtual en VR para escribir el nombre | Pulsación con puntero y gatillo; caracteres de español |
| U-10 | Tabla de controles en un solo sitio (`Docs/CONTROLES.md`) | Revisada por el usuario en cada plataforma |

### Fase 4 · Sistemas de juego (6-8 días)
| ID | Tarea | Diseño |
|---|---|---|
| S-01 | Monedas y tienda | Monedas por recursos y enemigos; se usan en cofres y estatuas |
| S-02 | Cofres aleatorios tipo MUCK | Cofre con precio que varía; contenido aleatorio: habilidad o objeto con rareza; icono y coste visibles |
| S-03 | Estatuas de revivir | Revive a un compañero por monedas; el jugador elige a quién revivir |
| S-04 | Al morir: se sueltan todos los objetos; no se conservan al revivir | Cambio en `death_manager.gd` y `downed.gd` |
| S-05 | Derribado puede moverse y usar objetos en la mano | Movimiento lento; uso de herramientas limitado |
| S-06 | Construcción libre tipo MUCK: paredes, puertas, escaleras, techos | Rejilla de piezas; colocación con previsualización; coste en materiales |
| S-07 | La cabaña deja de ser el único refugio | Varias estructuras; cada una con vida |
| S-08 | Enemigos que destruyen estructuras para entrar | IA que busca brechas; daño a paredes y puertas |
| S-09 | Nuevos enemigos para compensar cofres y monedas | Al menos 3: atacante de estructuras, merodeador nocturno, vigilante con alarma |
| S-10 | Armaduras, nuevos minerales y horno con mejoras | Materiales nuevos; horno que funde mineral; mejoras por niveles |
| S-11 | Bosses opcionales en estatuas | Moveset de 3 ataques; recompensa con rareza alta; activación por objeto ritual |
| S-12 | Pastel activa el modo difícil | Ver sección 3 |
| S-13 | Reglas con consecuencias (estilo mods de terror de Minecraft) | Ver sección 3 |
| S-14 | Economía y balance de todo lo anterior | `resources/game/balance.tres`; pruebas de rango de monedas |

### Fase 5 · Ambiente, Navidad, miedo y sonido (5-6 días)
| ID | Tarea | Fuente |
|---|---|---|
| V-01 | Skybox de día y de noche que se vea bien | Poly Haven HDRI (CC0) para día; noche procedural con luna y estrellas |
| V-02 | Efectos navideños: guirnaldas, luces, adornos, copos suaves (si se mantiene la nieve) | Kenney Holiday (CC0) ya en el proyecto; partículas propias |
| V-03 | Iluminación de miedo: sombras largas, niebla local, parpadeo de luces | Ajustes de `day_night.tscn` y de `cabin_lights.gd` |
| V-04 | Sonido de miedo: pasos, respiración, ambiente de noche, sustos sutiles | CC0 (Freesound, OpenGameArt) y música de Gemini (el usuario la genera) |
| V-05 | Música por estado: calma, tensión, persecución | Cuatro pistas: menú, día, noche, persecución |

## 3. Reglas con consecuencias y dificultad (diseño)

**Reglas con consecuencias (inspiración: mods de terror de Minecraft).** Cuando el jugador rompe una regla, además de los efectos actuales, puede activarse un monstruo. Ejemplo confirmado por el usuario: un monstruo sale de fuera, abre la puerta y se lleva a un compañero cercano al azar. El compañero queda con una vida en el bosque; si ya tenía toda la vida, muere. Reglas de aviso:
- Cada regla tiene una **cuenta atrás de 30 segundos** antes de romperse (U-04).
- Si el jugador cumple la acción a tiempo, la regla no se rompe.

**Modo difícil por pastel.** Al comerse el pastel (regla 1), el juego pasa a modo difícil durante el resto de la partida: más enemigos por noche, enemigos que destruyen más rápido, cofres más caros, estatuas con bosses más fuertes y el monstruo de las reglas se activa antes. Sin ese cambio, el pastel no tiene efecto. Se avisa al jugador con un aviso visual y sonoro.

**Dificultad.** Se mide en el balance (`resources/game/balance.tres`) y en el tiempo de cada noche. Se prueba que el modo difícil es jugable en cuatro jugadores.

## 4. Controles por plataforma (criterio común)

- **PC:** teclado y ratón con remapeo; mando de consola opcional con la misma tabla.
- **Móvil (Android):** botones grandes en zonas del pulgar, ajuste de tamaño y opacidad, giro con arrastre; sin atajos obligatorios.
- **Quest 2:** mapa de botones de cada mano, teletransporte o giro suave a elegir, ayuda visual en la muñeca, teclado virtual para el nombre.
- Todo control se describe en `Docs/CONTROLES.md` y se revisa en las tres plataformas antes de darlo por bueno.

## 5. Riesgos y mitigaciones

| Riesgo | Mitigación |
|---|---|
| Scope demasiado grande para la fecha | Fases con salida; el usuario elige qué se entrega primero |
| Animaciones de calidad sin Mixamo | Clips CC0 (Quaternius Universal Animations, KayKit) o rig de Tripo aprobado por el usuario |
| Rendimiento en Quest 2 con más enemigos y estructuras | Presupuesto de draw calls por fase; LOD y desactivación de IA lejana (ya existe en el juego) |
| Red de 4 jugadores con inventario, monedas y construcción | Autoridad en el host; pruebas LAN de 2 procesos en cada fase |
| Juice y miedo no se pueden medir por código | Revisión del usuario en cada fase con capturas y una partida corta |
| Dos juegos en uno (educativo y principal) | Se mantienen separados: el modo educativo no usa estos sistemas |

## 6. Decisiones del usuario (respondidas 2026-11-08)

1. Nieve: el juego principal **sigue nevado**.
2. Orden: **Fase 1 primero**, luego los sistemas nuevos (Fase 4).
3. Rareza: **seis** niveles (Común, Poco común, Raro, Épico, Legendario, Mítico).
4. Modo difícil por pastel: **aprobado** tal como está en la sección 3.
5. Quest 2: **disponible** para probar cada fase.
6. Monstruo que abre la puerta y se lleva a un compañero: **aprobado**.
7. Modelos: **Tripo autorizado** para armaduras, bosses y horno; preferir CC0 cuando existan.

Preguntas originales, con la respuesta que se aplicó:


1. **Nieve:** ¿el juego principal sigue nevado o cambia a un ambiente de invierno más suave? Afecta a V-02 y a V-03.
2. **Orden de fases:** ¿prefieres empezar por bugs y movimiento (Fase 1) o por sistemas nuevos (Fase 4)? Recomiendo Fase 1 primero: es lo que más se nota al jugar.
3. **Rareza:** ¿usamos las seis rarezas de Fortnite tal cual, o cuatro?
4. **Modo difícil:** ¿aprueba la propuesta de la sección 3, o quieres otra consecuencia por el pastel?
5. **Plataformas de prueba:** ¿tienes Quest 2 disponible para probar VR con una partida corta cada fase?
6. **Monstruo de las reglas:** ¿lo aprueba el nombre y comportamiento de la sección 3?
7. **Modelos:** ¿autorizas a Tripo para armaduras, bosses y el horno, o prefieres modelos CC0 cuando existan?

## 7. Estado de implementación (2026-11-08)

| ID | Estado | Evidencia |
|---|---|---|
| Fase 0 | Hecha | `tools/captura/` (6 escenas sin errores, secuencias a tiempo fijo) |
| B-01 | Hecha | Mineral con vetas (`mat_veta_mineral.tres`); captura `objetos_frente.png` |
| B-02 | Hecha (parcial) | Colisión de cabeza en picos; hacha de hierro centrada; `test_fase1_base` (46 PASS) |
| B-03 | Hecha | Inclinación −35° → −6°; recipientes y antorcha derechos; capturas `mano_*.png` |
| B-04 | Hecha | 56 recursos en 16 zonas (`recursos_zonas.tscn`); `test_escenas` 72 recursos |
| B-05 | Parcial | 15 cofres apoyados en el suelo real (`test_fase1_pois` 16 PASS). Falta el caso concreto de "estructuras bugueadas" |
| M-01 | Parcial | Valores de referencia en `Docs/MOVIMIENTO_REFERENCIA.md`; falta partida de prueba en Quest, PC y móvil |

**Siguiente paso:** partida de prueba del usuario (Quest 2 y PC) para juzgar B-03 y M-01 en sensación. Después, Fase 2 (manos y animación), que necesita decidir el rig: clips CC0 o modelos de Tripo aprobados.
