# Plan maestro: juego completo, modo educativo y rework visual/sonoro

Fecha: 2026-11-08. Autor: Claude (con reparto a agentes y usuario).
Estado: **vivo**. Este documento reemplaza la lista suelta de planes. Los detalles viven en los planes específicos (ver sección 8).

## 0. Realidad del alcance (leer primero)

Actualización 2026-11-08 (usuario): la meta de la exhibición pasa a **21 de noviembre** (3 días más). El juego principal no se rehace: recibe un **lavado de cara** (pulido visual, sonoro y "juice"). Plataformas objetivo: **Quest 2, PC y móvil Android**. Las revisiones de contenido las hace el usuario. Tripo y Gemini se usan desde la sesión abierta en el navegador del usuario.

"Juego completo, pulido, visual y sonoramente terminado, en PC, PCVR y Android" es un proyecto de varios meses de trabajo de un equipo, no de unas semanas. Lo que hay hoy:

- **Modo educativo:** 7 lecciones jugables en PC con ratón y teclado, cabaña y escenario exterior nuevos, sin VR ni Android probados, sin música, sin audio de sitio, con modelos de planta y de casa pendientes.
- **Juego principal:** completo en mecánicas (día, noches, Krampus, crafteo, LAN de 4 jugadores, VR y PC), con problemas abiertos (host que sale, reingreso, pruebas de VR pendientes, rendimiento en Quest sin medir, ambientación nevada que el usuario quiere revisar).

Propuesta de corte para no prometer lo que no se puede entregar:
- **Antes del 18 de noviembre:** modo educativo presentable (L1 a L7 en PC, escenario de La Paz, música y audio de sitio básicos, modelos de planta y casa si se aprueban).
- **Después:** VR y Android del modo educativo, desastres D1 a D7, y el rework visual y sonoro del juego principal por fases.

## 1. Workstreams

Cada workstream tiene dueño y archivos propios para evitar choques (regla de los archivos calientes en `Docs/TAREAS.md`).

| WS | Nombre | Dueño | Archivos propios | Entregable |
|---|---|---|---|---|
| WS-A | Modo educativo: lecciones y mecánicas | Claude | `scripts/edu/`, `scenes/edu/`, `scripts/test/test_edu_*` | L1 a L7 pulidas, juez y pistas revisados |
| WS-B | Escenario y arte de La Paz | Claude + usuario (revisión) | `scenes/edu/`, `assets/externos/texturas/`, `assets/edu/` | Casa, plantas, suelo, cielo, luz |
| WS-C | Modelos 3D (plantas, casa, personajes) | Usuario (Tripo) + Claude (integración) | `assets/edu/modelos/`, `Docs/TRIPO_REGISTRO.md` | Modelos aprobados e integrados |
| WS-D | Audio (SFX de sitio, música, mezcla) | Agente de audio + usuario (Gemini) | `assets/audio/edu/`, `assets/externos/CREDITOS.md` | Sonido de sitio y 4 pistas |
| WS-E | Juego principal: visual y sonido | Por definir (fase 2) | `scenes/menu/`, `scenes/world/`, `assets/` del juego | Rework por fases |
| WS-F | Plataformas: VR y Android | Claude + usuario (dispositivos) | `scripts/player/vr_rig.gd`, `export_presets.cfg` | VR y Android del modo educativo |
| WS-G | Pruebas y calidad | Claude | `scripts/test/`, `tools/` | Pruebas automáticas y lista de aceptación |

## 2. Criterios de calidad (definición de "terminado")

Una escena o lección se considera terminada cuando:
1. **Jugable:** se completa de principio a fin con ratón y teclado, sin estados atascados ni textos vacíos.
2. **Sin errores:** `verificar.ps1` y las pruebas de la lección pasan.
3. **Visual:** captura revisada con la lista de `PLAN_ASSETS_Y_AUDIO_EDU.md` sección 6 (luz, materiales, escala, legibilidad).
4. **Sonoro:** cada acción tiene su sonido; hay ambiente en el exterior; la música no tapa las voces ni los avisos.
5. **Contenido:** textos en es y en, náhuatl revisado por una persona hablante si está marcado como listo, y revisión de contenido sensible (L2, L4, L6, D1 a D7) por personal especializado.
6. **Accesible:** subtítulos de avisos sonoros, texto legible, sin dependencia exclusiva del color.

