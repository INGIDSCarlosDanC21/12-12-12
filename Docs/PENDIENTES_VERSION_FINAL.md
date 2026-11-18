# Pendientes para la versión final (juego principal)

Fecha: 2026-11-11. Rama: `dia-13/modelos`. Alcance: juego principal en PC, PCVR, Quest 2 y Android. **El modo educativo queda fuera** de este análisis.

## Estado medido hoy

- Batería de un proceso: 63 pruebas, **63 en verde** (se corrigió una comprobación desactualizada de `test_fase1_base`: el cabeceo en mano es 18° desde el 9 de noviembre).
- `tools/verificar.ps1`: importación y arranque bien. Marca como error el aviso `4 resources still in use at exit` de `test_escenas`.
- No se ejecutaron las 5 pruebas LAN de dos procesos ni las del modo educativo.
- Todas las ramas de agentes están integradas en `dia-13/modelos`. `main` va 135 commits por detrás.

## P0 · Sin esto no hay versión final

| # | Qué falta | Quién | Nota |
|---|---|---|---|
| 1 | **Copia de seguridad.** No hay remoto de git; el proyecto solo existe en `D:\12-12-12` | Usuario | INF-04. Es el mayor riesgo del proyecto |
| 2 | **Quest 2 real.** Nada de VR se ha probado en el visor: FPS (meta 72–75), inventario en panel, construcción, cofres, estatuas, mesa, teclado, ayuda de muñeca, balanceo de manos, reloj | Usuario + Claude | El APK de `builds/quest/` es del 4 de noviembre: hay que exportar uno nuevo |
| 3 | **Android real (Reno 11).** El APK del 9 de noviembre no está instalado: multitoque, autosalto, inventario táctil, tamaño de textos | Usuario + Claude | `builds/android-flat/12-12-12-tactil.apk` |
| 4 | **LAN real.** Solo se ha probado con dos procesos en el mismo PC. Falta PC↔PC, PC↔Quest y 4 jugadores; `tools/lan_firewall.ps1` sin ejecutar como administrador | Usuario | D13-03 |
| 5 | **Partida completa jugada por una persona** (30 min) para balance: armaduras, horno, construcción, rompedores, modo difícil, cofres de pago, coro de fantasmas (4 s / 5 s), munición contra Krampus | Usuario | Todo lo de la Fase 4 solo tiene pruebas automáticas |
| 6 | **Empaquetado.** Sin icono ni pantalla de arranque propios; versión `0.7.0` con código 1; el ejecutable de Windows es del 4 de noviembre; firma de publicación de Android sin comprobar; sin pantalla de créditos dentro del juego | Claude + usuario | Ver "Entrega" |

## P1 · Pulido que se nota

**Modelos:**
- Hechos el 2026-11-11 con Tripo (8 modelos, 200 créditos): minijefe gnomo musculoso, guardián de la estatua (jefe y estatua dormida), merodeador, vigilante, visitante, rompedor de estructuras, horno y estatua de revivir. Son estáticos, con animación procedural; los humanoides se pueden riguear después (ver `Docs/TRIPO_REGISTRO.md`).
- Siguen con cajas y cilindros: núcleo y adornos del coro de fantasmas, las 4 armaduras, campana ritual, monedas, lingotes y minerales, trampa para osos, munición, botón verde, mesa de leche, corral y comedero, ventana, y las 5 piezas de construcción. El pastel, el árbol y la chimenea mezclan modelo y piezas simples.
- Sin revisar en partida: tamaño de los modelos nuevos frente a sus colisiones, y cómo se ven en Quest 2.

**Audio:**
- Música: hay 2 pistas en `assets/audio/musica/`; los reproductores de música de ambiente no tienen pista. Faltan día, noche, persecución y jefe.
- Más de 80 reproductores de sonido sin archivo en la escena (algunos se asignan por código). Los más vacíos: Krampus (11 de 13), vaca, cerdo, gallina, enemigos nuevos, minijefes, coro, péndulo.
- Sonidos que nunca existieron: cadenas y pasos de Krampus, cascos, cofres, mugido, avisos de regla rota, amanecer y anochecer.
- Los sonidos del aviso de 30 s son provisionales (U-04). Varios sonidos del jugador solo los oye su dueño.

