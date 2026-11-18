# Plan semana 2 — jugabilidad, exploración y "juice"

Plan confirmado con el usuario el 2026-11-02 (NPC, leche y galletas, final secreto, día 8 empieza por las mejoras rápidas). Aún no hay nada implementado.

## 0. Diagnóstico (partida de prueba del usuario, 2026-11-02)
- El día y la noche se sienten vacíos: faltan herramientas a mano, las mecánicas son flojas y nada aprieta.
- Los objetos grandes no caben por la puerta, así que no se pueden llevar a la mesa de trabajo.
- El mapa está vacío: no hay estructuras, NPC, cofres, recompensas ni minijefes que inviten a explorar.
- No hay forma de conseguir la leche y las galletas de la regla 4.
- El suelo es plano y falta vegetación.
- El jugador no ve sus pies ni sus manos.
- Faltan modelos 3D.
- El cielo es pobre: faltan nubes, estrellas y aurora.
- El movimiento es raro: andar ya es demasiado rápido (3,5 m/s) y no hay inercia ni sensación de peso.
- Menú: el texto flotante se ve feo.
- La nieve en pantalla atraviesa paredes, encoge al mirar al suelo y cae dentro de casa.

## 1. Pilares (referencia: *99 Nights in the Forest* de Roblox + reglas extrañas)
1. **Tensión constante.** Toda la partida combina dos amenazas: la supervivencia (frío, fuego, hambre, criaturas) y las reglas, que la nota del dueño anterior da como una lista explícita y que el reloj y el cuaderno muestran.
2. **El día prepara la noche.**
   - Explorar trae recursos, botín y las ofrendas de la regla 4.
   - El crafteo convierte eso en defensas que funcionan de verdad.
   - Mejorar la chimenea por niveles agranda el radio de calor y de luz y cuesta leña, como la hoguera de *99 Nights*.
3. **La exploración se recompensa.** Hay puntos de interés con cofres, NPC, minijefes y fragmentos para el final secreto. Inspiración: las fortalezas de cultistas de *99 Nights*, que se rearman y dan cofres.
4. **Juice.** Cada acción responde con sonido, partículas, vibración, cámara y animación.
5. **Victoria.**
   - Final normal: sobrevivir hasta el amanecer de la noche final.
   - Final secreto: derrotar a Krampus en su forma final. Para eso hacen falta los fragmentos de las mazmorras, que forjan el arma que lo daña.

## 2. Bloques de trabajo

### B1. Movimiento, cuerpo y sensación (Opus)
- **Velocidades:** andar 2,2 m/s, correr 4,5 m/s, agachado 1,2 m/s.
- **Peso al moverse:** aceleración y frenada con inercia, y estamina para correr (barra discreta en el HUD y en el reloj VR).
- **Cámara en PC:**
  - balanceo de cabeza al andar (sutil, desactivable);
  - sacudida al recibir daño y al caer;
  - el campo de visión se abre un poco al correr;
  - aterrizaje con hundimiento.
- **Pasos:** sincronizados con la cadencia, no por distancia fija.
- **Cuerpo propio en PC:** se ven las piernas y el torso con la cabeza oculta (escala 0 del hueso Head en la vista local) y manos con los mitones del modelo en el viewmodel.
- **Cuerpo propio en VR:** manos de mitón en lugar de las manos genéricas de XR Tools. Las piernas en VR son opcionales y van detrás (es lo habitual en VR). Para los demás jugadores, los brazos del cuerpo apuntan a las manos del mando con IK de dos huesos.
- **Respuesta a cada golpe:**
  - pausa breve al golpear;
  - astillas en el árbol, chispas en la piedra, sangre o nieve en las criaturas;
  - vibración del mando en VR;
  - viñeta roja al recibir daño;
  - animación de la herramienta (balanceo en PC, peso en VR).

