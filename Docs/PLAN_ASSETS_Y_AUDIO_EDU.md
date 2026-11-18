# Plan: assets, escenarios y audio para el modo educativo

Fecha: 2026-11-08. Estado: **plan, sin implementar**.
Objetivo: que el modo educativo se vea y suene como un producto terminado (no como prototipo), con contexto de La Paz (BCS), sin depender de nada que no tengamos permiso para usar.

## 1. Principios

1. **Reutilizar antes que crear.** Primero lo que ya está en el proyecto, después lo descargado, y solo al final lo generado.
2. **Licencia registrada en cada archivo.** Todo entra en `assets/externos/CREDITOS.md` (fuente, URL, autor, licencia, uso). Si una licencia no permite uso comercial o sin atribución, no entra.
3. **Calidad por escena, no por pieza.** Un objeto mediocre se nota menos si la luz, el sonido y el encuadre son buenos. Cada escena se evalúa por capturas.
4. **Presupuesto para Android.** Las lecciones deben correr en tabletas y en Quest. Texturas ≤1K, objetos ≤8k triángulos, pocas luces dinámicas.
5. **Nada de nieve.** Ninguna textura, sonido ni modelo de nieve entra al modo educativo.

## 2. Inventario de lo que ya existe

| Categoría | Qué hay | Uso en el modo educativo |
|---|---|---|
| Modelos propios del juego | `assets/characters/` (vaca, cerdo, gallina, ermitaño, viajero, gnomo…), `assets/world/props/` (antorcha, cubo, cuchillo, carne…) | Animales de la lección 3, objetos de la 7, herramientas de la 5 |
| Modelos externos ya descargados | `assets/externos/modelos/`: `kenney_food`, `kenney_furniture`, `kenney_holiday`, `kenney_nature`, `kenney_survival`, `kenney_fantasy_town` | Comida, muebles, cofres, árboles, rocas, vallas |
| Cabaña y escenario | `scenes/world/cabin/cabin.tscn`, `cabin_edu.tscn`, `chimenea`, `puerta`, `ventanas`, mesa de trabajo | Base de las lecciones interiores |
| Texturas | `assets/textures/cabana/` (madera, piedra, techo), `assets/textures/cuadros/` | Interior de la casa (hay que cambiar a block/estuco para La Paz) |
| Audio | 5 ambientes, 16 objetos, 11 enemigos, 10 SFX, 6 UI, 3 mecánicas, 2 música (`assets/audio/`) | Puerta, fuego, pasos, objetos, UI. Música: casi nada |
| Tripo | Registro en `Docs/TRIPO_REGISTRO.md`: vaca, hacha, pino nevado (importados, sin escena). ~1700 créditos | Modelos únicos que no existan en bibliotecas |
| Herramientas | Blender (ya usado para el terreno), scripts de generación en `tools/blender/`, MCP de Blender disponible | Modelos procedurales, ajustes, exportación |

Lo que **no** existe y hace falta: arena, sol y cielo cálido, plantas de zona seca (cardón, palo verde, mezquite, pitaya), casa de block o estuco, música, sonidos de calor y naturaleza de La Paz.

## 3. Matriz de decisión por necesidad

Para cada elemento se elige el primer método que sirva:

| Orden | Método | Cuándo |
|---|---|---|
| A | **Reutilizar** del proyecto | El elemento existe y encaja (animales, comida, muebles, cofres, puerta) |
| B | **Descargar** CC0 | Existe en biblioteca con licencia clara (arena, plantas genéricas, sonidos) |
| C | **Procedural en Blender** | Forma simple y repetible (cardón, palo verde, mezquite, casa de block, ola) |
| D | **Tripo** (créditos del proyecto) | Objeto único sin equivalente libre (ej. casa de La Paz con detalles locales, personaje de la lección) |
| E | **Modelado a mano** | Solo si los anteriores no dan calidad suficiente |

### 3.1 Modelos y escenarios por lección

| Lección | Necesidad | Método | Fuente / nota |
|---|---|---|---|
| Todas | Suelo de arena y tierra, piedras | B | ambientCG (CC0): arena, tierra seca |
| Todas | Cielo cálido, sol | B/A | Poly Haven (CC0) HDRI de cielo diurno; o Environment de Godot |
| L1 fuego | Fogón de leña | C+A | Piedra del juego (`stone`) + leña del juego; fogón procedural |
| L2 puerta | Puerta con reja, ventana | A+B | Puerta del juego; reja de Kenney |
| L3 animales | Vaca, cerdo, gallina (propios); burro o cabra | A + D o B | Burro/cabra: buscar en Quaternius (CC0) |
| L4 sol | Sombrero, cantimplora, sombra | B+A | Kenney Survival (cantimplora ya en `kenney_survival`); sombrero: Quaternius o procedural |
| L5 bosque | Mezquite, palo verde, cardón, pitaya | C | Procedurales en Blender (ramas, espinas, color). Verificar con Kenney Nature |
| L6 golpe de calor | Muñeco tumbado, sombra (toldo) | A+C | Muñeco: reutilizar `muneco_nieve` sin nieve, o modelo simple; toldo procedural |
| L7 mochila | Cofres, comida local (fruta, carne, agua) | A | Kenney food/survival; cofres de `kenney_fantasy_town` |
| D1 terremoto | Mesa para refugiarse, objetos que caen | A | `kenney_furniture` (mesa) |
| D3 tsunami | Muelle, costa, olas | B+C | Muelle procedural; ola: shader o malla animada |
| D4 huracán | Tablones, ventanas, palmeras | A+C | Tablones del juego; palmera procedural |
| D5 inundación | Calle y agua | C | Calle con textura CC0; agua con shader (el juego ya tiene agua) |
| D7 incendio | Bosque seco, humo | B+C | Árboles procedurales; humo con partículas |

