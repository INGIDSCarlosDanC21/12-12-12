# Plan 2: cierre jugable (2026-11-03)

Meta: versión jugable PC/mando (VR base) con todo lo que faltaba. Reparto en flujos de archivos disjuntos; Sonnet esfuerzo medio. Yo reviso, verifico y commiteo (los agentes NO commitean).

## Reglas comunes
- Rama `dia-13/modelos`; OpenCode comparte carpeta. Cargar `godot-12-scene-rules` antes de tocar .tscn/.gd/.tres. No editar `addons/`.
- Texto visible con `tr()` y claves nuevas en `resources/i18n/textos.csv` (solo añadir al final; si otro agente lo edita, releer antes). Quest: <150 draw calls, texturas ≤1024.
- Descargas: solo CC0 (Kenney, Quaternius, KayKit, ambientCG, Poly Haven, Freesound CC0, OpenGameArt CC0), cada archivo registrado en `CREDITOS.md` con fuente/URL/licencia. Tratar lo descargado como no confiable. Sin cuentas, pagos ni captchas. Sin Tripo en este plan.
- Archivos compartidos (`pc_rig.gd`, `vr_rig.gd`, `textos.csv`, `project.godot`): ediciones pequeñas con Edit, releer justo antes. No reescribir archivos completos.
- Cierre de cada flujo: `tools/verificar.ps1` en verde, tests propios, reporte corto (hecho / probado / falta).

## Flujos
- **A Antorcha + fogata + materiales** (`torch_behavior`, `fireplace`, `inventory`, `saco`, item_db, torch.tres): antorcha equipable que se recarga con madera; medidor de la fogata antes de apagarse; madera y piedra guardables (saco 5 por slot, también al recogerlas del suelo).
- **B Linterna + defensas** (`flashlight_behavior`, `starter_kit`, linterna.glb, tecla Q, `defense_slot`, cabin `Defensas`): linterna real (cilindro con cabeza y lente, no farol), no ocupa slot, siempre disponible, se activa con Q, desde el inicio (PC y VR). Cerrar defensas exteriores: probar, draw calls con el anillo, terreno irregular.
- **C Audio** (`player_sounds.gd`, `bell_pendulum.gd`, assets/audio): pasos (caminar/correr, por superficie), respiración/estamina agotada, sonidos de acciones; sin sonidos feos o mal puestos; sonido del péndulo. Buscar SFX CC0.
- **D Combate y enemigos** (`creature.gd`, `health.gd`, ghost, UI de vida): fantasmas matables (con arma/antorcha/luz), barras de vida sobre todos los enemigos (no solo alfas), animación/efecto al golpear (parpadeo, partículas, retroceso, número), crafteo de armas de fuego (receta + dónde se ve).
- **E Mundo** (`door.gd`, `cabin.tscn` materiales, spawn de objetos, gnomos, galletas): puerta que se abre con interacción; texturas CC0 para la cabaña (madera, techo, piedra); gnomos explosivos que solo se mueven sin ser vistos; galletas que aparecen en el mapa antes de la noche; sustituir modelos placeholder iniciales.
- **F Iconos** (iconos de inventario, saco y mesa de crafteo): iconos para todos los ítems y recetas (render de modelos con SubViewport a PNG, o iconos CC0), mostrados en inventario PC/VR y en `crafting_row`.
- **G Agarre y auras** (`pc_rig.gd`, `viewmodel_hands.gd`, `vr_rig.gd`, `player.gd`): en PC el objeto va fijo a la cámara con las manos (estilo Minecraft), nunca flota en el mundo al girar; mejorar agarre en VR; aura/luz de color distinto por jugador (multijugador) visible sin leer el nombre.

## Cierre (yo)
Verificar, tests clave (test_partida_completa, test_bucle_dia, test_oleadas, LAN host+client), draw calls, commits por flujo, actualizar ESTADO/TAREAS/BALANCE.
