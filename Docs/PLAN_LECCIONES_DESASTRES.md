# Plan: lecciones sobre desastres naturales (modo educativo)

Fecha: 2026-11-08. Estado: **D1 a D7 implementadas (2026-11-09), pendientes de revisión**. Se construye sobre la base de las lecciones (`scripts/edu/leccion_base.gd`) y sigue el patrón de L2 a L7: objetivo, estaciones con E, juez, pista de inicio y resumen.

> **PENDIENTE DE REVISIÓN por el usuario:** todo el texto de D1 a D7 (claves `D1_*` a `D7_*` en `scripts/edu/edu_i18n.gd`, en español e inglés) y los comentarios de cabecera de cada lección. Hay que revisarlo con Protección Civil antes de la exhibición. No contiene cifras oficiales: solo recomendaciones generales. El náhuatl no se ha escrito para estas lecciones.

## 0. Estado de implementación (D1 a D7)

| Lección | Escena | Script | Juez (simplificado) |
|---|---|---|---|
| D1 Terremoto | `scenes/edu/leccion_d1.tscn` | `scripts/edu/leccion_d1_terremoto.gd` | Durante el temblor: bajo la mesa y agarrarse. Si no, el aviso se repite. |
| D2 Alerta y plan familiar | `leccion_d2.tscn` | `leccion_d2_plan.gd` | Escuchar a un adulto, punto de encuentro, contar, en ese orden. |
| D3 Tsunami | `leccion_d3.tscn` | `leccion_d3_tsunami.gd` | Llegar a la zona alta con la alerta activa. Si la ola llega a la orilla, se repite. |
| D4 Huracán | `leccion_d4.tscn` | `leccion_d4_huracan.gd` | Cubrir dos ventanas, guardar agua, comida y linterna en el kit, escuchar la radio. |
| D5 Inundación | `leccion_d5.tscn` | `leccion_d5_inundacion.gd` | Lugar alto y caja de luz cortada, en cualquier orden. Entrar en el agua no cuenta. |
| D6 Monóxido de carbono | `leccion_d6.tscn` | `leccion_d6_monoxido.gd` | Abrir una ventana y encender la chimenea. Sin ventilar, solo aviso. |
| D7 Incendio forestal | `leccion_d7.tscn` | `leccion_d7_incendio.gd` | Coger el kit, pasar por la ruta marcada y llegar al punto seguro. La ruta del fuego avisa. |

Base común: `scripts/edu/desastre_base.gd` (zonas de evento y volúmenes visuales sin colisión). Entrada en el mapa: `DESASTRES` en `scripts/edu/mapa_lecciones.gd`. Pruebas: `scripts/test/test_edu_desastres.gd`.

Simplificaciones respecto al diseño de §3:
- D1: "agacharse" (Ctrl) no se detecta; la estación "bajo la mesa" cuenta como agacharse y cubrirse. Agarrarse es una pulsación de E, no mantener 5 s.
- D4: las ventanas son estaciones de cubrir, no el tablón del juego principal. El kit usa agua, comida y linterna (objetos del juego); no hay botiquín ni documentos. La radio es una estación.
- D5: el "piso alto" es un lugar elevado de la zona, no un segundo piso.
- D7: no hay control de "no volver por cosas"; la lección solo juzga la ruta y el kit.
- Nombres: las escenas siguen el patrón `leccion_dN.tscn` del modo educativo, no `leccion_8` como en §4.

## 1. Principios

1. **Mensajes oficiales primero.** Cada lección remite a alertas de las autoridades. El juego enseña comportamientos, no pronósticos.
2. **Sin susto gratuito.** El temblor y el viento se muestran suaves. No hay muertes ni sangre. El fallo se muestra como consecuencia y se puede repetir.
3. **Un hábito por lección**, con 2 a 4 minutos de juego.
4. **Validación externa antes de publicarlo.** Protección Civil (Baja California Sur y México), CENAPRED y el Servicio Meteorológico Nacional revisan el contenido. Las guías de referencia son de organismos como FEMA, CDC, la Cruz Roja y UNDRR; se citan en los créditos y en la guía docente.
5. **Lenguaje para todos los públicos** (infantil y general), en español, inglés y náhuatl.

## 2. Qué desastres y por qué

Prioridad por relevancia en La Paz, Baja California Sur (costa del Pacífico, huracanes y sismos):

| Prioridad | Lección | Por qué |
|---|---|---|
| 1 | Terremoto: agacharse, cubrirse y agarrarse | El riesgo más común en México; hábito único y verificable |
| 2 | Huracán y tormenta: preparar la casa y el kit | Relevante en BCS; reutiliza el inventario (L7) |
| 3 | Tsunami: subir a tiempo | Costa del Pacífico; hábito de "no ir a la playa a mirar" |
| 4 | Inundación: no cruzar agua | Lluvias intensas; hábito de "subir y no entrar al agua" |
| 5 | Monóxido de carbono: calefacción segura | Conecta con la lección de la chimenea (L1) |
| 6 | Incendio forestal: ruta de salida | Relevante en temporada seca |
| 7 | Plan familiar y punto de encuentro | Conecta con L2 (adulto de confianza) |
| Extra | Rayos ("si truena, entra"), ceniza volcánica | Opcionales si hay tiempo |

## 3. Diseño de cada lección

Formato común: estaciones interactivas, un juez con la regla, pista de inicio, resumen final.

