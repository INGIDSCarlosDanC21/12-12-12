# Balance (B11)

Tabla de números del juego, dónde vive cada uno y la ronda de ajuste de B11.
Objetivo del ajuste: partida de 2 a 4 jugadores en la que los recursos alcanzan para la ruta
completa de mejoras sin sobrar, y la cabaña aguanta la primera oleada.

## Duración de la partida (pendiente de confirmar)

El GDD fija una partida de 15 min con **dos** noches (Día 1 4 min, Noche 1 3, Día 2 3, Noche final 5;
`resources/game/match_config.tres`). El encargo hablaba de 3 noches y 25-35 min: eso cambia una
mecánica del GDD y las fases de `GameState`, así que **no se ha tocado**. Si se confirma, la forma
más barata es escalar las cuatro duraciones de `match_config.tres` x2 (Día 1 8, Noche 1 6, Día 2 6,
Noche final 10 = 30 min) y tocar solo `siege_event_*` y `spawn_interval_s` en proporción.
Una tercera noche exige una fase nueva en `GameState` y en `Docs/CONTRATOS.md`.

## Dónde vive cada número

| Familia | Dónde está | ¿Cubierto por un `.tres` de balance? |
|---|---|---|
| Rendimiento y daño de herramientas por nivel, carne, ordeño | `resources/game/balance.tres` | Sí |
| Defensas (barricada, cubierta metálica, cristal, refuerzos, pinchos, trampa) | `balance.tres` | Sí |
| Chimenea (radio, luz, combustible, costes de mejora), mesa (costes) | `balance.tres` | Sí |
| Asedio y reglas rotas (presión, eventos, fuego apagado, ira) | `balance.tres` | Sí |
| Inventario (25 ranuras, pilas de 99, barra rápida de 5) | `scripts/inventory/inventory.gd` | **No** (en script) |
| Krampus: vida, velocidades, patrones, forma final | `resources/ai/krampus/boss_balance.tres` | Sí (antes vacío: usaba los valores por defecto del script; ahora fija los ajustados) |
| Fases, ira, ventana de reanimación | `resources/game/match_config.tres` | Sí |
| Oleadas (cantidad, ritmo, extra por jugador e ira) | `resources/waves/*.tres` | Sí |
| Tablas de botín | `resources/loot/*.tres` | Sí |
| Costes de recetas (madera, piedra, mineral) | `resources/recipes/*.tres` | Sí |
| Daño y munición de armas y herramientas | `resources/items/*.tres` | Sí |
| Vida, daño y velocidad de criaturas | `@export` en `scenes/ai/**` (nodo de la criatura) | **No** (en escena) |
| Golpes, cantidad y reaparición de árboles, rocas y vetas | `@export` en `scenes/world/resources/*.tscn` | **No** (en escena) |
| Frío (pérdida por segundo, umbrales) | `@export` en `scripts/player/temperature.gd` | **No** (en script) |
| Combustible de chimenea y antorcha | `@export` en `fireplace.gd` y `torch_behavior.gd` | **No** (en script) |

Huecos conocidos: las tres filas "No" siguen dispersas. Pasarlas a `BalanceData` exige cablear los
scripts y reescribir escenas; queda como tarea aparte, no se hizo en B11.

## Números de referencia (sin cambios)

| Dato | Valor |
|---|---|
| Reno zombi | 100 vida, 12 daño cada 1,3 s |
| Rodolfo | 280 vida, 22 daño cada 1,5 s, 60 a estructuras |
| Gnomo / Fantasma | 20 vida / 100 vida, 3 daño |
| Minijefe gnomo | 300 vida, 30 daño |
| Hacha 25, pico 20, cuchillo 14, martillo 10, Krampus Bane 40, Desert Eagle 45 (cargador 7), escopeta 80 (cargador 2) | `resources/items` |
| Árbol 4 golpes x3 madera (reaparece 240 s), roca 5 golpes x3 piedra, veta 6 golpes mineral; el recurso suelta **un solo montón** con etiqueta `xN` | escenas de recursos |
| Oleada Noche 1 | 3 renos + 0,5 por nivel de ira + 1,0 por jugador extra; 2 gnomos fijos desde el 15 % de la noche; Rodolfo desde ira 3 |
| Oleada Noche final | 6 renos + igual escala; Rodolfo escala con jugadores |
| Frío | de día 0,05/s con chamarra y 0,3/s sin ella (suelo 35, no mata); de noche 0,4/s con antorcha, 1,25/s con chamarra y 3,5/s sin nada (letal en ~29 s) |
| Combustible de chimenea | 360 s máx., 60 s por leño |

## Ronda de ajuste