### 3.2 Casa de La Paz

Es la pieza más visible. Dos caminos:
- **Caminos B+C:** partir de la cabaña, cambiar paredes a estuco blanco o block, techo plano, reja en la ventana, persianas. Texturas CC0 de ambientCG (estuco, concreto).
- **Camino D (Tripo):** generar una casa de block con palapa como referencia única, y limpiarla en Blender. Gasta créditos; hacerlo solo si el camino B+C se ve pobre.

Recomendación: camino B+C primero, evaluar por captura. Tripo solo si hace falta.

## 4. Audio

### 4.1 Lo que hay y lo que falta

| Categoría | Existe | Falta |
|---|---|---|
| Fuego | Crepitar, leña que se echa, siseo al apagarse | Fogón (diferente de chimenea) |
| Puerta | Abrir, cerrar, golpe | Llamar a la puerta (golpes), timbre |
| Pasos | Madera, nieve (**quitar nieve**) | Pasos sobre arena y piedra |
| Ambiente | Viento, tormenta, pasos | Cigarras y grillos, mar, viento seco, calle con tráfico lejano |
| Objetos | 16 sonidos | Agua vertida, fruta mordida, cuchillo |
| UI | 6 sonidos | Confirmar y error más claros para niños |
| Alertas | Ninguna | Sirena suave, alerta sísmica (tono de advertencia) |
| Música | 2 pistas | Menú, lección tranquila, lección con tensión suave, cierre festivo |

### 4.2 Fuentes

| Tipo | Fuente | Licencia | Notas |
|---|---|---|---|
| Efectos | Kenney (impact, rpg-audio, interface) | CC0 | Ya usados |
| Efectos | Freesound | Revisar **por archivo** (CC0 o CC-BY con crédito) | Buscar "fire", "sand", "cicada", "waves", "knock" |
| Efectos | OpenGameArt | Revisar por archivo | Buscar ambientes |
| Música | Free Music Archive / Pixabay Music | Revisar por pista (CC0 o licencia de uso libre) | Pixabay permite uso libre; confirmar términos |
| Música | Generada con una herramienta de música con tu cuenta | Según los términos de esa herramienta | Requiere que tú la generes: no tengo acceso desde aquí |

### 4.3 Música: qué necesitamos

Cuatro pistas de bucle, en instrumentos suaves (guitarra, marimba, vientos, percusión ligera), en tonos cálidos:
1. **Menú:** tranquila, alegre sin ser infantil.
2. **Lección de hábitos:** ritmo lento, para pensar.
3. **Alerta o desastre (D1 a D7):** tensión suave, sin golpes fuertes. Debe seguir siendo apta para niños.
4. **Cierre "Nochebuena":** festiva, corta (20 a 30 s).

Especificación: ogg, 44.1 kHz, bucle sin corte, volumen normalizado a -16 LUFS, con crédito en `CREDITOS.md`.

### 4.4 Técnica en Godot

- Buses ya existentes (`SFX`, `Musica`); una capa de ambiente nueva para el exterior.
- Mezcla: música a -18 dB respecto a voces y avisos; voces y avisos siempre audibles.
- Un único reproductor de música con cruce de 2 segundos al cambiar de lección.
- Sonidos de avisos (alerta) con subtítulos y señal visual (accesibilidad).

## 5. Generar contenido: qué se puede y qué no

| Tipo | Puedo hacerlo aquí | Necesita a alguien o una herramienta externa |
|---|---|---|
| Modelos procedurales | Sí, con scripts de Blender (MCP y consola) | Blender abierto para el MCP |
| Modelos de Tripo | Sí, con tu cuenta y créditos | Tu acceso a Tripo |
| Descargas CC0 | Sí, con tu permiso (cada archivo a `assets/externos/`) | Tu permiso |
| Efectos de sonido | Buscar y registrar, con tu permiso | Tu permiso para descargar |
| Efectos sintetizados (viento, sonido de calor, tonos de alerta) | Sí, generando audio en Godot o en Blender | Pruebas auditivas tuyas |
| Música | No compongo audio de calidad aquí | Generador de música con tu cuenta, o un músico |

