# Audio: archivos esperados

Toda la música y los efectos los entrega el usuario. El juego ya tiene un nodo de sonido
(`AudioStreamPlayer` o `AudioStreamPlayer3D`) en cada sitio, con el `stream` vacío: basta con
asignar el archivo en el Inspector. Mientras no tenga archivo, el nodo no suena y no da error.

- Formato recomendado: `.ogg` (Vorbis). Efectos cortos también pueden ser `.wav`.
- Buses: `Master`, `Music`, `SFX`, `Voice`, `Ambience` (ver `default_bus_layout.tres`).
- Carpeta: `res://audio/` con `sfx/`, `music/`, `voice/` y `ambience/`.

## Reglas (día 4)

| Nodo | Escena | Archivo sugerido |
|---|---|---|
| `SonidoComer` | `scenes/interactables/rules/cake.tscn` | `audio/sfx/pastel_comer.ogg` |
| `SonidoBoton` | `scenes/interactables/rules/green_button.tscn` | `audio/sfx/boton_verde.ogg` |
| `SonidoServirLeche` | `scenes/interactables/rules/milk_table.tscn` | `audio/sfx/leche_servir.ogg` |
| `SonidoServirGalletas` | `scenes/interactables/rules/milk_table.tscn` | `audio/sfx/galletas_servir.ogg` |
| `SonidoCuadroCambia` | `scenes/interactables/rules/picture.tscn` | `audio/sfx/cuadro_cambia.ogg` |
| `SonidoCuadroVoltea` | `scenes/interactables/rules/picture.tscn` | `audio/sfx/cuadro_voltea.ogg` |
| `SonidoCascabeles` | `scenes/rules/rule_07.tscn` | `audio/sfx/cascabeles.ogg` |
| `SonidoRisas` | `scenes/rules/rule_07.tscn` | `audio/sfx/risas.ogg` |
| `SonidoGolpes` | `scenes/interactables/door.tscn` | `assets/audio/objetos/puerta_golpe.ogg` (golpe seco en madera) |
| `SonidoAbrir` | `scenes/interactables/door.tscn` | `assets/audio/objetos/puerta_abrir.ogg` |
| `SonidoCerrar` | `scenes/interactables/door.tscn` | `assets/audio/objetos/puerta_cerrar.ogg` |

## Puerta de la cabaña (día 13)

Los tres sonidos son cortos y CC0, de los packs de Kenney (ver `assets/externos/CREDITOS.md`).
Sustituyen a los MP3 antiguos: `puerta_abrir.mp3` y `puerta_crujido.mp3` duraban 17 s (un crujido
largo) y hacían que abrir la puerta sonara mal.

| Nodo | Archivo | Duración | Origen |
|---|---|---|---|
| `SonidoAbrir` | `assets/audio/objetos/puerta_abrir.ogg` | 0,92 s | Kenney RPG Audio, `doorOpen_1.ogg` |
| `SonidoCerrar` | `assets/audio/objetos/puerta_cerrar.ogg` | 0,68 s | Kenney RPG Audio, `doorClose_1.ogg` |
| `SonidoGolpes` | `assets/audio/objetos/puerta_golpe.ogg` | 0,33 s | Kenney Impact Sounds, `impactWood_medium_000.ogg` |

El mismo `SonidoGolpes` se usa para los golpes de la regla 10 y para las embestidas de enemigos
(`Door.bash`). `max_polyphony = 2` permite que dos golpes seguidos no se corten.

## Péndulo del cascabel (`scenes/events/bell_pendulum.tscn`)

| Nodo | Archivo sugerido |
|---|---|
| `SonidoCampana` | `audio/sfx/pendulo_campana.ogg` |
| `SonidoCadena` | `audio/sfx/pendulo_cadena.ogg` |
| `SonidoAparece` | `audio/sfx/pendulo_aparece.ogg` |
| `SonidoBuenPresagio` | `audio/sfx/presagio_bueno.ogg` |
| `SonidoMalPresagio` | `audio/sfx/presagio_malo.ogg` |

## Anomalías (`scenes/events/anomalies/`)