### B2. Agarre y transporte
- **Rotar en tres ejes:** en PC, R más rueda o Shift cambia de eje; en VR basta la muñeca, que ya funciona. Es la solución que pidió el usuario.
- **Tamaño de los objetos:** los troncos dejan de ser monolitos y salen en 2–3 piezas al talar; nada del juego debe medir más de 1,6 m.
- **Trineo arrastrable:** carga varias piezas y entra por la puerta. Ayuda en coop y da mucho juego.
- **Mesa de trabajo:** acepta materiales en un radio de 2 m, para no tener que soltarlos exactamente encima.

### B3. Herramientas y equipo
- **Kit inicial en la cabaña:** hacha de piedra, linterna y la nota.
- **Niveles de herramienta:** de piedra a hierro y a "hierro de Krampus" (el arma del final secreto).
- **Efecto del nivel:** el nivel da más rendimiento y más daño. Sin durabilidad, para no complicarlo.
- **Herramientas nuevas:**
  - **martillo:** monta las mejoras;
  - **cuchillo:** despieza la caza para obtener carne;
  - **cubo:** recoge agua del lago.

### B4. Bucle del día y crafteo con efecto real
- **Lista de tareas explícita:** la nota y el reloj la muestran con temporizador, en el orden "antes del anochecer".
- **Escasez:** cerca de la cabaña los recursos son pocos; lejos hay más y mejores.
- **Mejoras de la cabaña con efecto real:**
  - **barricada:** bloquea la ventana hasta que la rompen (tiene vida);
  - **pinchos:** dañan y ralentizan a quien entra;
  - **refuerzo de pared y puerta:** dan más vida;
  - **trampa para osos:** se puede colocar en el exterior.
- **Chimenea por niveles (1 a 4):** cada nivel da más radio de calor y de luz, quema más tiempo y desbloquea recetas.
- **Mesa de trabajo por niveles (1 a 3):** desbloquea los niveles de herramienta.

### B5. Exploración: puntos de interés, NPC, cofres y minijefes
Habrá de 6 a 8 puntos de interés generados como escenas fuente editables y colocados en el mapa. Cada uno tiene botín y peligro.

| Punto de interés | Peligro / minijefe | Recompensa típica |
|---|---|---|
| Granja abandonada | Gallinas y cerdos | Leche, comida, cubo |
| Cabaña del leñador | Lobos y lobo alfa | Hacha de hierro, galletas |
| Mina | Oscuridad, gnomos y minijefe "Gnomo minero" | Mineral, pico de hierro, fragmento |
| Capilla en ruinas | Fantasmas y minijefe "Coro de fantasmas" | Munición, fragmento |
| Campamento de cazadores | Oso alfa | Escopeta, munición, trampas |
| Taller de Santa en ruinas (mazmorra principal) | Elfos corruptos y minijefe "Muñeco de nieve maldito" | Galletas especiales, fragmento, receta del arma |
| Establo de los renos | Renos zombis y Rudolph (de día, dormido) | Fragmento, carne |
| Torre de vigía | Nada (vista y mapa) | Mapa en el reloj, bengala |

- **Cofres:**
  - tablas de botín `.tres` (común, raro, épico) que el host abre y replica;
  - se rellenan cada día (como en *99 Nights*).
- **NPC preprogramados** (confirmados por el usuario):
  - **ermitaño amistoso:** intercambia objetos y da pistas y lore;
  - **elfos corruptos:** enemigos humanoides que guardan el taller;
  - **viajero perdido:** si lo llevas a la cabaña, da una recompensa (como los niños perdidos de *99 Nights*).
- **Leche y galletas:** se consiguen en los cofres y mazmorras. Además, la vaca de la granja da leche con un cubo, para que no dependa solo de la suerte (confirmado).
- **Fragmentos de Krampus:** son 3–4. Con todos se forja el arma del final secreto en la mesa de nivel 3.

