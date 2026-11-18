# Plan Tripo3D — cómo generar los modelos del juego desde el navegador

Objetivo: que Claude genere, texture, rigee y descargue los modelos de `Docs/PLAN_SEMANA_2.md` (sección 3, 62 modelos) usando la sesión de Tripo que el usuario ya tiene iniciada, con costos controlados y sin intervención manual salvo aprobaciones.

## 0. Reglas de seguridad (mandan sobre todo lo demás)
- Se usa la sesión ya iniciada en el navegador integrado (`https://studio.tripo3d.ai/es/workspace/generate`). **Nunca** escribir contraseñas ni crear cuentas; si pide iniciar sesión, parar y avisar.
- **No** pulsar "Actualizar", comprar créditos ni activar pruebas de suscripción (Generar en partes, Textura 8K, Team Plan). No resolver captchas.
- **Créditos:** cada generación gasta créditos (hoy 1795). Antes de cada lote se muestra al usuario la lista y el costo estimado y se espera su sí. Tope por lote lo fija el usuario.
- Descargas: permitidas por el usuario para los modelos del juego. Cada archivo baja a una carpeta nueva y vacía del scratchpad, se trata como no confiable (no se ejecuta nada) y solo se copia al proyecto tras revisarlo.
- Los modelos de cuentas gratuitas pueden ser públicos y tener licencia abierta: **confirmar con el usuario su plan y la licencia** antes de publicar el juego, y anotar cada modelo en `Docs/CREDITOS.md` (fuente Tripo, fecha, prompt).

## 1. La herramienta (lo visto en la sesión del usuario)
- Barra lateral: **Imagen, Modelo, Dividir, Rellenar partes, Remesh, Smart UV, Textura, Editar, Upscale, PBR, Rig, Animar**.
- Panel Modelo: pestañas **Modelo HD / Malla Smart**; entradas texto (máx. 1000 caracteres), imagen, multivista y boceto; botón **Pose en T**; ajustes de geometría y textura (calidad de malla, topología triángulos/quads, conteo de polígonos, textura); modelo de IA **H3.1 – Máx. calidad**; botón **Generar** muestra el costo (hoy 30 créditos; en la portada, imagen a 3D 55).
- Subir modelo propio (OBJ, FBX, STL, GLB ≤150 MB) para remesh, textura o rig.
- La ventana del navegador integrado es estrecha: usar `find` y `read_page` para localizar botones antes de hacer clic con coordenadas, y comprobar con captura.

## 2. Fórmula de prompt (en inglés; el modelo lo entiende mejor)
`[objeto concreto] + [silueta y proporciones] + [materiales y desgaste] + [paleta] + [estilo] + [restricciones]`

- **Estilo base del juego (se pega al final de todo prompt):** `dark Christmas horror, stylized realistic game asset, muted desaturated colors, dirty worn PBR materials, single object, centered, no ground, no base, no text, no logo`.
- **Negativo** (campo aparte si existe): `text, logo, watermark, base, pedestal, background, extra limbs, floating parts, glossy plastic`.
- Primero las palabras importantes (pesan más). Un objeto por modelo. Frases cortas con formas y materiales, no adjetivos vagos ("scary", "cool").
- Personajes y animales: **Pose en T** (humanoides) o pose neutral de pie con cuatro patas visibles (cuadrúpedos), boca cerrada, sin armas pegadas al cuerpo.
- Armas y herramientas: horizontales, mango visible, sin manos; una sola pieza.
- Estructuras: vista isométrica, sin terreno alrededor, puertas y ventanas como vanos simples; pocas partes (se rellena con piezas aparte).
- Bucle de calidad: **generar → inspeccionar (4 vistas) → refinar**. Si sale bien, usar ese modelo (o una captura) como imagen de referencia para las variantes del mismo conjunto y mantener el estilo coherente.
- Máximo **2 reintentos** por modelo; si falla, anotar y pasar al siguiente.