## 3. Hitos

| Hito | Fecha | Contenido | Criterio |
|---|---|---|---|
| H0 | 8 nov | Plan maestro, tablero, descargas CC0 de texturas y cielo | Hecho |
| H1 | 11 nov | Video del modo educativo | L1 y L2 jugables con escenario de La Paz y audio de sitio básico |
| H2 | 21 nov | Exhibición y jurado (movido desde el 18) | L1 a L7 jugables en PC, música, plantas y casa si están aprobadas |
| H3 | Después del 18 | D1 a D7 (desastres) | Cada lección con su juez, texto revisado |
| H4 | Después | VR y Android del modo educativo | Pruebas en dispositivo real |
| H5 | Por fases | Rework visual y sonoro del juego principal | Se decide por fases con el usuario |

## 4. Recursos y permisos (estado)

| Recurso | Estado |
|---|---|
| Descargas CC0 (Poly Haven, ambientCG, Kenney, Quaternius, Freesound CC0) | Permiso dado. Texturas y cielo hechos; el resto en curso |
| Blender abierto (MCP) | Permiso dado; el usuario lo mantendrá abierto cuando haga falta |
| Tripo 3D | Permiso dado. Créditos disponibles (~1700 al último registro); se usa para modelos aprobados por el usuario |
| Gemini (música) | Permiso dado. El usuario genera las pistas; hay que guardar prompt, fecha y términos |
| Música de otros juegos | **No.** Tiene copyright y no se descarga, aunque el producto no sea comercial |
| Skills y agentes | Permiso dado para usar agentes (Sonnet/Opus) en tareas separadas |

## 5. Regla de trabajo con agentes

- Cada agente recibe un workstream con archivos propios y un criterio de aceptación.
- Ningún agente edita archivos calientes (`project.godot`, `main_menu.tscn`, `game.tscn`, `textos.csv`) sin que Claude lo integre.
- Los agentes entregan: lista de archivos cambiados, pruebas ejecutadas y dudas abiertas. Claude revisa, integra, verifica y hace el commit.
- Descargas: cada archivo con fuente, URL, autor y licencia en `CREDITOS.md`. Si la licencia no es clara, no entra.

## 6. Próximos pasos inmediatos

1. **WS-D:** agente de audio busca y descarga SFX CC0 de sitio (cigarras, mar, viento seco, fogón, golpes en puerta, pasos en arena) con registro de licencia.
2. **WS-A:** L4 y L6 con revisión de textos; prueba de la lección 7 con el flujo real.
3. **WS-B:** cuando Tripo entregue T1 a T4, Claude integra en L5 (plantas) y en el escenario (casa).
4. **WS-C:** el usuario genera T1 (cardón) y T2 (palo verde) en Tripo; luego T3 y T4 si T1 y T2 convencen.
5. **WS-D:** cuando el usuario tenga las pistas de Gemini, Claude las integra con un único reproductor y cruce de 2 segundos.
6. **WS-A:** después del 11 de noviembre, D1 (terremoto) y D4 (huracán).

## 7. Decisiones que quedan para el usuario

1. ¿Confirmas el corte de H2 (18 de noviembre) como meta de "modo educativo completo"?
2. ¿El rework del juego principal (WS-E) se hace después del 18 y por fases? Recomiendo sí.
3. ¿Qué dispositivos de prueba hay para VR (Quest 2 y PCVR) y Android (tableta o teléfono)?
4. ¿Quién revisa el contenido de L2, L4, L6 y D1 a D7? Sin esa revisión no se publica como "terminado".

## 8. Documentos relacionados

- `Docs/TABLERO_MODO_EDUCATIVO.md`: tareas del modo educativo y reparto.
- `Docs/PLAN_MODO_EDUCATIVO_INTEGRADO.md`: arquitectura de la integración.
- `Docs/PLAN_CONTEXTO_MEXICO.md`: cambio de contexto a La Paz.
- `Docs/PLAN_ASSETS_Y_AUDIO_EDU.md`: modelos, texturas y audio.
- `Docs/PLAN_LECCIONES_DESASTRES.md`: lecciones D1 a D8.
- `Docs/GDD.md`: diseño del juego principal.
