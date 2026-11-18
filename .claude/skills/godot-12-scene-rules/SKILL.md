---
name: godot-12-scene-rules
description: Reglas obligatorias para crear o editar escenas, scripts y recursos de Godot en el proyecto 12/12/12 (escenas editables en el editor, capas, autoloads, i18n, rendimiento Quest 2). Úsala antes de tocar cualquier archivo .tscn, .gd, .tres o .gdshader.
---

# Reglas de escenas, scripts y recursos (12/12/12)

Esta skill resume las reglas del proyecto. La **fuente de verdad** es `Docs/CONTRATOS.md` (`D:\12-12-12\Docs\CONTRATOS.md`). Si algo de aquí choca con ese documento, gana `CONTRATOS.md` y hay que avisar al orquestador. Para el diseño de mecánicas, consulta `Docs/GDD.md` antes de implementar.

## Resumen de reglas

**Escenas editables**
- Cada entidad es una escena `.tscn` con sus nodos visibles en el árbol: mallas, colisiones, áreas, `Marker3D`, luces y partículas.
- El código **solo instancia** escenas existentes (`PackedScene` exportado). Nada de construir nodos visuales por código.
- `@tool` solo para vistas previas en el editor, nunca para generar contenido oculto.
- Ningún nodo con `visible = false` sin que su nombre lo explique (`HiddenUntilNight`).

**Código y datos**
- Parámetros con `@export`; datos en Resources `.tres` (`resources/items/`, `recipes/`, `waves/`, `ai/`, `themes/`, `i18n/`).
- GDScript con tipado estático (`var x: int`, `func f() -> void`). Tabulaciones para indentar.
- Archivos `snake_case`; escenas, nodos y `class_name` en `PascalCase`; señales en pasado (`rule_broken`).
- Autoloads con nombre fijo: `EventBus`, `GameState`, `NetManager`, `Settings`.

**Textos**
- Todo texto visible usa `tr("CLAVE")`. Nunca texto literal en escenas ni scripts.
- Claves en `MAYUSCULAS_CON_GUIONES_BAJOS` (`RULE_*`, `ITEM_<ID>_NAME`, `MENU_*`, `OPT_*`, `HUD_*`, `DEATH_*`, `PHASE_*`).
- Archivo único: `resources/i18n/textos.csv` (columnas `keys,es,en`).

**Capas**
- Render: 1 Mundo · 2 Viewmodel · 3 UI3D · 4 SoloVR · 5 SoloPC.
- Física: 1 Mundo · 2 Jugador · 3 Animal · 4 Enemigo · 5 Agarrable · 6 Recurso · 7 Interactuable · 8 Trigger.

**Motor y convenciones**
- Godot 4.7.2, renderer Mobile, física Jolt. Unidades en metros. +Y arriba, −Z adelante.
- Carpetas: `scenes/`, `scripts/` (espejo de `scenes/` más `core/`), `assets/`, `resources/`, `tools/`, `addons/` (no editar).

**Rendimiento Quest 2 (mínimo 75 FPS)**
- Presupuesto de draw calls bajo (objetivo del plan: menos de 150 en Quest).
- Sin luces dinámicas extra: como máximo 1 luz dinámica con sombra.
- Materiales compartidos (`.tres`), no duplicados por instancia.
- Glitches en VR ocurren en el mundo (vértices, materiales, luces). Nada de distorsión de pantalla completa en VR.

## Checklist antes de entregar

- [ ] La escena abre en el editor sin errores ni advertencias de recursos faltantes.
- [ ] Todos los nodos son visibles en el árbol y tienen nombre descriptivo en `PascalCase`.
- [ ] Parámetros ajustables expuestos con `@export`.
- [ ] Todo texto visible pasa por `tr("CLAVE")` y la clave existe en `textos.csv`.
- [ ] Capas de render y física correctas en cada nodo que las necesite.
- [ ] Nada se construye por código: solo se instancian `PackedScene` exportadas.
- [ ] Sin texto literal en escenas ni scripts.
- [ ] Presupuesto Quest 2: draw calls bajos, sin luces dinámicas extra, materiales compartidos.
- [ ] Ningún nodo oculto sin que su nombre lo explique.
- [ ] Ningún archivo de `addons/` modificado.

## Anti-patrones

### 1. Construir una malla en código

Mal: el nodo visual aparece en la ejecución, no se ve en el editor y no se puede ajustar desde el Inspector.

```gdscript
# MAL
func _ready() -> void:
	var mesh_instance := MeshInstance3D.new()
	mesh_instance.mesh = BoxMesh.new()
	add_child(mesh_instance)
```

Bien: la escena se instancia desde un `PackedScene` exportado. Su contenido se edita en el editor.

```gdscript
# BIEN
@export var cabin_scene: PackedScene

func _ready() -> void:
	var cabin := cabin_scene.instantiate()
	add_child(cabin)
```

### 2. Texto literal en un script

Mal: el texto no se traduce y no existe en el CSV.

```gdscript
# MAL
$HealthLabel.text = "Vida"
```

Bien: la clave pasa por `tr()` y debe existir en `resources/i18n/textos.csv`.

```gdscript
# BIEN
$HealthLabel.text = tr("HUD_HEALTH")
```

### 3. Valores mágicos en el código

Mal: el ajuste queda enterrado en el script y no se puede cambiar desde el editor.

```gdscript
# MAL
const RESPAWN_TIME := 10.0
```

Bien: se expone con `@export` (o se guarda en un `.tres`).

```gdscript
# BIEN
@export var revive_time: float = 10.0
```

### 4. Nodo oculto sin explicación

Mal: `visible = false` en un nodo llamado `Fogata`. Nadie sabe por qué está oculto.

Bien: el nombre dice el motivo, por ejemplo `HiddenUntilNight`, y el script lo activa de forma explícita.

### 5. Luces dinámicas de más

Mal: una `OmniLight3D` con sombra por cada fogata, antorcha y ventana. Rompe el presupuesto de Quest 2.

Bien: fuego con emisivos y tarjetas de luz, y una sola sombra direccional corta (ver plan, día 4).

### 6. Material duplicado por instancia

Mal: cada pino crea su propio `StandardMaterial3D`. Bien: un único `.tres` compartido por todos los pinos.

Si una tarea exige cambiar una regla de `CONTRATOS.md`, una mecánica del GDD o borrar código o assets, parar y preguntar antes.