### Plantillas por tipo
| Tipo | Plantilla |
|---|---|
| Animal | `farm cow, quadruped, bony thin body, patchy dirty fur, sunken eyes, muted browns and greys, standing neutral pose, ` + estilo |
| Humanoide | `corrupted elf, thin humanoid, torn red and green clothes, sharp ears, cracked mask, T-pose, ` + estilo |
| Herramienta | `iron axe, wooden handle with leather wrap, chipped blade, rust, lying horizontal, ` + estilo |
| Consumible | `glass bottle of milk, cork stopper, frosted dirty glass, upright, ` + estilo |
| Estructura | `ruined wooden chapel, snow on roof, broken windows, simple shapes, isometric view, ` + estilo |
| Vegetación | `snow covered pine tree, dark green layered branches, heavy snow, simple trunk, ` + estilo |

## 3. Canalización por tipo (presupuestos de `PLAN_SEMANA_2.md`)
| Tipo | Pasos en Tripo | Después |
|---|---|---|
| Criatura con rig | Modelo (Pose en T o neutral) → Remesh ~6–8k tris → Textura PBR 1K → **Rig** (humanoide o cuadrúpedo) | Blender: `tools/blender/personajes/process_model.py`, retarget de animaciones propias |
| NPC / minijefe | igual, ≤8k (jefes ≤12k) | igual |
| Herramienta / objeto de mano | Modelo → Remesh 500–2k tris → Textura PBR 1K, sin rig | GLB al proyecto, pivote en el agarre |
| Estructura | Modelo → Remesh 2–6k → Textura 1K | Fusionar con `tools/mundo/fusion_util.gd` |
| Vegetación / roca | Modelo → Remesh 300–1k tris → Textura 1K | MultiMesh |
- **Animar:** Tripo solo da animaciones genéricas (y bailes). Se usa **solo Rig**; las animaciones salen del banco propio retargeteado en Blender.
- Probar siempre con la herramienta "Remesh" y la "Smart UV" antes de texturizar si la malla viene muy densa (menos créditos y mejor rendimiento en Quest).

## 4. Presupuesto de créditos (a medir en la prueba piloto)
1. Anotar el costo de cada paso: Generar (30), Remesh, Textura, PBR, Rig, Upscale.
2. Con 1795 créditos y ~30 por generación hay margen para unas 50–60 generaciones sin extras; con textura y rig por modelo caben bastantes menos. **Priorizar P0** (ver orden sugerido en `PLAN_SEMANA_2.md`) y dejar P1/P2 para cuando haya créditos.
3. Medir la renovación gratuita diaria de créditos si existe y planear lotes diarios.

## 5. Prueba piloto (antes de cualquier lote)
Tres modelos con aprobación del usuario: **vaca** (cuadrúpedo con rig), **hacha de hierro** (herramienta) y **pino nevado** (vegetación). Se comparan costo, tiempo, calidad y facilidad de importar. Se ajusta esta guía con lo aprendido y se pasa al lote P0.

## 6. Control de calidad por modelo
- Silueta clara y proporciones correctas en 4 vistas; sin partes flotantes ni base.
- Triángulos dentro del presupuesto; textura 1K; PBR legible con la paleta apagada del juego.
- Escala real y pivote correctos tras importar; rig sin deformaciones raras en pose neutra (revisar con Blender).
- Si no cumple, reintento con prompt ajustado (máx. 2).

## 7. Registro y nombres
- Archivos: `tripo_<id>_<nombre>.glb` (id de la tabla de `PLAN_SEMANA_2.md`). Carpeta de entrada: scratchpad; destino final `assets/models/...` según el pipeline existente.
- Se mantiene `Docs/TRIPO_REGISTRO.md` con: id, nombre, prompt, modelo de IA, costos, intentos, estado y observaciones.

## 8. Preguntas abiertas al usuario
1. ¿Plan/licencia de Tripo y tope de créditos por lote?
2. ¿Aceptas que los P1/P2 esperen a que haya créditos?
3. ¿Navegador integrado (ya con la sesión) o Chrome? Por defecto: el integrado.
4. ¿Quién importa al proyecto: Claude con agentes Sonnet/Haiku (recomendado) o tú?

