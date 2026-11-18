# Plan: modo educativo e inclusivo (prototipo para competencia)

Estado: **plan, sin implementar**. No se toca el juego principal: el modo vive aparte.
Fecha: 2026-11-08.

## 0. Datos del concurso (bases del 14.º Concurso de Prototipos, DASC-UABCS)

Fuente: convocatoria del 20 de octubre de 2026 (archivo local en Descargas).

| Fecha | Qué |
|---|---|
| 6 nov 2026 | **Fecha límite de inscripción** (ya pasó; confirmar que el equipo está registrado en https://uabcs.online/) |
| **11 nov 2026** | **Video del proyecto, máximo 5 min**, entregado al DASC |
| 18 nov 2026, 8:00–14:00 | Etapa 1: exhibición a estudiantes, profesores y **niños de primaria y secundaria** |
| 18 nov 2026, 16:30–19:00 | Etapa 2 (final): demostración funcional ante el jurado, que decide el tiempo y hace preguntas |

Reglas relevantes:
- Equipo de hasta 4 personas.
- Categorías: Nivel Intermedio y Nivel Avanzado (software y hardware). Elegibilidad por carrera y semestre; **confirmar la categoría**.
- **Premio especial y mención al mejor proyecto inclusivo.** Este modo apunta a esa mención.
- Criterios de evaluación: claridad de exposición, rigor científico y tecnológico, innovación y creatividad, aplicación, práctica y **responsabilidad social**.
- Lona de 1 m × 0.35 m detrás de la mesa con el nombre del proyecto y el diseño oficial del evento (lo descarga el equipo del sitio del DASC).
- Se sugiere que el proyecto esté orientado a **tecnología inclusiva y de aprendizaje (objetos de aprendizaje)**.

Implicaciones para el plan:
- La exhibición del 18 de noviembre es **para niños**: el modo educativo debe ser jugable por ellos sin explicación previa, con textos cortos y una lección de 2–4 minutos.
- El video de 5 minutos (11 de noviembre) es lo primero que mira el jurado: debe mostrar el modo educativo, no el juego de terror.
- El jurado valora "rigor científico": cada lección debe tener un objetivo explícito y una fuente (guía docente y referencias de los contenidos).

## 0.1 Decisiones ya tomadas por el equipo

- **Edad objetivo:** para todo público (no solo 8–12). Ajustar lecciones y lenguaje a niveles distintos.
- **Plataformas:** PC, VR y Android (Quest y móviles/tabletas Android). Esto cambia el alcance: VR y Android pasan de "extra" a obligatorios (ver §4 y §5).
- **Idiomas:** español, inglés y **náhuatl** (lengua indígena; las bases dan puntos extra por ello; ver §0.2).
- **Pruebas:** el equipo tiene niños y docentes que pueden probar el prototipo.

## 0.2 Náhuatl

- Nombre correcto en español: **náhuatl** (lengua nahua). Código de idioma: `nah`.
- Hay varias variantes (de la Huasteca, de Puebla, del centro de México, de la sierra de Guerrero, entre otras). Hay que **elegir una variante** para el prototipo y decirla en la presentación.
- La traducción debe **revisarla una persona hablante** de esa variante antes de la exhibición, con crédito en los créditos del juego. Una traducción automática no es suficiente para el jurado ni para los niños.
- Técnicamente, Godot admite el idioma como un `.csv` de traducciones más, en el modo educativo (`resources/i18n/edu.csv`, ver §5).
- Empezar con las frases de las lecciones y el menú; el resto del juego queda fuera del alcance del modo.

## 0.3 Borrador de vocabulario en náhuatl (a verificar)

Preparado por el equipo de desarrollo como punto de partida, **no como traducción final**. Está en variante clásica/central con ortografía moderna y hay que revisarlo con una persona hablante antes de usarlo con niños.

| Español | Náhuatl (borrador) | Confianza |
|---|---|---|
| hola | niltze | media |
| gracias | tlazohcamati | alta |
| está bien / bien | ca cualli | media |
| no | amo | alta |
| agua | atl | alta |
| fuego | tletl | alta |
| árbol / madera | cuahuitl | alta |
| piedra | tetl | alta |
| casa | calli | alta |
| luna | metztli | alta |

Notas para la revisión:
- La ortografía varía entre variantes (`cuahuitl`, `kuawitl`, `tlazohcamati`, `tlazocamati`).
- Las frases de seguridad de la lección de la puerta ("avisa a un adulto") **no** se traducen hasta tener revisión: un error ahí es peor que no traducirla.
- Las variantes de la Huasteca, de Puebla o de la sierra de Guerrero no son intercambiables con esta lista. Fijar una variante y escribirla en los créditos.

## 1. Idea central

El juego ya tiene mecánicas con valor pedagógico real: cuidar una casa, mantener el fuego, proteger la puerta, alimentar animales, abrigarse, planificar un inventario limitado y ayudar a un compañero caído. El modo educativo convierte esas mecánicas en **hábitos de seguridad, cuidado y cooperación**, sin violencia ni sustos fuertes.

**Nombre de trabajo:** *Nochebuena en la cabaña* (modo "Refugio").
**Frase para el jurado:** "Aprender a cuidar un hogar, a ayudarse y a planificar, jugando en una cabaña nevada."

Lo que cambia respecto al juego principal:

| Juego principal | Modo educativo |
|---|---|
| Krampus, renos zombis, gnomos explosivos | Sin enemigos. Retos y "visitas" que se resuelven con reglas de seguridad |
| Reglas como amenaza (romperlas = caos) | Reglas como **hábitos** (romperlas = consecuencias suaves y reparables) |
| Fallar = muerte y derribo | Fallar = pista y reintento. Nunca se pierde la partida |
| Partida de 15 min cronometrada | Ritmo libre, pausable, sin presión de tiempo |
| VR y PC, LAN de 4 jugadores | **PC, VR y Android** (un jugador). La LAN queda fuera del modo |

## 2. Público y objetivos de aprendizaje

**Público:** para todo público. Las lecciones tienen dos niveles de lenguaje: infantil (primaria) y general (secundaria, familias, docentes). La exhibición del 18 de noviembre incluye niños de primaria y secundaria.
**Público secundario:** docentes y familias que lo usen en el aula o en casa.

Objetivos medibles (cada uno con una lección y un criterio observable):

1. **Seguridad con el fuego:** encender, mantener y apagar con control; no dejar la chimenea sin vigilancia.
2. **Seguridad personal:** no abrir la puerta a alguien que no se conoce; avisar a un adulto de confianza. (Tratado con cuidado y sin miedo: ver §6.)
3. **Cuidado de animales:** alimentar y dar agua a horas adecuadas; revisar el corral.
4. **Abrigo y clima:** vestirse según la temperatura y limitar el tiempo fuera con frío.
5. **Uso sostenible:** cortar leña de forma responsable (no talar todo el bosque), replantar y reciclar.
6. **Primeros auxilios básicos:** detectar a un compañero caído, pedir ayuda y reanimarlo siguiendo pasos fijos.
7. **Planificación y matemáticas:** contar, apilar y repartir recursos en un inventario de 25 casillas.
8. **Cooperación y comunicación:** repartir tareas y decir en voz alta lo que se necesita (modo 2 jugadores, si hay tiempo).

Marco de referencia: los objetivos se redactan para encajar en currículos de primaria de ciencias naturales, educación para la salud, educación en valores y matemáticas. Revisar con un docente antes de la entrega.

## 3. Estructura de lecciones (vertical slice primero)

Cada lección: objetivo claro al inicio, 2–4 minutos de juego, una comprobación final (lista de verificación, no examen con nota), y pista si se falla.

| # | Lección | Mecánica reutilizada | Criterio de logro |
|---|---|---|---|
| L1 | El fuego de la cabaña | Chimenea, leña, temperatura | Mantener la chimenea encendida 2 min y apagarla bien al salir |
| L2 | ¿Quién llama a la puerta? | Puerta, interacción | Identificar visitantes conocidos vs. desconocidos y avisar a un adulto |
| L3 | Los animales necesitan cuidado | Granja, comedero, agua | Alimentar a todos los animales antes del anochecer |
| L4 | Ropa para el frío | Temperatura, chamarra | Equiparse antes de salir con frío y volver a tiempo |
| L5 | Bosque para todos | Árbol, tocón, recurso | Cortar solo lo necesario y dejar un brote que crece |
| L6 | Un amigo caído | Derribo y reanimación | Pedir ayuda y reanimar con los 3 pasos (revisar, llamar, ayudar) |
| L7 | La mochila llena | Inventario de 25 casillas | Repartir 60 objetos en categorías sin pasar del límite |
| Final | La Nochebuena | Todo lo anterior | Informe final de lo aprendido y celebración cooperativa |

**Vertical slice (para la primera demo):** L1 + L2 + menú de módulos + informe final. Es lo mínimo que demuestra la idea completa.

## 4. Inclusión (requisito, no adorno)

Checklist que el jurado puede verificar en la demo:

- **Subtítulos** con nombre del hablante y tamaño ajustable.
- **Señales visuales** equivalentes a cada sonido importante (fuego, puerta, aviso de frío).
- **Paleta apta para daltonismo** (sin depender del rojo/verde para informar).
- **Texto legible:** tipografía con opción de alta legibilidad, tamaño ajustable, frases cortas y lenguaje de nivel de lectura de primaria.
- **Sin prisa:** pausa siempre disponible, sin cronómetro en las lecciones.
- **Reducción de movimiento:** sin balanceo de cámara, sin sacudidas, sin niebla intensa; opción de cámara fija.
- **Controles flexibles:** teclado, ratón, mando y una opción de un solo dedo o una sola mano. Aprovechar el remapeo y los controles táctiles ya creados en el día 13.
- **Idiomas:** español e inglés desde el inicio (el proyecto ya tiene i18n); catalán o gallego como extensión.
- **Representación diversa:** personajes de distintas edades, familias y culturas; nada de estereotipos de género en los roles.
- **Confort en VR y Android:** en VR, modo sentado y giro por pasos activados por defecto; sin movimiento libre obligatorio; las lecciones se pueden completar sin girar el cuerpo. En Android (táctil), botones grandes y sin atajos de teclado obligatorios.
- **Idiomas:** español, inglés y náhuatl, con el mismo sistema de claves `tr()`.
- **Sin contenido que asuste:** sin gore, sin persecuciones, sin voces de terror.

## 5. Arquitectura técnica (sin tocar el juego principal)

Principio: **el modo se añade, no se modifica.**

- **Escena de entrada propia:** `scenes/edu/edu_main.tscn` con su propio script raíz en `scripts/edu/`. No se toca `main_menu.tscn` ni `game.tscn`.
- **Arranque separado:** dos opciones, en orden de preferencia:
  1. Un **export preset aparte** ("12/12/12 Educativo") con la escena principal `edu_main.tscn` como main scene del preset. Así el jurado abre un ejecutable distinto. Habría tres presets: Windows (PC y PCVR) y Android (Quest y tabletas/móviles).
  2. Para desarrollo: `godot --path D:/12-12-12 res://scenes/edu/edu_main.tscn`. No requiere cambios en `project.godot`.
  Verificar antes de comprometerse que el preset permite cambiar la escena principal en esta versión de Godot 4.7.
- **Sin autoloads nuevos.** Cambiar `project.godot` es un archivo caliente (ver `TAREAS.md`). Las configuraciones del modo se cargan desde su propio recurso.
- **Traducciones propias:** `resources/i18n/edu.csv`, registradas en tiempo de ejecución con `TranslationServer`, para no ensuciar `textos.csv`.
- **Reutilizar solo datos y recursos, no scripts con estado:** modelos, texturas, sonidos y escenas de decoración se pueden instanciar. Los scripts de gameplay (`game.gd`, `death_manager.gd`, `net_manager.gd`, `creature*`, `krampus*`) **no se usan**: están acoplados al juego principal. Si hace falta una lógica parecida (temperatura, inventario), se escribe una versión simple dentro de `scripts/edu/`.
- **Un solo jugador.** Quitar la red del modo reduce mucho el riesgo. Esto evita, además, los fallos de LAN que se ven en el día 14 (host que se va y partida trabada).
- **Guardado local:** progreso en `user://edu_progreso.json`. Sin cuentas, sin red, sin datos personales.
- **Exportar informe:** botón que genera un archivo `.txt` o `.json` con las lecciones completadas y el tiempo, para que el docente lo revise. Sin enviarlo a ningún servidor.
- **Pruebas headless:** `scripts/test/test_edu_*.gd`, en la misma línea que las pruebas actuales, y entrada en `tools/verificar.ps1` solo como prueba adicional.

Carpeta propuesta:

```
scenes/edu/       escenas del modo (menú, lecciones, informe)
scripts/edu/      lógica del modo (lecciones, progreso, accesibilidad)
resources/edu/    recursos: textos, lecciones, ajustes de accesibilidad
resources/i18n/edu.csv
Docs/EDU_*.md     guía docente, lista de inclusión, informe de pruebas
```

## 6. Tono y tratamiento sensible

- **Krampus:** no aparece en el modo educativo (decisión confirmada, §9). Es una figura de la tradición alpina asociada a castigar a los niños "malos", y su mensaje de castigo no encaja en un contexto educativo.
- **La lección de la puerta** es la más delicada. Debe enseñar a **avisar a un adulto de confianza y no abrir a desconocidos**, sin generar miedo ni culpar a nadie. Nunca se presenta como "hay un monstruo fuera". Revisarla con un psicólogo infantil o un docente antes de mostrarla.
- **Consecuencias suaves:** si el fuego se apaga, hace más frío y la lección se reintenta; si el animal no come, baja su energía y se puede volver a alimentar. Nunca un "game over".
- **Lenguaje positivo:** "¡Vuelve a intentarlo!", "Casi lo tienes", en vez de "Fallaste".

## 7. Material para el jurado

El jurado valora probablemente: innovación, valor educativo, calidad técnica, inclusión y presentación. Entregables:

1. **Demo jugable** de 5 minutos: L1 + L2 + informe final.
2. **Video de 3 minutos** con una lección completa, subtítulos y una explicación de cada objetivo.
3. **Guía docente de 2 páginas:** objetivos, cómo usar el modo en clase, criterios de logro.
4. **Hoja de inclusión** con el checklist de §4 marcado y las pruebas hechas.
5. **Evidencia de prueba con usuarios:** si es posible, 3–5 niños o docentes probando el prototipo, con el consentimiento de sus familias. Medir:
   - Test corto antes y después (5 preguntas sobre fuego, puerta, animales, abrigo y bosque).
   - Tiempo hasta completar la primera lección.
   - Una pregunta de usabilidad (escala 1–5: "¿Lo entendiste sin ayuda?").
   Si no hay tiempo de prueba real, decirlo con honestidad en la presentación; el jurado valora más la honestidad que una cifra inventada.
6. **Presentación de 1 página** que conecte el juego con un problema real: prevención de accidentes domésticos y educación para la seguridad.

## 8. Calendario propuesto

Plazos reales: **video el 11 de noviembre** y **exhibición y jurado el 18 de noviembre**. Quedan 10 días desde hoy (8 de noviembre), y el video sale antes que el prototipo final. Por eso el calendario se divide en dos entregas.

**Entrega 1: video (11 de noviembre).** Lo que se muestra debe existir ya: L1 jugable en PC, pantalla de inicio con las tres opciones de idioma y un fragmento de la guía docente. Si no hay VR ni Android funcionando para entonces, se muestra en el video como trabajo en curso y se dice así.

**Entrega 2: exhibición y jurado (18 de noviembre).** Prototipo con PC, VR y Android funcionando, lecciones L1 a L3 como mínimo, náhuatl en el menú y las frases de las lecciones.

| Fase | Contenido | Duración |
|---|---|---|
| 0 | Confirmar categoría y registro, variante de náhuatl, reglas de §9 | 0,5 día |
| 1 | Escena de entrada, menú de módulos, progreso local, lección L1 jugable | 2 días |
| 2 | Lección L2 (puerta) con revisión de contenido y menú de lecciones | 1,5 días |
| 3 | Accesibilidad: subtítulos, daltonismo, texto, reducción de movimiento, controles | 2 días |
| 4 | Lecciones L3–L7 y final, con el mismo patrón de lección | 3 días |
| 5 | Guía docente, hoja de inclusión, informe exportable, prueba con usuarios | 2 días |
| 6 | Video, presentación, pulido y ensayo de la demo | 1,5 días |

Si el tiempo aprieta, el orden de prioridad es: **L1 + L2 + accesibilidad + informe + guía docente**. El resto se puede presentar como "roadmap" con los prototipos ya hechos.

## 9. Decisiones

Confirmadas por el equipo (2026-11-08):
- **Edad:** para todo público.
- **Plataformas:** PC, VR y Android.
- **Idiomas:** español, inglés y náhuatl.
- **Krampus:** fuera del modo educativo. Sin enemigos ni figura de castigo.
- **Categoría:** Nivel Intermedio (primer semestre).
- **Inscripción:** confirmada.
- **Pruebas con niños:** permiso confirmado. Pendiente de organizar: avisos a las familias y, si la escuela lo pide, consentimiento del centro.
- **Cambios del mapa:** commit `b4c652b` (piezas del terreno).

Pendiente:
1. **Variante de náhuatl:** el borrador de §0.3 es de referencia, no de entrega. Hace falta una revisión por hablante antes de la exhibición.

## 10. Riesgos

| Riesgo | Mitigación |
|---|---|
| Alcance grande para el tiempo | Vertical slice primero; priorizar L1 + L2 + informe |
| Dependencia de VR o red | Modo de un jugador en PC; VR y LAN fuera del alcance mínimo |
| Contenido de la lección de la puerta mal recibido | Revisión con docente o psicólogo; lenguaje sin miedo |
| Licencias de assets | Solo CC0 y registro en `assets/externos/CREDITOS.md` (ya es la norma del proyecto) |
| Rendimiento en equipos de escuela (PC modestos) | Calidad "baja" por defecto en el modo; medir draw calls |
| Afirmar resultados de aprendizaje sin datos | Presentar solo lo medido; declarar el tamaño de la muestra |

## 11. Antes de empezar (pendiente del día 14 del juego principal)

Esto no forma parte del modo educativo, pero conviene cerrarlo antes de la competencia porque el juego principal también debe funcionar sin trabas en la presentación:

- **Host que sale:** al salirse el host, hoy el juego queda trabado para todos en lugar de devolverlos al lobby. Pendiente de corregir.
- **Reincorporación:** nadie puede volver a unirse después de salir o desconectarse. Pendiente de corregir.

Estos dos puntos se corrigen en una tarea aparte, sobre `net_manager.gd` y `game.gd`, sin mezclarlos con el modo educativo.