| Anomalía | Nodo | Archivo sugerido |
|---|---|---|
| flicker | `SonidoParpadeo` | `audio/sfx/luces_parpadeo.ogg` |
| footprints | `SonidoPasos` (3D) | `audio/sfx/pasos_nieve.ogg` |
| red_snow | `SonidoNieveRoja` (Ambience) | `audio/ambience/nieve_roja.ogg` |
| tree_moved | `SonidoArbolMovido` | `audio/sfx/arbol_movido.ogg` |
| picture / bells / knock | `SonidoAviso` | `audio/sfx/aviso_evento.ogg` |
| shake_objects | `SonidoTemblor` | `audio/sfx/temblor.ogg` |
| silhouette | `SonidoSilueta` (3D, Ambience) y `SonidoSiluetaDesaparece` (3D) | `audio/ambience/silueta.ogg`, `audio/sfx/silueta_desaparece.ogg` |
| doppelganger | `SonidoDoble` (3D, Ambience) | `audio/ambience/doble.ogg` |
| vertex_glitch | `SonidoGlitch` (3D) | `audio/sfx/glitch.ogg` |
| summon_pendulum | `SonidoInvocacion` | `audio/sfx/invocacion.ogg` |
| early_threat | `SonidoAmenaza` | `audio/sfx/amenaza.ogg` |
| blackout | `SonidoApagon` | `audio/sfx/apagon.ogg` |
| fire_drain | `SonidoFuegoSeApaga` | `audio/sfx/fuego_se_apaga.ogg` |
| krampus_whisper | `SonidoSusurroKrampus` (Voice) | `audio/voice/krampus_susurro.ogg` |

## Pendientes (días 5 y 6)
Disparos, impactos, pasos del jugador, animales, enemigos, Krampus, muerte (banner),
menú 360, música de día y de noche y ambiente de viento. Se añaden aquí cuando existan sus nodos.

## Criaturas (día 5)

Cada criatura tiene un nodo `Sonidos` con estos `AudioStreamPlayer3D` (bus `SFX`); `Creature` los toca al cambiar de
estado de animación. Archivo sugerido: `audio/sfx/<criatura>_<sonido>.ogg`.

| Nodo | Cuándo suena | Criatura de prueba (`scenes/ai/test/criatura_prueba.tscn`) |
|---|---|---|
| `SonidoAlerta` | nota a un jugador (`alert`) | `audio/sfx/criatura_prueba_alerta.ogg` |
| `SonidoAtaque` | golpea (`attack`) | `audio/sfx/criatura_prueba_ataque.ogg` |
| `SonidoDolor` | recibe daño (`hurt`) | `audio/sfx/criatura_prueba_dolor.ogg` |
| `SonidoMuerte` | muere (`dead`) | `audio/sfx/criatura_prueba_muerte.ogg` |

## Animales (día 5)

Todas las escenas de `scenes/ai/animals/` tienen el nodo `Sonidos` con los cuatro `AudioStreamPlayer3D` estándar (bus `SFX`,
vacíos): `SonidoAlerta` (`alert`, nota o es provocado), `SonidoAtaque` (`attack`), `SonidoDolor` (`hurt`) y `SonidoMuerte` (`dead`).
Archivo sugerido: `audio/sfx/<animal>_<sonido>.ogg`.

| Animal (escena) | `SonidoAlerta` | `SonidoAtaque` | `SonidoDolor` | `SonidoMuerte` |
|---|---|---|---|---|
| Vaca (`cow.tscn`) | `vaca_alerta.ogg` (mugido corto) | no ataca (sin uso) | `vaca_dolor.ogg` (mugido de susto) | `vaca_muerte.ogg` |
| Gallina (`chicken.tscn`) | `gallina_alerta.ogg` (cacareo de alarma) | `gallina_ataque.ogg` (picotazo) | `gallina_dolor.ogg` | `gallina_muerte.ogg` |
| Cerdo (`pig.tscn`) | `cerdo_alerta.ogg` (chillido) | `cerdo_ataque.ogg` (mordisco) | `cerdo_dolor.ogg` | `cerdo_muerte.ogg` |
| Oso (`bear.tscn`) | `oso_alerta.ogg` (rugido) | `oso_ataque.ogg` (zarpazo) | `oso_dolor.ogg` | `oso_muerte.ogg` |
| Lobo (`wolf.tscn`) | `lobo_alerta.ogg` (gruñido/aullido) | `lobo_ataque.ogg` (mordisco) | `lobo_dolor.ogg` | `lobo_muerte.ogg` |

El ganado del corral (`livestock_idle.gd`) no tiene sonidos todavía; si se añaden, un `AudioStreamPlayer3D` `SonidoComer`
dentro de cada animal que suene al empezar a comer.

## Renos y oleadas (día 5)

Los renos usan el mismo nodo `Sonidos` de `Creature` (4 `AudioStreamPlayer3D`, bus `SFX`, `stream` vacío). Archivos sugeridos:

| Criatura | `SonidoAlerta` | `SonidoAtaque` | `SonidoDolor` | `SonidoMuerte` |
|---|---|---|---|---|
| Reno zombi (`scenes/ai/reindeer/zombie_reindeer.tscn`) | `reno_zombi_alerta.ogg` (bramido distorsionado) | `reno_zombi_ataque.ogg` (cornada / embestida a la puerta o a la chimenea) | `reno_zombi_dolor.ogg` | `reno_zombi_muerte.ogg` |
| Rodolfo (`scenes/ai/reindeer/rudolph.tscn`) | `rodolfo_alerta.ogg` (sirve también de aviso antes de embestir) | `rodolfo_ataque.ogg` | `rodolfo_dolor.ogg` | `rodolfo_muerte.ogg` |

Los golpes a la puerta suenan con `SonidoGolpes` de la propia puerta (`assets/audio/objetos/puerta_golpe.ogg`). Las oleadas (`WaveDirector`) no tienen
nodos de sonido; si se quiere un aviso de oleada (cuerno lejano, aullido de renos), irá en un nodo `SonidoOleada` del director
(`audio/sfx/oleada_aviso.ogg`), todavía sin crear.

## Gnomo y fantasma (día 5)

Ambos usan el nodo `Sonidos` de `Creature` (bus `SFX`; el ambiente del fantasma, bus `Ambience`). Archivos sugeridos en `audio/sfx/`.

| Criatura | Nodo | Cuándo suena | Archivo sugerido |
|---|---|---|---|
| Gnomo (`scenes/ai/gnome/gnome.tscn`) | `SonidoAlerta` | nota a un jugador (risita) | `audio/sfx/gnomo_risita.ogg` |
| Gnomo | `SonidoDolor` | recibe daño | `audio/sfx/gnomo_dolor.ogg` |
| Gnomo | `SonidoMecha` | se arma (`anim_state = armed`, todos los peers): chisporroteo de la mecha, dura ~`fuse_time_s` (1,1 s) | `audio/sfx/gnomo_mecha.ogg` |
| Explosión (`scenes/fx/gnome_explosion.tscn`) | `SonidoExplosion` | al explotar (vive en el efecto, no en el gnomo, para no cortarse al borrarse) | `audio/sfx/gnomo_explosion.ogg` |
| Fantasma (`scenes/ai/ghost/ghost.tscn`) | `SonidoAlerta` | nota a un jugador (susurro) | `audio/sfx/fantasma_alerta.ogg` |
| Fantasma | `SonidoToque` | toca al jugador (escalofrío) | `audio/sfx/fantasma_toque.ogg` |
| Fantasma | `SonidoDisipa` | se disipa con la luz (`dead`) | `audio/sfx/fantasma_disipa.ogg` |
| Fantasma | `SonidoAmbiente` (Ambience, bucle) | mientras flota; `Ghost` lo toca solo al aparecer si tiene `stream` | `audio/ambience/fantasma_lamento.ogg` |

## Krampus (día 5)

`scenes/ai/krampus/krampus.tscn`, nodo `Sonidos` (todos `AudioStreamPlayer3D`, bus `SFX`, `stream` vacío). Suenan en todos los
peers al cambiar `anim_state` o el modo (`krampus_mode`) de Krampus.

| Nodo | Cuándo suena | Archivo sugerido |
|---|---|---|
| `SonidoAlerta` | fija una presa en la fase jefe (`alert`) | `audio/sfx/krampus_alerta.ogg` (gruñido) |
| `SonidoAtaque` | zarpazo a un jugador o embestida a la puerta (`attack`) | `audio/sfx/krampus_zarpazo.ogg` |
| `SonidoDolor` | recibe daño (`hurt`) | `audio/sfx/krampus_dolor.ogg` |
| `SonidoMuerte` | derrotado en la fase jefe (`dead`) | `audio/sfx/krampus_muerte.ogg` |
| `SonidoRugido` | empieza la fase jefe (`roar`) y aparece en la visita temprana (`unit_size` 6: se oye lejos) | `audio/sfx/krampus_rugido.ogg` |
| `SonidoRobo` | agarra el pastel (`steal`) | `audio/sfx/krampus_risa_robo.ogg` (risa) |
| `SonidoComer` | se come el pastel en su guarida (modo `EATING`) | `audio/sfx/krampus_comer.ogg` |
| `SonidoSoltarPastel` | suelta el pastel al recibir daño | `audio/sfx/krampus_suelta_pastel.ogg` (bramido de rabia) |
| `SonidoAturdido` | se tambalea por el daño (`stagger`) | `audio/sfx/krampus_aturdido.ogg` |
| `SonidoEmbestida` | prepara la embestida (`charge_windup`) | `audio/sfx/krampus_embestida.ogg` (resoplido + cascos) |
| `SonidoPisoton` | golpe del pisotón (`slam`) | `audio/sfx/krampus_pisoton.ogg` (impacto grave) |
| `SonidoLatigo` | latigazo de cadena (`lash`) | `audio/sfx/krampus_cadena.ogg` (cadenas oxidadas) |