## 9. Lo aprendido en la prueba piloto (2026-11-02, vaca, hacha de hierro y pino nevado)
**Costos reales (créditos):** texto a 3D con textura 2K y malla normal = **25**; con calidad Ultra +15 (no usar); textura 4K +10; **Auto Rig = 20**. Vaca 45, hacha 25, pino 25: 95 en total. Quedaron 1700. El límite del usuario es **80 por modelo**, así que un modelo con rig cabe con holgura.
**Ajustes que se guardan solos en la cuenta** (panel "Geometría y textura"): Calidad de malla Ultra OFF, Textura ON, calidad de textura 2K, topología Triángulo, y el **conteo de polígonos** (slider de 500 a 2 000 000). Con eso Tripo genera directamente la malla ligera, sin pasar por Remesh ni gastar más: vaca 7,6k tris con 33 huesos, hacha 1,5k, pino 1,4k. Para rig en cuadrúpedos 7–8k va bien.
**Cómo operar la interfaz (ventana de 1400x900 con `resize_window`; la ventana por defecto es muy estrecha):**
- Ir a `https://studio.tripo3d.ai/es/workspace/generate`. La entrada vuelve a "imagen": hacer clic en el icono del lápiz (texto) a la izquierda del panel antes de escribir.
- Para escribir el prompt: clic en el campo, `ctrl+a` y `type`. `form_input` a veces no se guarda.
- Slider de polígonos: enfocarlo y mandar eventos de teclado (`Home` = 500, `PageUp` = +10, `ArrowRight` = +1) con una pausa de ~6 ms entre cada uno; se comprueba con `aria-valuenow`.
- Interruptores del panel: `role=switch`; el botón Generar muestra el costo (`Generar N`): revisar antes de pulsar.
- Generar tarda ~2,5 min; el Auto Rig ~2 min. Esperar con `wait` de 10 s.
- Rig: barra lateral "Rig" → tipo **Otro** para animales (el humanoide pide pose T) → "Auto Rig".
- Exportar: botón "Exportar" de abajo → formato GLB, textura 2k, "Exportar esqueleto" activo → botón amarillo. Baja a `C:\Users\mitte\Downloads` como `<nombre> 3d model.glb`. Mientras la descarga termina queda un `.tmp` que ya es un GLB completo y válido (empieza con `glTF`); se renombra al exportar el siguiente modelo.
**Resultado técnico:**
- Los GLB salen normalizados a **1 m en su eje mayor**. Hay que escalar en la escena (vaca ×2,4 de largo, hacha ×0,7, pino ×6 de alto).
- El rig trae 33 huesos (`tripo::Root`, `Spine_0..3`, `Head_0..2`, patas `Limb`, `Tail`) y una esfera `Icosphere` de visualización de huesos que el script quita.
- Texturas de 2048 px: `tools/blender/tripo/a_juego.py` las baja a 1024 y re-exporta (vaca 464 KB, hacha 164 KB, pino 211 KB).
- Los GLB de Tripo no traen animaciones: salen de nuestro banco retargeteado.
- `tools/blender/personajes/process_model.py` (decimado, sin UV ni materiales) **no** sirve para estos modelos con textura.
**Flujo de importación (lo hace Claude):**
1. Copiar el GLB de Descargas a una carpeta nueva del scratchpad y comprobarlo con Blender (`inspect.py`: tris, huesos, texturas).
2. `blender -b --python tools/blender/tripo/a_juego.py -- jobs.json` → deja el GLB en `assets/...`.
3. `godot --headless --path D:/12-12-12 --import` y comprobar que carga.
4. Anotar en `Docs/TRIPO_REGISTRO.md` y en `assets/externos/CREDITOS.md`.
Si algo no se puede hacer desde el navegador (descarga bloqueada, captcha), avisar al usuario para que lo descargue él.