### B6. Noches con presión
- **Por dónde atacan:** las oleadas eligen puertas y ventanas. Lo que no tenga barricada cae y hay que defenderlo.
- **Eventos de reglas de noche:** golpes en la puerta, cuadros, cascabeles y el árbol que deja de brillar ocurren a la vez que el asedio.
- **El fuego es el recurso crítico:** si se apaga, llega el frío, más enemigos y más ira.
- **Ira visible:** el cuaderno anota la regla rota y suenan un audio y un efecto visual.

### B7. Krampus completo (Opus)
- **Patrones de la fase jefe:**
  - embestida;
  - cadenas que arrastran al jugador;
  - lanzar el saco, que suelta gnomos;
  - rugido que apaga las luces;
  - salto al tejado.
- **Forma final:** solo es vulnerable al arma de los fragmentos. Derrotarlo da el final secreto: una escena corta con texto y la cabaña al amanecer.
- **Si no tienes el arma:** sobrevivir hasta el amanecer da el final normal.

### B8. Mundo y ambiente (Opus)
- **Relieve:** ruido en el generador de Blender (montículos, hondonadas, ventisqueros), sin romper la malla de navegación ni los marcadores.
- **Vegetación** con ProtonScatter o MultiMesh, respetando el presupuesto de llamadas de dibujo:
  - pinos de 3 variantes;
  - abetos nevados;
  - arbustos secos;
  - troncos caídos;
  - rocas;
  - tocones.
- **Cielo de día:** shader con nubes falsas (capas de ruido animadas).
- **Cielo de noche:** estrellas que titilan, luna y aurora boreal (cinta animada en el shader del cielo). Todo en un solo `ProceduralSky` o shader de cielo, cuyo coste en el Quest es casi nulo.
- **Ambiente:** niebla baja, copos iluminados por la linterna y aliento visible con el frío.

### B9. Nieve de pantalla (problema concreto)
**Causa:** la nieve vive en la cámara del viewmodel en PC, que dibuja por encima de todo (por eso atraviesa paredes) y va pegada a la cámara (por eso encoge al mirar al suelo).

**Solución por capas:**
1. **Nieve en el mundo:** `GPUParticles3D` con `local_coords = false` en una caja de unos 20×10×20 m que sigue al jugador solo en la horizontal y queda unos 8 m por encima. Los copos caen en el espacio del mundo, así que mirar al suelo ya no cambia nada.
2. **Interiores:** si el jugador está en `indoor_zone` (`Temperature.is_point_indoors`), la emisión se apaga y los copos que quedan mueren con un fundido. Es barato y sirve en todas las plataformas.
3. **Tejados:** colisión `GPUParticlesCollisionHeightField3D` con `follow_camera_enabled`, de baja resolución, que actualiza al moverse. Los copos chocan con el tejado y no entran por las ventanas. Solo en PC; en el Quest basta la capa 2 (validar el coste).
4. **Viewmodel:** la nieve sale de la cámara del viewmodel.
5. **Shader del copo:** desvanecimiento por profundidad y por cercanía a la cámara, sin tarjetas grandes.

### B10. Menú
- El texto pasa a la cara frontal de cada regalo con neón que parpadea como un tubo averiado (shader emisivo con ruido y cortes).
- Los iconos de Nevid se sientan encima de cada regalo, apoyados, en vez de flotar como cartel.

### B11. Equilibrio y prueba
- Tabla de números en un `.tres`: vida, daño, tiempos, rendimiento y botín.
- Partida con visor y 2–4 jugadores, OVR Metrics en el Quest, lista de bugs y una ronda de ajuste.

## 3. Modelos 3D para Tripo (estilo: dark Christmas horror estilizado-realista, colores apagados y desaturados, PBR sucio)
Presupuesto: criaturas 4–8k tris, jefes ≤12k, props 300–2k, estructuras 2–6k, texturas de 1K. Con textura y, en las criaturas, con Auto Rig.

**Criaturas y NPC** (prioridad P0 → P2)