| Valor | Antes | Ahora | Por qué |
|---|---|---|---|
| `barricade_health` | 150 | 180 | Rodolfo hace 60 a estructuras: 150 caía en 3 golpes y dejaba cero margen; 180 aguanta 3 golpes con restos y un reno zombi (9 dps) tarda 20 s. |
| `window_glass_health` | 30 | 40 | Con 30 un solo golpe de Rodolfo la rompe; 40 exige dos y da tiempo a cubrirla (regla 5). |
| `spikes_damage` | 8 (13 dps) | 10 (17 dps) | Un reno zombi (100 vida) tardaba 7,5 s en morir sobre pinchos; ahora 6 s y compensa su coste de 2 de madera. |
| `bear_trap_damage` | 30 | 40 | Con 30 solo quitaba el 30 % de un reno; 40 más 3 s inmovilizado permite rematarlo con un golpe de hacha o cuchillo. |
| Chimenea, mejora 1 | piedra 6, madera 4 | piedra 5, madera 4 | Piedra escasa (3 rocas en el mapa). |
| Chimenea, mejora 2 | mineral 4, piedra 10 | mineral 3, piedra 8 | Mineral y piedra escasos. |
| Chimenea, mejora 3 | mineral 8, piedra 10, madera 6 | mineral 5, piedra 8, madera 6 | Era el mayor consumo de mineral de toda la ruta. |
| Mesa, nivel 2 | mineral 2, piedra 6 | mineral 2, piedra 5 | Mismo motivo. |
| Mesa, nivel 3 | mineral 6, piedra 8 | mineral 4, piedra 7 | Mismo motivo; el nivel 3 desbloquea Krampus Bane. |
| Veta de mineral `drop_count` | 2 | 3 | El mapa tiene unas 3 vetas; la ruta completa pedía unos 30 de mineral y salían 6 por ciclo. |
| Veta de mineral reaparición | 300 s | 240 s | Alinea con el árbol; da una segunda cosecha antes de la noche final. |
| Roca reaparición | 180 s | 150 s | Piedra escasa; cabe una cosecha más por mitad de partida. |
| Krampus `max_health` | 1500 | 1400 | Con 2 jugadores, el cargador del Desert Eagle (7 x 45) aporta solo 315; bajar el total acorta la fase pre-forma-final sin tocar la forma final (que exige Krampus Bane). |
| Krampus `health_per_extra_player` | 400 | 350 | Con 4 jugadores pasa de 2700 a 2450. Pesa menos que el aumento de armas y munición por jugador. |
| Krampus `roar_blackout_s` | 12 | 10 | Con enfriamiento de 28 s, 43 % del tiempo a oscuras era demasiado. |
| Krampus `roar_cooldown_s` | 28 | 30 | Idem: 33 % a oscuras. |
| Krampus `chains_cooldown_s` | 9 | 10 | Con aturdimiento de embestida y cadenas, casi no había descanso entre patrones. |
| Noche 1 `count` (renos) | 4 | 3 | Objetivo: noche 1 superable casi siempre al primer intento. Con 4 jugadores la oleada baja de 8 a 6 renos. |
| Noche 1 `extra_per_wrath_level` | 1,0 | 0,5 | La ira sube sola al romper reglas; el escalado duplicaba la oleada en el peor momento. |
| Noche 1 `extra_per_player` | 1,5 | 1,0 | 4 jugadores ya aportan más manos y armas; 1,5 por jugador extra llenaba `max_alive`. |
| Noche 1 `initial_delay_s` / `spawn_interval_s` | 10 / 12 | 12 / 14 | Da unos segundos más para cerrar la cabaña y reparte la oleada sin picos. |
| Gnomos de Noche 1 y Noche final | escalaban con ira y jugadores | 2 fijos (Noche 1) | La línea de gnomos era la que desbordaba el tope con 4 jugadores; los renos ya escalan. |

## Sin cambios, a vigilar en la prueba con visor

- Tablas de botín: la munición y los cofres épicos (escopeta) parecen repartidos con equilibrio; falta
  jugarlo para saber si sobra o falta munición contra Krampus con 4 jugadores.
- Oleadas: con los valores nuevos, Noche 1 da 3 renos + 1,0 por jugador extra (6 con 4 jugadores) y
  2 gnomos fijos. Si aun así se siente injusta, bajar `max_alive` de 10 a 8.
- Frío de noche (3,5/s, umbral de congelación 25): con 100 de temperatura quedan unos 29 s en el
  exterior sin calor; coherente con la regla 11.

Nota: estos ajustes son aritmética sobre los valores del proyecto, no una partida medida; la ronda
real con visor y 2-4 jugadores (B11) debe confirmarlos.
