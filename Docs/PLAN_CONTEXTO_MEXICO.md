# Plan: adaptar el modo educativo a México (La Paz, Baja California Sur)

Fecha: 2026-11-08. Estado: **plan, sin implementar**.
Motivo: el modo educativo está ambientado en una cabaña nevada y varias lecciones hablan de frío y nieve. En La Paz no nieva. Para el jurado y para los niños, el contexto debe ser el de su casa.

## 1. Qué hay hoy que no encaja

| Dónde | Qué | Problema |
|---|---|---|
| `scenes/edu/cabin_edu.tscn` | Tarjetas de luz de nieve (`LuzNieveEste/Oeste`, `mat_luz_nieve`), ventanas de brillo de nieve | Ambiente nevado en la cabaña |
| `scripts/edu/leccion_base.gd` (suelo exterior) | Suelo casi blanco y cielo azul pálido | Parece paisaje de invierno |
| `scripts/edu/edu_i18n.gd` L4 | "Ropa para el frío", "Hace frío", "Abrigarse" | Un frío que no existe en La Paz |
| `scripts/edu/edu_i18n.gd` L6 | "Tu compañero se cayó en la nieve" | Escena de nieve |
| `scripts/edu/leccion_6_amigo.gd` | Muñeco tumbado en la nieve | Escena de nieve |
| `Docs/PLAN_LECCIONES_DESASTRES.md` D6 | "Tormenta invernal" | No aplica en BCS |
| `scripts/edu/leccion_1_cabana.gd` y L1 | "Chimenea" | En casas de La Paz es más común el fogón o la estufa de gas |
| `scripts/edu/leccion_5_bosque.gd` | Árboles genéricos | Vegetación de BCS es de zona seca |

Nota: el juego principal (`main_menu`, mundo y cabaña) también está nevado. Este plan **no lo toca**: solo cambia el modo educativo, según lo que se pidió.

## 2. Ambiente de La Paz (decisión de diseño)

Propuesta: escenario de **casa en zona seca, con sol**.
- **Cielo:** día claro, sol fuerte, atardecer cálido. Sin nieve ni partículas de nieve.
- **Suelo exterior:** arena y tierra, con piedras. Textura CC0 (ambientCG, "sand" o "ground").
- **Vegetación:** palo verde, mezquite, cardón y pitaya. Fuentes CC0: Kenney Nature Kit y Quaternius Ultimate Nature (revisar que incluyan cactus; si no, modelos sencillos propios).
- **Casa:** paredes de block o estuco, techo plano o de palapa, puerta de madera, ventana con reja. Se reutiliza la geometría de la cabaña (`cabin_edu.tscn`) con texturas nuevas; los materiales de la cabaña son madera, así que hay que cambiarlos.
- **Luz:** tonos cálidos, sombra marcada, sin tarjetas de luz de nieve.

## 3. Cambios por lección

| Lección | Cambio | Razón |
|---|---|---|
| L1 fuego | "Chimenea" → **fogón de cocina** (leña o gas). Mismo juez: se apaga con agua y se sale. Mensaje: nunca dejar un fogón encendido sin vigilancia | El fogón es el fuego común en casa |
| L2 puerta | Ninguno | Universal |
| L3 animales | Vaca, gallina y cerdo se mantienen; añadir burro o cabra (animales comunes en BCS) | Realidad local |
| **L4** | **Ropa para el frío → "Protegerse del sol y del calor"**. Objetivo: llevar agua y protección (sombrero o sombra) antes de salir a las horas de más sol. Juez: haber cogido agua y sombrero antes de salir. Mensaje: beber agua aunque no se tenga sed | El riesgo real en La Paz es el calor, no el frío |
| L5 bosque | Árbol → **mezquite o palo verde**. Se corta leña caída, no el cardón ni la pitaya. Replantar un brote de la misma especie | Cuidado de la vegetación seca |
| **L6** | **"Un amigo caído en la nieve" → "Un amigo que se siente mal por el calor"**. Pasos: 1) llevarlo a la sombra, 2) darle agua en sorbos, 3) pedir ayuda a un adulto y llamar al 911. Es un muñeco de práctica | Golpe de calor es un riesgo real; la ayuda profesional se pide siempre |
| L7 mochila | Objetos: quitar "galletas" navideñas y usar comida local (fruta, carne, agua); mantener herramientas y materiales | Coherencia |
| Final | "La Nochebuena" puede seguir como nombre de la familia de lecciones (Navidad se celebra en México), pero el cierre habla de cuidar la casa | Decisión (sección 7) |