### D1 · Terremoto: agacharse, cubrirse y agarrarse
- **Escenario:** la cabaña (L2–L4 ya la usan).
- **Evento:** un temblor suave de 10 segundos. Un aviso sonoro previo (alerta) da 5 segundos.
- **Regla:** al temblor, el jugador debe **agacharse** (Ctrl o tecla de agacharse), **meterse bajo la mesa** (estación) y **agarrarse** (mantener E 5 s).
- **Errores enseñados:** correr a la puerta o al exterior durante el temblor (aviso y repetición); quedarse de pie junto a una ventana (aviso).
- **Juez:** completar si se cubre y agarra durante el temblor. Sin castigo.
- **Ojo:** el temblor es solo visual y sonoro, nunca mueve al jugador.

### D2 · Alerta y plan familiar
- **Escenario:** la cabaña.
- **Mecánica:** una alerta suena. El jugador elige la acción en 3 pasos: escuchar a un adulto, ir al punto de encuentro (estación en el exterior), contar a quién se ve.
- **Juez:** completar si se sigue el orden. Conecta con L2.

### D3 · Tsunami: subir a tiempo
- **Escenario:** exterior con costa y zona alta (muelle y colina; mapa nuevo o reutilizar `map.tscn` de forma acotada).
- **Evento:** un temblor fuerte o el mar se retira.
- **Regla:** ir a la zona alta antes de que el agua llegue. No ir a la orilla a mirar.
- **Errores enseñados:** ir a la playa a ver las olas; llevarse cosas en lugar de subir.
- **Juez:** llegar a la zona alta dentro del tiempo. La ola es una barrera visual que avanza; sin daño.

### D4 · Huracán: preparar la casa y el kit
- **Escenario:** la cabaña con ventanas (las ventanas del juego `window.tscn`).
- **Tareas:** cerrar y cubrir ventanas (tablón, como en el juego), guardar en el kit agua, comida, linterna, botiquín, documentos y radio (reutiliza L7), escuchar la alerta oficial.
- **Juez:** completar las tres tareas: ventanas, kit, escucha de la alerta.
- **Ojo:** no salir durante el huracán; la lección termina dentro.

### D5 · Inundación: no cruzar agua
- **Escenario:** calle con agua que sube.
- **Regla:** subir al piso alto, no caminar ni conducir por agua corriente, cortar la luz si hay agua en el piso (estación en la caja de luz).
- **Errores enseñados:** cruzar el agua corriente.
- **Juez:** completar si se sube y se corta la luz.

### D6 · Monóxido de carbono: calefacción segura
- **Escenario:** la cabaña y la chimenea (`FireplaceEdu`).
- **Regla:** la chimenea y el brasero se usan con ventilación. Nunca quemar carbón dentro sin ventilar. Abrir una ventana.
- **Juez:** completar si se abre una ventana y se mantiene la chimenea sin brasero dentro.
- **Ojo:** el jugador no se intoxica en la lección; el aviso es un mensaje de CO y la ventana es la solución.

### D7 · Incendio forestal: ruta de salida
- **Escenario:** exterior con bosque y dos rutas.
- **Regla:** salir por la ruta marcada, con el kit de emergencia, sin volver por cosas.
- **Juez:** llegar al punto seguro por la ruta correcta.

### D8 · Plan familiar (opcional)
Integrado en D2 si el tiempo es corto.

## 4. Estructura técnica

- Cada lección: `scripts/edu/leccion_8_terremoto.gd` … en el mismo patrón que L2–L7, con `scenes/edu/leccion_8.tscn`.
- Eventos (temblor, alerta, ola): un nodo `EventoDesastre` con señales. Sin lógica en la base.
- Textos en `EduI18n` con claves `D1_*`, etc. Náhuatl solo tras revisión.
- Pruebas: juez por lección en `test_edu_lecciones` (misma forma que L2–L7).
- Menú: la lista agrupa "Hábitos" (L1–L7) y "Desastres" (D1–D7).

## 5. Calendario sugerido

| Fase | Trabajo | Días |
|---|---|---|
| Hoy | Aprobar la lista y el orden | — |
| 1 | D1 terremoto y D2 alerta, con evento compartido | 2 |
| 2 | D4 huracán (reutiliza L7) | 1 |
| 3 | D3 tsunami (escenario exterior nuevo) | 2 |
| 4 | D5 y D6 | 1,5 |
| 5 | D7 incendio y revisión de todo con Protección Civil | 1,5 |

Para la exhibición del 18 de noviembre bastan D1 y D4: son los de mayor relevancia local y los más sencillos de contar. Los demás pueden presentarse como hoja de ruta.

## 6. Riesgos

| Riesgo | Mitigación |
|---|---|
| Información incorrecta sobre seguridad | Revisión de Protección Civil y de CENAPRED antes de publicar |
| Miedo o trauma en niños | Eventos suaves, sin víctimas, con mensajes de calma |
| Simplificar de más una emergencia real | Cada lección dice que las autoridades mandan; se enseña el hábito, no todo el protocolo |
| Tiempo insuficiente | D1 y D4 primero; el resto como hoja de ruta |

## 7. Decisiones que necesito

1. ¿Apruebas el orden y D1 + D4 como mínimo para el 18?
2. ¿Contacto en Protección Civil de La Paz para la revisión de contenido?
3. ¿Quieres que D3 use un escenario de costa nuevo o que reutilice el mapa actual?
