# Instrucciones para agentes (OpenCode y Claude Code)

Este archivo es el punto de entrada para **OpenCode**. Si eres **Claude Code**, tu punto de
entrada es `CLAUDE.md`; ambos llevan a la misma fuente de verdad. Léelos en este orden:

1. `CLAUDE.md` — qué es el proyecto, stack, entorno y cómo ejecutar.
2. `Docs/CONTRATOS.md` — **fuente de verdad técnica**. Si algo choca con otro documento, gana este.
3. `Docs/GDD.md` — diseño de mecánicas.
4. `Docs/TAREAS.md` — tablero de tareas, propietario, rama y criterio de aceptación.
5. `Docs/ESTADO.md` — último avance y siguiente paso.
6. `.claude/skills/godot-12-scene-rules/SKILL.md` — checklist antes de tocar escenas/scripts.

## Reglas que no se negocian

- **Una tarea = una rama corta** que sale de la rama del día. Nunca trabajar en `main`.
- **Un solo escritor por archivo.** Antes de empezar, mira `Docs/TAREAS.md` y reclama la tarea.
  En los archivos calientes (`project.godot`, `player.tscn`, `player.gd`, `game.tscn`,
  `map.tscn`, `textos.csv`) se trabaja por turnos y se avisa en el tablero.
- **Cierra el editor de Godot antes de editar un `.tscn`.** Si el editor tiene la escena
  abierta, al guardar pisa los cambios del agente.
- **No editar `addons/`.** No se borran assets ni código sin confirmación del usuario.
- **Nada se construye por código:** solo se instancian `PackedScene` existentes. Parámetros
  con `@export` y datos en `.tres`.
- **Todo texto visible usa `tr("CLAVE")`** y la clave debe existir en
  `resources/i18n/textos.csv`.
- **No cambiar `CONTRATOS.md` ni mecánicas del GDD** sin aprobación del usuario. Si hace
  falta, se deja la propuesta en `Docs/TAREAS.md` y se espera.
- Tipado estático en GDScript, tabulaciones para indentar, nombres `snake_case` en archivos y
  `PascalCase` en escenas, nodos y `class_name`.

## Rutina de sesión

**Al empezar**
1. `git -c safe.directory=D:/12-12-12 status` y `git log --oneline -5`.
2. Leer `Docs/ESTADO.md` y `Docs/TAREAS.md`.
3. Reclamar una tarea (estado `WIP` + tu nombre + rama + fecha) o continuar la tuya.
4. Crear/actualizar la rama de la tarea.

**Al terminar**
1. Pasar `tools/verificar.ps1` sin errores (y prueba en 2 instancias si toca red).
2. Commit con mensaje `Día N: <tema>` (o `WIP: <tema>` si no está terminada).
3. Actualizar `Docs/TAREAS.md` (estado y criterio cumplido) y `Docs/ESTADO.md`.
4. Dejar el **bloque de handoff** (plantilla abajo) en `Docs/ESTADO.md`.
5. Avisar de los archivos calientes que quedaron tocados.

## Bloque de handoff

```markdown
### Handoff — AAAA-MM-DD — <agente>
- **Hecho:** ...
- **Archivos tocados:** ...
- **En curso / sin terminar:** ...
- **Siguiente paso:** ...
- **Bloqueos:** ...
- **Cómo verificar:** comando exacto o pasos en el editor.
```

## Verificación

```powershell
# Importar recursos y detectar errores de carga/parseo
& $env:GODOT --headless --path D:/12-12-12 --import

# Arrancar 180 fotogramas sin ventana y sin VR
& $env:GODOT --headless --xr-mode off --path D:/12-12-12 --quit-after 180
```

`tools/verificar.ps1` hace ambas cosas, filtra `SCRIPT ERROR`, `Parse Error`, `ERROR:` y
devuelve código distinto de 0 si encuentra algo.

## Estado actual (2026-10-31)

Semana 1, día 3 en curso en la rama `dia-3/objetos`. Hay trabajo sin commitear de la sesión
de Claude; el detalle y el reparto están en `Docs/TAREAS.md`. No reescribas archivos que otro
agente tenga en `WIP`.