| # | Modelo | P | Nota |
|---|---|---|---|
| 1 | Vaca de granja | P0 | Cuadrúpedo, rig |
| 2 | Cerdo salvaje | P0 | Cuadrúpedo, rig |
| 3 | Gallina | P0 | Rig |
| 4 | Fantasma de Navidad | P0 | Sin rig; translúcido en shader |
| 5 | Gnomo musculoso (minijefe del jugador muerto) | P0 | Bípedo, rig |
| 6 | Lobo alfa (repetir descarga) | P0 | Ya generado |
| 7 | Elfo corrupto | P1 | Bípedo, rig; enemigo del taller |
| 8 | Muñeco de nieve maldito (minijefe) | P1 | Rig sencillo |
| 9 | Ermitaño (NPC amistoso) | P1 | Bípedo, rig |
| 10 | Viajero perdido (NPC a rescatar) | P1 | Bípedo, rig |
| 11 | Gnomo minero (minijefe) | P2 | Variante del gnomo |
| 12 | Krampus forma final | P2 | ≤12k; más demoníaco |

**Herramientas y objetos de mano**

| # | Modelo | P |
|---|---|---|
| 13 | Hacha de piedra / hacha de hierro | P0 |
| 14 | Pico de piedra / pico de hierro | P0 |
| 15 | Martillo | P0 |
| 16 | Cuchillo de caza | P1 |
| 17 | Cubo de metal | P1 |
| 18 | Escopeta de doble cañón | P0 |
| 19 | Pistola (Desert Eagle) | P0 |
| 20 | Linterna antigua | P0 |
| 21 | Antorcha | P0 |
| 22 | Arma del final secreto ("garra de Krampus" o hacha de cuerno) | P2 |
| 23 | Bengala | P2 |

**Consumibles y recursos**

| # | Modelo | P |
|---|---|---|
| 24 | Vaso de leche / botella de leche | P0 |
| 25 | Plato de galletas de jengibre | P0 |
| 26 | Carne cruda y cocinada | P1 |
| 27 | Fruta y verdura invernal | P1 |
| 28 | Botella de agua / cantimplora | P1 |
| 29 | Caja de munición (escopeta / pistola) | P0 |
| 30 | Chamarra de abrigo (prenda) | P1 |
| 31 | Tronco cortado (pieza de 1 m) | P0 |
| 32 | Piedra / mineral de hierro / lingote | P0 |
| 33 | Fragmento de Krampus (cuerno roto brillante) | P1 |

**Cabaña, reglas y mejoras**

| # | Modelo | P |
|---|---|---|
| 34 | Pastel navideño (regla 1) | P0 |
| 35 | Botón verde en caja metálica (regla 2) | P0 |
| 36 | Chimenea de piedra con 4 niveles | P0 |
| 37 | Árbol de Navidad con adornos (regla 8) | P0 |
| 38 | Marco de cuadro (regla 6) | P1 |
| 39 | Mesa de trabajo, 3 niveles | P0 |
| 40 | Barricada de tablones | P0 |
| 41 | Pinchos de madera | P0 |
| 42 | Refuerzo de chapa | P1 |
| 43 | Trampa para osos | P1 |
| 44 | Trineo de madera | P1 |
| 45 | Cofre (común, raro, épico) | P0 |
| 46 | Péndulo con cascabel | P1 |
| 47 | Cama, mesa, silla, estantería y alfombra (interior) | P2 |

**Estructuras de exploración** (una pieza o pocas por estructura)

| # | Modelo | P |
|---|---|---|
| 48 | Granero abandonado | P0 |
| 49 | Cabaña del leñador | P0 |
| 50 | Entrada de mina con vigas | P0 |
| 51 | Capilla de madera en ruinas | P1 |
| 52 | Campamento de cazadores (tienda y hoguera) | P1 |
| 53 | Taller de Santa en ruinas | P1 |
| 54 | Establo de renos | P1 |
| 55 | Torre de vigía | P2 |
| 56 | Valla de granja, poste y farol | P1 |