**Animación y aspecto (Fase 2 del plan v2):**
- Jugador y avatares sin clips: todo es animación procedural. Falta decidir el rig (clips CC0 o Tripo).
- Sin hacer o sin revisar: armadura visible en los demás jugadores en LAN (A-03), barra de vida sobre el nombre (A-06), variantes de color por jugador (A-05), postura de miedo (A-07).
- Escopeta inclinada en la mano; linterna en mano pequeña.

**Ambiente y rendimiento:**
- Volver a medir draw calls (límite 150): desde la última medida entraron 56 recursos, construcción y enemigos nuevos.
- Las montañas desaparecen en Quest (distancia de dibujado 95 m).
- `LightmapGI` sin hornear (D4-07).
- Capturas de referencia sin revisar por el usuario (L-10); captura nocturna en Quest (L-05).

## P2 · Deuda técnica y de red

- El coste de construcción lo declara cada cliente; el host no ve el inventario real.
- Armadura y modo difícil sin probar para clientes que entran tarde.
- Krampus no aplica el multiplicador de modo difícil a las puertas.
- Las puertas de la cabaña no son objetivo del rompedor de estructuras.
- La antorcha que tira un cliente nace llena; la linterna de bolsillo no se ve en el avatar remoto de PC.
- B-05 "estructuras bugueadas": falta el caso concreto.
- Pruebas inestables anotadas: `test_animales` ("el cerdo muerde") y `test_oleadas`.

## Entrega

1. Remoto privado y subir todas las ramas; integrar `dia-13/modelos` en `main` y etiquetar la versión.
2. Decidir qué pasa con los cambios locales sin commitear (`cabin.tscn`, `noche_1.tres`, `noche_final.tres`, tres enemigos, `entorno.tres`, `lobby_debug.tscn`), los 2 stash y `12-12-12.pck`, que está versionado en la raíz siendo un archivo de compilación.
3. Borrar los 11 worktrees de agentes (ya integrados) y decidir sobre `dbg_shot.gd` y `tools/lan/`.
4. Icono, pantalla de arranque, número de versión y firma de publicación; exportar Windows, Quest y Android desde el mismo commit.
5. Licencias: confirmar el plan de Tripo y la licencia de Holiday Bits; pantalla o archivo de créditos en el juego.
6. Documentos atrasados: el GDD sigue describiendo 15 min, 13 objetos y 3 regalos (hoy son 30 min, inventario de 25 ranuras, monedas, construcción y 4 regalos). `Docs/TAREAS.md` marca como pendientes tareas ya integradas.

## Decisiones del usuario (respondidas el 2026-11-11)

| # | Decisión | Respuesta | Estado |
|---|---|---|---|
| 1 | Plataformas de la versión final | PC, PCVR, Quest 2 nativo y Android táctil | Anotado en el GDD |
| 2 | Regalo "Educativo" en el menú | Oculto (`show_educativo = false` en `main_menu.gd`); el menú vuelve a tres regalos | Hecho |
| 3 | Modelos de Tripo T1 a T8 | Aprobados los 8 (unos 260 créditos, máximo 80 por modelo) | Hecho con cambios: 3 ya existían y 2 tenían modelo libre, así que los créditos (200) fueron a 8 modelos que seguían con formas simples. Ver `Docs/TRIPO_REGISTRO.md` |
| 4 | Música | Buscar pistas CC0 o generarlas; el usuario las aprueba antes de conectarlas | Pendiente |
| 5 | Duración de la partida | 30 minutos definitivos | GDD actualizado |
| 6 | Coro de fantasmas | Más fácil: ventana de 6 s y núcleo abierto 8 s | Hecho (`coro_fantasmas.gd`) |

## Orden recomendado

1. Remoto de git (10 minutos, lo hace el usuario).
2. Exportar los tres paquetes nuevos e instalar; una sesión de prueba por plataforma con lista de comprobación.
3. Corregir lo que salga de esas pruebas (lo más probable: VR).
4. Modelos y audio que faltan, por orden de visibilidad: jefes y enemigos, objetos de las reglas, construcción.
5. Balance tras una partida completa de 2 a 4 jugadores.
6. Empaquetado, créditos, documentos y etiqueta de versión.