Importante: la música es lo que más calidad da y lo que menos puedo entregar yo. Hay que decidirlo pronto.

## 6. Calidad: criterios de aceptación

Cada escena se aprueba cuando cumple:
- **Luz:** sombras que definen la forma, sol con tono cálido, sin zonas de color plano.
- **Materiales:** ninguna textura se repite a la vista; ninguna pieza tiene el color por defecto.
- **Escala:** un jugador de 1,7 m ve objetos con la escala correcta (puerta de 2 m, cardón de 2 a 4 m).
- **Sonido:** cada acción tiene su sonido; hay ambiente en todo el exterior.
- **Legibilidad:** texto de al menos 18 px en tableta, contraste suficiente, subtítulos de todo aviso sonoro.
- **Rendimiento:** 60 FPS en PC de referencia; 30 FPS mínimo en Android de gama media (medir).

Se revisa con capturas de cada escena antes de darla por buena.

## 7. Producción por fases

| Fase | Entregable | Duración | Antes del |
|---|---|---|---|
| 0 | Confirmar permisos y herramientas (sección 8) | 0,5 día | — |
| 1 | Textos nuevos, suelo de arena, cielo cálido, luz sin nieve | 1 día | Video (11 nov) |
| 2 | Casa de La Paz (estuco, reja, techo plano) | 1,5 días | Video |
| 3 | Plantas locales procedurales (cardón, palo verde, mezquite) | 1 día | 18 nov |
| 4 | Audio: efectos de sitio (cigarras, fogón, puerta, golpes) | 1 día | 18 nov |
| 5 | Música: 4 pistas (si la generas o consigues) | 1 día | 18 nov |
| 6 | Prueba de calidad con capturas y audición; corrección | 1 día | 18 nov |

Para el video: fases 1 y 2, con el audio de la puerta y el fuego que ya existen. Lo demás se completa para la exhibición.

## 8. Decisiones y permisos que necesito

1. **Permiso para descargar** assets CC0 de ambientCG, Poly Haven, Quaternius, Kenney y Freesound/OpenGameArt (cada archivo se revisa y se registra).
2. **Blender abierto** para el MCP, si quieres que genere las plantas y la casa desde aquí.
3. **Créditos de Tripo:** ¿los usamos para la casa de La Paz o para un personaje único? Recomiendo solo si el camino B+C no da calidad.
4. **Música:** ¿quién la genera o la consigue? Opciones: tú con una herramienta de música (y me pasas los archivos con su licencia), o un músico que la produzca. Sin música, las lecciones se ven bien pero el producto pierde mucho.
5. **Estilo visual final:** ¿mantenemos la paleta cálida y de dibujo de la interfaz actual, o queremos algo más realista?
6. **Revisión** de la casa y las plantas por alguien de La Paz, para que la escena sea reconocible.

## 9. Riesgos

| Riesgo | Mitigación |
|---|---|
| Licencias poco claras en Freesound u OpenGameArt | Solo archivos CC0 o con crédito claro; registrar cada uno |
| Música sin resolver a tiempo | Usar una pista CC0 de prueba y reemplazar; las lecciones no dependen de la música para funcionar |
| Escenas que se ven "de prototipo" | Revisión por capturas con la lista de la sección 6 |
| Android lento | Texturas 1K, LOD simple, menos luces; medir en un dispositivo real |
| Tripo gasta créditos sin necesidad | Solo para piezas que no tengan equivalente libre, con aprobación previa |

## 10. Estado de ejecución (2026-11-08)

Decisiones del usuario: descargas CC0 permitidas; Blender abierto; Tripo "según haga falta"; música generada por el usuario con Gemini; estilo realista; el usuario revisa plantas y casas; **evitar generar modelos y dejar sin modelo lo que no esté resuelto**.

Hecho:
- Texturas CC0 de Poly Haven en `assets/externos/texturas/polyhaven/`: arena (`coast_sand_05`), tierra (`dirt`), estuco (`clay_plaster`), en 1K con difuso, normal OpenGL, rugosidad y oclusión. Registradas en `CREDITOS.md`. Importadas en Godot.

Pendiente:
- **Música:** no se descarga música de otros juegos (tiene copyright aunque el producto no sea comercial). Se usa música CC0 o con licencia de uso libre, o la que genere el usuario con Gemini (con sus términos). Hace falta la lista de pistas para cada escena.
- **Sonidos de sitio** (cigarras, mar, viento seco, fogón): buscar en fuentes con licencia por archivo; Freesound requiere cuenta para la API.
- **Modelos:** se prioriza reutilizar y descargar CC0. Lo que no tenga modelo se deja fuera de la escena (sin placeholder generado). Tripo y Blender solo con aprobación previa de cada pieza.
- **Escenario:** suelo de arena y cielo cálido en el modo educativo (sección 3 de `PLAN_CONTEXTO_MEXICO.md`).
