# Tablero del modo educativo (La Paz, BCS)

Actualizado: 2026-11-08 (noche). Entregas: **video 11 nov** · **exhibición y jurado 18 nov**.

Leyenda: `TODO` · `WIP` · `REVIEW` · `DONE` · `BLOCKED` (espera a otra persona).

## 1. Reparto

| Dueño | Qué hace | Por qué |
|---|---|---|
| **Claude** | Código, escenas, textos, pruebas, integración de assets y audio | Es el trabajo de proyecto |
| **Usuario** | Gemini (música), Tripo (modelos de plantas y casa), revisión de plantas y casas, grabación del video | Tiene las cuentas y el criterio local |
| **Descargas en segundo plano** | Texturas, cielo, modelos CC0 | No bloquean el trabajo de código |

Regla: no se generan modelos sin pedirlos. Lo que no tenga modelo aprobado **se deja sin modelo** (no se pone un placeholder generado).

## 2. Tareas del usuario

### U1 · Música con Gemini (prioridad alta para el video)
Pedir 4 pistas. Formato: WAV o MP3, 44.1 kHz, en bucle, sin voces.

| Pista | Archivo destino | Prompt sugerido | Duración |
|---|---|---|---|
| Menú | `assets/audio/musica/menu_la_paz.ogg` | Instrumental acústico cálido, guitarra y marimba, tono tranquilo y alegre, pulso lento (~90 bpm), sin voces, sensación de tarde de sol en el puerto | 90 s, en bucle |
| Lección | `assets/audio/musica/leccion_calma.ogg` | Piano y guitarra suaves, sensación de concentración, pulso lento (~80 bpm), sin voces | 2 min, en bucle |
| Alerta | `assets/audio/musica/alerta_suave.ogg` | Pulso sutil de cuerdas graves, tensión leve pero nada asustante, sin golpes fuertes ni sustos, sin voces | 60 s, en bucle |
| Cierre | `assets/audio/musica/cierre_festivo.ogg` | Corto y festivo: guitarra, vientos, percusión ligera, tono de celebración familiar | 25 s, sin bucle |

Condiciones: la pista debe poder usarse en un juego educativo sin restricciones de uso no comercial. Guardar el prompt y la fecha. Si Gemini no da licencia clara, avisarme antes de usarla.

### U2 · Modelos con Tripo (solo lo que no tiene modelo libre)
Ajustes para todos: estilo **realista**, textura 1K, sin base ni fondo, centrado, escala real indicada, sin rig. Máximo 8 000 triángulos por modelo.

| # | Modelo | Prompt | Escala real | Prioridad |
|---|---|---|---|---|
| T1 | Cardón (cactus columnar) | realistic columnar cardon cactus of Baja California Sur, 4 to 6 arms, ribs, dry spines, dusty green, desert, isolated, no ground | 4 m | Alta (L5) |
| T2 | Palo verde | realistic palo verde tree, green bark, small leaves, thin sparse crown, dry desert, isolated, no ground | 4 m | Alta (L5) |
| T3 | Mezquite | realistic mesquite tree, twisted dark bark, small pinnate leaves, dry desert, isolated, no ground | 3 m | Media (L5) |
| T4 | Casa de estuco | realistic single-story house in La Paz, white stucco walls, flat roof, barred window, wooden door, no ground, no fence | 6 × 8 m | Media (escenario) |

Mi recomendación: empezar por T1 y T2. Si el resultado no convence, no seguir gastando créditos: la escena se queda sin ese modelo.

### U3 · Revisión
- Revisar cada modelo de planta y de casa antes de integrarlo (U2).
- Revisar el texto de las lecciones L2, L4 y L6 con personal de salud o de protección civil.
- Grabar el video del 11 de noviembre con las lecciones que estén en `DONE`.

### U4 · Blender
Abierto para revisión de modelos si hace falta. No se necesita para generar nada en este tablero.

## 3. Tareas de Claude

| ID | Tarea | Estado | Criterio de aceptación |
|---|---|---|---|
| C1 | Escenario exterior: cielo HDRI `kloofendal_43d_clear_puresky` y suelo de arena (`coast_sand_05`) en `leccion_base.gd` | DONE (captura sin nieve, sol y sombras) | Captura diurna sin nieve |
| C2 | `casa_edu.tscn`: quitar tarjetas de nieve (DONE); estuco en paredes (pendiente: la casa es una sola malla de madera, hay que separarla en Blender) | PARTIAL | Captura interior sin nieve ni madera nevada |
| C3 | L1: textos revisados (la lección habla de fuego; la etiqueta "Fuego" es del juego) | DONE | Textos actualizados en es/en |
| C4 | L4 "Protegerse del sol y del calor": agua antes de salir, pista y resumen | DONE | Salir sin agua da pista, con agua completa |
| C5 | L5: árbol según disponibilidad. Si T1/T2 no existen, la escena se queda sin ese modelo | TODO | Prueba sin modelos de planta falla controlada, no con error |
| C6 | L6 "Golpe de calor": sombra, agua en sorbos y 911 | DONE (texto pendiente de revisión médica) | Orden de pasos y textos revisados |
| C7 | Música: reproducir la pista de cada escena (menú, lección, alerta, cierre) con cruce de 2 s | TODO (espera U1) | Cambia de pista al entrar en cada escena |
| C8 | Audio de sitio: cigarras, fogón, golpe de puerta | TODO | Sonido en la escena correspondiente |
| C9 | Prueba automática: falla si una escena de `scenes/edu/` contiene "nieve" o "snow" | DONE | Prueba en `test_edu_lecciones` |
| C10 | Integrar modelos aprobados (U2) en su escena | TODO (espera U2) | Captura con el modelo |

## 4. Descargas en segundo plano

| Recurso | Estado | Destino |
|---|---|---|
| Arena `coast_sand_05` (1K) | DONE | `assets/externos/texturas/polyhaven/coast_sand_05/` |
| Tierra `dirt` (1K) | DONE | `assets/externos/texturas/polyhaven/dirt/` |
| Estuco `clay_plaster` (1K) | DONE | `assets/externos/texturas/polyhaven/clay_plaster/` |
| Cielo `kloofendal_43d_clear_puresky` (1K HDR) | DONE | `assets/externos/cielo/polyhaven/` |
| Modelos de planta CC0 de zona seca | BLOCKED: Quaternius está en Google Drive; Poly Haven no tiene cactus ni palmas | Decidir si se usan los modelos de Tripo |
| Sonidos de sitio (cigarras, mar, viento seco) | TODO | Fuentes con licencia por archivo |

Licencias registradas en `assets/externos/CREDITOS.md`.

## 5. Calendario

| Día | Usuario | Claude |
|---|---|---|
| 8 nov (noche) | Lanza U1 (Gemini) y T1/T2 (Tripo) | C1 |
| 9 nov | Revisa modelos T1/T2 | C2, C3 |
| 10 nov | Entrega música y modelos; graba el video | C4, C6, C7 |
| 11 nov | Entrega el video | C5, C8, C9 |
| 12–17 nov | Revisión de textos y de plantas | C10 y pulido |
| 18 nov | Exhibición | — |
