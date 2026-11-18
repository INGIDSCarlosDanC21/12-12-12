# Plan: animaciones en primera persona y pulido de interfaz (PC y Android)

Estado: **implementado en PC y VR; pendiente de revisión en Android (C3)**.

Decisiones del usuario (2026-11-09): el filo del hacha mira al frente, como un hacha de mano real; las manos de Quest 2 tienen animación de andar y correr sin fijar el movimiento de la mano real; el Android de prueba es un Reno 11.

### Ejecución
- **A (hecho):** `item_model_turn_deg` en `viewmodel_hands.gd` gira 90° el modelo (no la raíz) de hacha, hacha de hierro, pico, pico de hierro y martillo. Capturas en `tools/logs/captura/mano_*.png` (antes en `antes_a1/`): la cabeza mira al frente. Falta tu aprobación visual.
- **B (hecho):** `hand_animator.gd` tiene una fase por mano (la izquierda desfasada media vuelta), tomada de `PCRig.get_bob_phase()`. La mano que sujeta algo se amortigua (`hold_damp`). Al correr sube la amplitud y los brazos van recogidos. Pruebas en `test_manos_animacion.gd` (43/43).
- **VR (hecho, solo visual):** `vr_rig.gd` aplica el mismo `HandAnimator` a los modelos de mano (`LeftHandModel`/`RightHandModel`). No toca los controladores, así que el agarre y el teletransporte siguen la mano real. `test_vr_flujo` pasa (47/47). Falta sentirlo en Quest 2.
- **D (hecho):** nombres cortos en la barra (`ITEM_X_HUD`, con respaldo al nombre completo), "Mochila" en la quinta casilla, valor en los títulos de Vida y Temperatura, casilla vacía más visible. Capturas a 1280×720 y 2400×1080 sin cortes.
- **C (pendiente):** los controles táctiles de Android no se ven en PC. Hay que revisarlos en el Reno 11.

### Pendiente fuera de este cambio
- La escopeta se ve inclinada en la mano. No la he tocado porque no la pediste.
- `verificar.ps1` marca "4 resources still in use at exit" como error. `test_escenas` pasa 32/32. No lo he comparado con una versión anterior.
Fecha: 2026-11-09. Alcance: viewmodel del PC (`pc_rig.gd`), que usan el ratón de PC y la build plana de Android con controles táctiles; y la interfaz de la barra de objetos y el HUD.

---

## 1. Diagnóstico (verificado en código y capturas)

### 1.1 Objetos tumbados, mirando a izquierda y derecha
- `scripts/player/viewmodel_hands.gd:121-135` (`apply_item_pose`) solo gira el **nodo raíz** de la copia del objeto (`node.rotation`). El modelo visual que cuelga dentro (`ModeloVisual` → `assets/world/props/hacha_madera.glb`) conserva la orientación con la que se hizo para el suelo del mundo.
- Evidencia: `tools/logs/captura/mano_axe.png`. El mango está vertical y la cabeza del hacha queda horizontal, de modo que el filo mira a los lados.
- Los giros de `item_rotation_deg` (-6° en X) y `item_rotation_overrides` solo corrigen la inclinación hacia delante. No corrigen el giro sobre el eje del mango, que es lo que hace que se vea tumbado.
- **Hipótesis a confirmar (paso A1):** el eje del mango de cada `.glb` no coincide con el eje que espera la mano. No está medido todavía para los 8 objetos.

### 1.2 Las manos no se animan de forma independiente
- `scripts/player/hand_animator.gd`: hay una sola fase `_phase` y ambas manos la leen con `sin(_phase) * side` y `absf(s)`.
  - El cabeceo es opuesto entre manos (espejo), pero la subida (`absf`) es **igual para las dos**: las manos suben y bajan a la vez, como si no hubiera pasos.
  - El balanceo es de 12 mm y 3° al andar. Al correr solo sube un 80 % más. Casi no se ve.
- Las manos son mitones rojos estáticos, sin huesos ni clips. Todo el movimiento es procedural.
- Al correr no cambia la postura (brazos más adelantados o recogidos), solo la amplitud.
- **Ya existe la cadencia real**: `pc_rig.gd` tiene `_bob_phase`, `get_step_rate()` (1.8 pasos/s andando, 2.6 corriendo) y la señal `footstep(sprinting)`. El balanceo de cámara ya usa esa fase, pero las manos no la comparten.

### 1.3 Móvil (Android)
- Android sin visor usa el mismo `PCRig` (`pc_rig.gd:1048-1063`). Por tanto, cualquier arreglo del viewmodel le afecta por igual. Los controles táctiles se crean solo en Android sin XR.
- No se ha comprobado todavía si los botones táctiles tapan las manos o la barra de objetos en pantallas de 16:9 y 20:9.

### 1.4 Quest 2 (VR)
- Las manos de VR son las de XR Tools con blend tree (`addons/godot-xr-tools/hands/animations/*/hand_blend_tree.tres`, modificados en el git status). Son un sistema distinto. **No entra en este plan** salvo que quieras que también anden al caminar.

### 1.5 Interfaz (capturas `hud_pc.png` y capturas de partida de esta sesión)
- Nombres de la barra cortados: "Hacha ...", "Pico de ...", "Invent..." (`scripts/ui/hud_slot.gd`, el nombre se pone tal cual con `tr(data.name_key)`).
- Ranura 5 vacía casi invisible, con el número en rojo tenue.
- Colores de las teclas (1-5, Tab) sin criterio claro. La tecla del inventario tiene un estilo distinto.
- Barras de Vida y Temperatura con etiquetas pequeñas y sin escala de lectura.
- Pendiente medir en 1280×720, 1920×1080 y 2400×1080.