## 4. Cambios en los planes de desastres

| Lección | Cambio |
|---|---|
| D1 terremoto | Se mantiene. Es el riesgo sísmico de México |
| D2 alerta y plan familiar | Se mantiene. Mencionar el sistema de alerta sísmica del país |
| D3 tsunami | Se mantiene. Costa del Pacífico |
| D4 huracán | Se mantiene y sube de prioridad. Es el riesgo más alto de BCS |
| D5 inundación | Se mantiene. Lluvias intensas |
| D6 **tormenta invernal → calor extremo y golpe de calor** | Se reemplaza. Relación con L4 y L6 |
| D7 incendio forestal | Se mantiene. Temporada seca |

## 5. Pasos técnicos

1. **Escenario:** `cabin_edu.tscn` pasa a `casa_edu.tscn` (copia): quitar tarjetas de nieve, cambiar materiales, ajustar luz.
2. **Entorno exterior:** `leccion_base.gd` cambia el suelo y el cielo de `_construir_entorno()` por arena y sol.
3. **Assets:** descargar CC0 (ambientCG para arena, Kenney/Quaternius para plantas). Cada archivo a `assets/externos/` con fuente y licencia en `CREDITOS.md`. **Requiere tu permiso para descargar** (ver sección 7).
4. **Textos:** actualizar las claves afectadas en `EduI18n` (L1, L4, L5, L6, L7, final) y las de D6. El náhuatl sigue sin revisión.
5. **Escenas de lección:** L4 y L6 cambian de estación y de objetos; L5 cambia de árboles; L1 cambia la etiqueta del fuego.
6. **Pruebas:** añadir una prueba que recorra las escenas de `scenes/edu/` y falle si encuentra nodos o materiales con "nieve" o "snow" en el nombre. Así no vuelve a colarse.
7. **Captura:** nueva captura de L1 y L4 para el video del 11 de noviembre.

## 6. Calendario y prioridades

El video se entrega el 11 de noviembre: si el cambio de escenario no está listo, el video mostraría la versión nevada. Por eso:

| Día | Trabajo |
|---|---|
| 1 | Textos de L1, L4, L6 y D6 (solo texto, sin assets). Cambiar el suelo y el cielo a arena y sol |
| 2 | Escenario de casa (`casa_edu.tscn`), quitar la nieve, materiales cálidos |
| 3 | L4 y L6 con sus nuevas mecánicas (agua y sombrero, sombra y 911). Captura para el video |
| 4 | L5 con mezquite y palo verde; revisar la lista de lecciones |
| Después del 11 | D4 huracán y D1 terremoto con el contexto local |

## 7. Decisiones que necesito

1. **Nombre del modo y de la familia de lecciones:** ¿mantenemos "Nochebuena en la cabaña" o cambiamos a algo como "Cuidemos nuestra casa"? Mi recomendación: cambiar el nombre a "Cuidemos nuestra casa" y dejar "Nochebuena" solo como cierre de la lista.
2. **Permiso para descargar assets CC0** (ambientCG y Kenney/Quaternius), con sus licencias registradas.
3. **Fogón o estufa en L1:** ¿fogón de leña o estufa de gas? Recomiendo fogón de leña: es el que más se parece a la mecánica actual.
4. **Revisión:** ¿tienes contacto en Protección Civil de La Paz para revisar L4 (calor), L6 (golpe de calor) y D4 (huracán)?
5. **Juego principal:** ¿quieres que el juego principal conserve la ambientación nevada, o también debe cambiar? Este plan no lo toca.
