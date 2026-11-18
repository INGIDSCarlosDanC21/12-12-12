# 12/12/12 — Documento de diseño (GDD)

## Concepto
Videojuego de terror en realidad virtual (y PC) de exploración en un mundo abierto (*freeroam*). El jugador es el nuevo dueño de una cabaña aislada y debe cuidarla siguiendo las reglas extrañas de una vieja nota, mientras sobrevive a criaturas de la temporada navideña. El enemigo principal es **Krampus**.

## Plataformas y objetivos técnicos
- Realidad virtual y PC, sin problemas de compatibilidad entre dispositivos.
- Multijugador cross-plataforma entre **PCVR, PC y Meta Quest 2**.
- Mínimo **75 FPS** en los dispositivos VR.
- **MVP en 2 semanas.**

## Reglas de la cabaña (nota antigua)
1. No te comas el pastel.
2. No presiones el botón verde.
3. No dejes que el fuego se apague.
4. Mantén un vaso lleno de leche con galletas antes de que anochezca.
5. No dejes puertas ni ventanas abiertas sin cubrir antes de dormir.
6. Si un cuadro cambia, voltéalo.
7. Si escuchas cascabeles o risas, no te muevas.
8. Decora el árbol de navidad cada vez que deje de brillar.
9. Alimenta a los animales antes de que se ponga la luna.
10. No abras la puerta si alguien la toca; no hay nadie detrás.
11. Jamás salgas de noche o ellos vendrán.

> Nota: la regla 10 dice "No habras la puerta" en el texto original; se asume "No abras" en el juego final.

## Mecánicas del jugador
- Primera persona, en VR y PC.
- **Bolsillos** con objetos (VR) y **botón de inventario** (PC).
- Golpear objetos con hacha o pico para extraer materia prima (madera, rocas, minerales).
- **Crafteo** para mejorar la cabaña y hacer extensiones más seguras.
- **Linterna** como objeto de mano.
- Correr.
- Agacharse para hacer menos ruido.
- Interactuar con objetos (puertas, mesas de trabajo).
- **Agarrar objetos** que no forman parte del inventario manteniendo click sobre ellos. Se pueden rotar con la tecla R (PC) o con la mano (VR).

## Inventario
| Objeto | Función |
|---|---|
| Hacha | Extraer madera |
| Pico | Extraer minerales y rocas |
| Linterna | Visión; ahuyenta fantasmas |
| Armadura contra frío | Armadura |
| Frutas y verduras | Comida |
| Carne de animal | Comida |
| Agua | Bebida |
| Leche | Bebida (regla 4) |
| Galletas | Comida (regla 4) |
| Escopeta | Arma |
| Balas de escopeta | Munición |
| Desert Eagle | Pistola |
| Balas de pistola | Munición |

## Interactuables
- Objetos del mapa que se pueden cargar o agarrar, con lógica de peso y manejo similar al de una persona.
- Puertas.

## Animales (NPC no hostiles o de recurso)
- **Ganado:** granja encarcelada. Se puede alimentar al ganado, solo para cumplir la regla 9.
- **Animales salvajes** (fuente de comida):
  - **Cerdos** de distintos tamaños. Corren frenéticamente y se mueven en zigzag para morder y dañar.
  - **Osos.** Se mueven lento y te rasguñan, empujándote.
  - **Vacas.** No atacan; solo huyen si las dañas.
  - **Lobos.** Van siempre en grupo con un alfa y te rodean para atacar.
  - **Gallinas.** No atacan, pero van siempre en grupo. Si atacas a una, todas vienen por ti. Hacen poco daño, pero cada golpe ralentiza al jugador.
- **Animales alfa:** más fuertes que los normales, pero dan más comida.

## Enemigos
- **Reno distorsionado zombificado.**
- **Rodolfo el reno zombificado:** es un alfa.
- **Gnomo:** no se mueve mientras lo miras. Cuando no lo miras, se acerca y explota.
- **Fantasma de Navidad:** se disipa al iluminarlo con la linterna.

## Enemigo principal: Krampus
Criatura mitad cabra y mitad demonio, de origen pagano y alpino, contraparte oscura de San Nicolás. Pelaje negro, grandes cuernos, cadenas oxidadas y un saco a la espalda. Castiga y se lleva a quienes se portaron mal. Su tradición se celebra cada 5 de diciembre en la *Krampusnacht*, con desfiles llamados *Krampuslauf*.

## Menú de inicio
Un árbol de Navidad al centro con tres regalos en el suelo y un tren que los trae:
- **Izquierda:** Multijugador.
- **Centro:** Jugar.
- **Derecha:** Opciones.

El cuarto regalo, Educativo (añadido el 2026-11-08), queda **oculto en la versión final del juego principal** (decisión del 2026-11-11). El modo educativo no se borra: se reactiva con `show_educativo` en `main_menu.gd`.

## Narrativa (trailer)
El protagonista ganó una cabaña en medio de la nada en un concurso de "la persona más trabajadora del mundo", porque su oponente no se presentó por estar trabajando. Se mudó lejos de la ola de inteligencia artificial y el desempleo. El vendedor le presenta el terreno (vacas, cerdos, lago, árboles y la cabaña) y le pide firmar la compra. Advierte que no deje que el fuego se apague, "o él vendrá". Al preguntar, el vendedor se va a toda velocidad gritando que es libre.

## Decisiones confirmadas
- **Motor:** Godot 4.7 con GDScript.
- **Multijugador:** solo red local (LAN) por ahora, hasta **4 jugadores**.
- **Idiomas:** español e inglés.
- **Partida de 30 minutos** (definitiva desde el 2026-11-11; antes 15): Día 1 (8 min) → Noche 1 (6 min, primera oleada) → Día 2 (6 min) → Noche final (10 min, Krampus viene por el pastel). Valores en `resources/game/match_config.tres`.
- **Plataformas de la versión final:** PC (teclado y ratón), PCVR, Meta Quest 2 nativo y Android táctil.
- **Coro de fantasmas (minijefe de la capilla):** el núcleo se abre cuando los tres fantasmas han brillado con linterna dentro de una ventana de 6 s y queda abierto 8 s; una sola linterna basta.
- **Romper una regla** provoca anomalías por todo el mapa: glitches, un ambiente más aterrador, enemigos nuevos, más dificultad y la activación del péndulo del cascabel.
- **Minijefe:** el jugador muerto reencarna en un **gnomo de jardín musculoso**. Si sus compañeros lo derrotan, vuelve a la vida.

## Frío y fuego
- La chimenea y las fogatas calientan al jugador. Si la temperatura llega a cero, el jugador muere de frío.
- La **chamarra** (armadura contra el frío) permite tolerar el exterior durante el **día**.
- De **noche** es obligatorio llevar una **fuente de luz de fuego** para salir.
- Mientras se lleva el fuego **no se pueden usar armas ni otros objetos** al mismo tiempo.

- Salir de noche **con** fuego te salva del frío, pero **igual rompe la regla 11** (sube la ira y dispara anomalías).
- En VR, con el fuego en una mano, la otra mano puede agarrar e interactuar (puertas, objetos sueltos), pero no usar armas ni objetos del inventario.

## Preguntas abiertas
- Diseño de la mecánica de "cuidar la cabaña" (cómo se verifica cada regla).