---

## 2. Fases

### Fase A: orientación de objetos en mano (PC y Android)
- **A1. Medir.** Script headless que imprima, para cada objeto del viewmodel (axe, pickaxe, hammer, knife, bucket, flashlight, torch, shotgun), el eje del mango y el eje del filo a partir del AABB de sus mallas. Salida en `tools/logs/captura/medir_orientacion.txt`. Reutilizar `tools/captura/medir_items.gd` si sirve.
- **A2. Corregir el modelo, no el nodo raíz.** Añadir una corrección por objeto (`hand_basis` o similar) que se aplique al `ModeloVisual` de la copia dentro del viewmodel. Así no se rompen el colisionador ni el desfase de la física. Los valores van en `item_rotation_overrides` o en `ItemData` (decidir en A1).
- **A3. Capturar y aprobar.** `tools/captura/captura_mano.gd` genera `mano_<id>.png` para los 8 objetos. Tú apruebas cada captura antes de cerrar la fase. Criterio: el filo mira al frente, no a los lados; el mango sale hacia delante y hacia arriba; ningún objeto queda tumbado.
- **Criterio de salida:** las 8 capturas aprobadas; `test_equipado_manos.gd` y `test_manos_animacion.gd` en verde.

### Fase B: manos independientes (PC y Android)
- **B1. Una fase por mano.** La mano derecha sigue la fase de la cámara (`_bob_phase` del rig) y la izquierda va desfasada 180°. Así cada mano sube cuando avanza el pie contrario.
- **B2. Exponer la fase del rig.** Añadir un getter en `pc_rig.gd` para `_bob_phase`. `hand_animator.gd` la usa en vez de su propio `_phase`. Así las manos y la cámara van en el mismo ritmo.
- **B3. Amplitud por estado.** Andar: 3-4 cm de subida, 6-8° de cabeceo. Correr: 6-8 cm, 14-18°, con los brazos adelantados y un leve retraso de la mano libre. Agachado: pasos cortos. Valores iniciales; se ajustan en la prueba real.
- **B4. Mano con objeto.** La mano que sujeta algo amortigua el balanceo (50 %) para que el objeto no salte. La mano libre sí balancea del todo.
- **B5. Pruebas.** Ampliar `test_manos_animacion.gd`: (a) las dos manos tienen fase opuesta; (b) la subida de cada mano alterna; (c) la amplitud al correr es mayor que al andar; (d) con objeto, la amplitud de la mano derecha es menor.
- **Criterio de salida:** pruebas en verde y captura en movimiento (secuencia con `--fixed-fps` y `--write-movie`) revisada por ti.

### Fase C: móvil (Android sin visor)
- **C1. Diseño de pantalla.** Revisar que las manos y el viewmodel no tapen los controles táctiles, con capturas en 16:9 y 20:9 (con `--resolution` en Godot).
- **C2. Tamaños táctiles.** La barra de objetos y el inventario deben cumplir un mínimo de 44 px (en escala de `UiPrefs.touch_scale`).
- **C3. Prueba en dispositivo.** Export de APK de depuración y prueba en tu Android. Esto solo lo puedes hacer tú; yo dejo el APK y la lista de comprobaciones.
- **Criterio de salida:** sin solapes en 16:9 y 20:9; tu confirmación en dispositivo.

### Fase D: pulido de interfaz (PC y Android)
- **D1. Nombres sin cortar.** Añadir `short_name_key` a `ItemData` (p. ej. "Hacha", "Pico", "Cuchillo"), con textos en `textos.csv` (es y en). Si no existe, reducir la fuente con `autowrap` o `clip_text` sin puntos suspensivos. Así no cambia ningún dato de juego.
- **D2. Ranura vacía visible.** Borde tenue y número en color normal; sin el aspecto de ranura apagada.
- **D3. Teclas coherentes.** Un solo estilo para 1-5 y Tab; el color rojo solo para la ranura activa.
- **D4. Barras legibles.** Etiquetas con valor numérico o estado textual (Frío, Templado…), más contraste.
- **D5. Revisión en tres resoluciones.** Capturas de `hud_pc.png` a 1280×720, 1920×1080 y 2400×1080, antes y después.
- **Criterio de salida:** sin textos cortados en es ni en en; capturas antes/después aprobadas.

### Fase E: verificación y cierre
- `tools/verificar.ps1` antes de cada commit.
- Pruebas nuevas o ampliadas en verde.
- Actualizar `Docs/TAREAS.md` (las tareas de A a D) y `Docs/ESTADO.md` con el bloque de handoff.
- Un commit por fase, con la línea `Co-Authored-By`. Sin push ni merge de ramas de agente.

---

## 3. Qué no sé todavía
- Si el problema de los objetos viene solo de la orientación del `.glb` o también de cómo se cuelga la copia. Lo confirma A1.
- Si los botones táctiles tapan algo en Android. Lo confirma C1.
- Cómo se ve la animación en movimiento real. Las capturas fijas no bastan para juzgar el balanceo; hace falta la secuencia (B5).

## 4. Preguntas para ti
1. ¿Te sirve que el filo del hacha mire al frente como en un hacha de mano real, o prefieres otro ángulo de agarre?
2. ¿Quieres que las manos de Quest 2 también caminen y corran con esta misma lógica, o solo las del PC y Android?
3. ¿Qué Android tienes para la prueba (modelo y resolución)?