**Vegetación y terreno**

| # | Modelo | P |
|---|---|---|
| 57 | Pino nevado (3 variantes) | P0 |
| 58 | Abeto muerto | P1 |
| 59 | Arbusto seco nevado | P1 |
| 60 | Tronco caído | P1 |
| 61 | Roca nevada (3 variantes) | P0 |
| 62 | Tocón | P2 |

**Del equipo de diseño** (generarlos ahora en Tripo como provisionales): los modelos del vendedor y el auto del tráiler quedan fuera, igual que el tráiler. Krampus base y el jugador ya existen.

Orden sugerido para Tripo:
1. P0 de criaturas;
2. herramientas;
3. cabaña y mejoras;
4. vegetación;
5. estructuras;
6. P1 y P2.

## 4. Reparto por agentes y ahorro de tokens
- **Briefs cortos:** cada agente lee este plan y `Docs/CONTRATOS.md`; el encargo solo dice el bloque, la propiedad de archivos y el criterio. No se repite contexto.
- **Modelos según dificultad:**
  - **Opus:** B1 (movimiento y cuerpo), B7 (Krampus), B8 (relieve y cielo) y el primer punto de interés como plantilla.
  - **Sonnet:** B2, B3, B4, B5 (los demás puntos de interés copian la plantilla), B6, B9 y B10.
  - **Haiku:** tablas de botín y de equilibrio, `textos.csv`, documentación e importar modelos de Tripo con el script `tools/blender/personajes/process_model.py` ya hecho.
- **Paralelismo:** como mucho 3–4 agentes por día, con propiedad de archivos que no se pise. Un solo agente integra al final del día, con todas las pruebas y un commit.
- **Pruebas:** una prueba headless por bloque. La partida completa y la prueba LAN se reutilizan como regresión.
- **Modelos de Tripo:** el usuario los genera por tandas. Un agente Haiku o Sonnet los decima e integra con el pipeline existente.

## 5. Calendario estimado (al ritmo de la semana 1: un "día" ≈ una sesión con 3–4 agentes)

| Día | Bloques | Resultado |
|---|---|---|
| 8 | B9 nieve, B10 menú, B1 velocidades e inercia + cámara PC, B2 rotar en tres ejes y troncos en piezas | Se siente mejor al momento |
| 9 | B1 cuerpo propio (PC y VR) e IK de brazos, B3 herramientas y kit, B4 mejoras con efecto real y chimenea por niveles | El día tiene herramientas y objetivos |
| 10 | B8 relieve, vegetación y cielo (nubes, estrellas, aurora), B5 sistema de cofres, botín y primer punto de interés plantilla | El mapa deja de estar vacío |
| 11 | B5 resto de puntos de interés, NPC, minijefes y fragmentos; leche y galletas en el botín | Hay razones para explorar |
| 12 | B6 noches con presión, B7 patrones de Krampus, forma final y final secreto | Clímax completo |
| 13 | Integración de modelos de Tripo (tandas), juice de golpes, audio que falta | Se ve y suena a juego |
| 14 | B11 equilibrio, prueba con visor y 2–4 jugadores, OVR Metrics, bugs, PR | Versión de torneo |

Total: unos 7 días de trabajo como la semana 1, con riesgo en B5 y B7. Los modelos dependen del ritmo de Tripo.

## 6. Después de esto (semana 3, si la hay)
- Tráiler (vendedor y auto).
- Animaciones de verdad: Mixamo o Auto Rig de Tripo con clips.
- Piernas en VR.
- Más puntos de interés y eventos.
- Modo dificultad.
- Guardado de opciones en la nube local.
- Accesibilidad: subtítulos de audio y daltonismo.
- Optimización fina en el Quest con los modelos finales.
- Juego por internet (noray), que hoy está descartado.