Pendiente (semana 2): pasos pesados y cadenas en bucle mientras camina, y una música de jefe (la pondría el HUD/audio escuchando
`KrampusDirector.boss_started`).

## Menú (día 6)

Buses verificados (`default_bus_layout.tres`): `Master`, `Music`, `SFX`, `Voice` y `Ambience` (los cuatro últimos envían a `Master`).
`Settings.apply()` (autoload, también al cambiar un dial de Opciones) convierte el volumen lineal 0..1 de cada bus a dB y
silencia el bus si vale 0. Los nodos de sonido del menú ya existen con el `stream` vacío (no suenan ni dan error):

| Nodo | Escena | Bus | Archivo sugerido |
|---|---|---|---|
| `MusicaMenu` (`autoplay`) | `scenes/menu/main_menu.tscn` | Music | `audio/music/menu.ogg` (villancico lento y apagado, en bucle) |
| `SonidoClic` | `scenes/menu/main_menu.tscn` | SFX | `audio/sfx/ui_clic.ogg` (suena al apuntar un regalo) |
| `SonidoTren` | `scenes/menu/train.tscn` | SFX | `audio/sfx/tren_juguete.ogg` (en bucle: suena mientras el tren llega y se para al frenar) |
| `SonidoAbrirRegalo` | `scenes/menu/present_multi.tscn`, `present_play.tscn`, `present_options.tscn` | SFX | `audio/sfx/regalo_abrir.ogg` (crujido de papel y cinta al abrirse la tapa) |
| `SonidoEngranaje` | `scenes/menu/options_gears.tscn` | SFX | `audio/sfx/engranaje.ogg` (clac mecánico al cambiar un ajuste) |

## Muerte (día 6)

Todos con `stream` vacío hasta que llegue la carpeta de audio.

**Jugador** (`scenes/player/player.tscn`, nodo `Derribo/Sonidos`, `AudioStreamPlayer3D`, bus `SFX`; suenan en todos los peers sobre el cuerpo):

| Nodo | Cuándo suena | Archivo sugerido |
|---|---|---|
| `SonidoDerribo` | el jugador cae derribado | `audio/sfx/jugador_derribado.ogg` (golpe seco + quejido) |
| `SonidoReanimado` | se levanta (reanimado o vuelto del minijefe) | `audio/sfx/jugador_reanimado.ogg` (bocanada de aire) |
| `SonidoMuerte` | se acaba el anillo y muere | `audio/sfx/jugador_muerte.ogg` (último aliento) |

**Interfaz de muerte** (`scenes/game/death_manager.tscn` y `scenes/ui/death/death_banner.tscn`, `AudioStreamPlayer`, no posicionales):

| Nodo | Bus | Cuándo suena | Archivo sugerido |
|---|---|---|---|
| `BannerMuerte/SonidoBanner` | `SFX` | cada banner (muerte, minijefe, vuelta a la vida) | `audio/ui/banner_muerte.ogg` (campana grave) |
| `Sonidos/SonidoMinijefe` | `SFX` | aparece el minijefe de un jugador | `audio/sfx/minijefe_reencarna.ogg` |
| `Sonidos/SonidoFinPartida` | `Music` | derrota (`GAME_OVER`) | `audio/music/fin_partida.ogg` |
| `Sonidos/SonidoVictoria` | `Music` | victoria al amanecer (`DAWN_WIN`) | `audio/music/victoria_amanecer.ogg` |

**Minijefe** (`scenes/ai/miniboss/miniboss_gnome.tscn`, nodo `Sonidos`, `AudioStreamPlayer3D`, bus `SFX`):

| Nodo | Cuándo suena | Archivo sugerido |
|---|---|---|
| `SonidoAparicion` | al aparecer junto al cuerpo | `audio/sfx/minijefe_aparicion.ogg` (risa grave de gnomo) |
| `SonidoAlerta` | fija una presa (`alert`) | `audio/sfx/minijefe_alerta.ogg` |
| `SonidoAtaque` | golpe con el poste (`attack`) | `audio/sfx/minijefe_poste.ogg` (madera contra carne) |
| `SonidoDolor` | recibe daño (`hurt`) | `audio/sfx/minijefe_dolor.ogg` |
| `SonidoMuerte` | derrotado (`dead`) | `audio/sfx/minijefe_muerte.ogg` (cerámica rompiéndose) |

Pendiente (semana 2): latido en bucle mientras el jugador local está derribado (viñeta) y pasos pesados del minijefe.
