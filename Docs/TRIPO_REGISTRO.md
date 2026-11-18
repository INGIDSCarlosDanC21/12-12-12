# Registro de modelos Tripo

| id | modelo | prompt (resumen) | ajustes | créditos | tris | rig | destino | estado |
|---|---|---|---|---|---|---|---|---|
| 1 | Vaca de granja | farm cow, quadruped neutral pose, bony, patchy hide, sunken eyes + estilo base | H3.1, textura 2K, 7.9k polys, sin Ultra | 25 + rig 20 | 7548 | sí (Otro, 33 huesos) | `assets/characters/vaca/vaca.glb` | importado, sin escena (escala ×2,4) |
| 13 | Hacha de hierro | iron axe, wooden handle leather wrap, chipped blade, lying horizontal + estilo base | H3.1, textura 2K, 1.5k polys | 25 | 1456 | no | `assets/world/props/hacha_hierro.glb` | importado, sin escena (escala ×0,7) |
| 57 | Pino nevado | snow covered pine tree, conical, layered branches heavy snow + estilo base | H3.1, textura 2K, 1.5k polys | 25 | 1383 | no | `assets/world/vegetacion_tripo/pino_nevado.glb` | importado, sin escena (escala ×6) |

Estilo base: `muted desaturated colors, dark Christmas horror, stylized realistic game asset, dirty worn PBR materials, centered, no ground, no base, no text, no logo`.
Créditos al terminar el piloto: 1700 (de 1795).

## Lote del 2026-11-11 (8 modelos, 200 créditos; quedan 1425)

Ajustes: H3.1, Ultra apagado, textura 2K, triángulos, conteo de polígonos fijado al generar (sin Remesh). Sin rig. Los humanoides se pidieron en pose A y salieron en pose T; `tools/blender/tripo/a_juego.py` les baja los brazos (`arms_down_deg`) y reduce la textura a 1K en WebP. El original en pose T sigue en la cuenta de Tripo con el identificador indicado, por si se quiere riguear (Auto Rig, 20 créditos).

| modelo | prompt (resumen) | polys pedidos | tris | escala en escena | destino | escena | id de Tripo |
|---|---|---|---|---|---|---|---|
| Gnomo musculoso (minijefe) | muscular garden gnome, bodybuilder torso, red hat, white beard, ceramic skin, fence post club | 6000 | 5912 | ×2,3 | `assets/characters/gnomo_musculoso/` | `scenes/ai/miniboss/miniboss_gnome.tscn` | fa3440c5-277d-45fd-8aeb-8c4e2caca197 |
| Guardián de la estatua (jefe) | stone guardian golem, cracked granite armor, horned helmet, stone fists, moss | 8000 | 7726 | ×3,5 (jefe), ×2,4 (estatua dormida) | `assets/characters/guardian_estatua/` | `boss_estatua.tscn`, `statue_boss.tscn` | d5fbd061-9463-41fb-8160-cf7f6ae5b3e4 |
| Merodeador nocturno | night prowler, thin hunched humanoid, clawed long arms, black fur cloak, bone mask | 5000 | 4715 | ×2,0 (brazos −40°) | `assets/characters/merodeador/` | `scenes/ai/merodeador/merodeador.tscn` | 93afbdfc-3d03-4b61-903d-969d6442a05a |
| Vigilante con alarma | nutcracker soldier sentinel, cracked wooden face, black hat, red coat, brass hand bell | 5000 | 4958 | ×2,0 (brazos −55°) | `assets/characters/vigilante/` | `scenes/ai/vigilante/vigilante.tscn` | ad18bec5-4cd8-43ec-b7d6-8d0de709f685 |
| Rompedor de estructuras | hulking brute ogre, huge forearms, iron bucket helmet, leather apron, chains, planks | 6000 | 5751 | ×2,0 | `assets/characters/rompedor/` | `scenes/ai/structure_breaker/structure_breaker.tscn` | 2453d95a-8110-42cc-b89b-0274cdbd827b |
| Visitante | tall faceless visitor, long dark overcoat, wide brimmed hat, blank face, burlap sack | 5000 | 4862 | ×2,3 (brazos −62°) | `assets/characters/visitante/` | `scenes/ai/visitante/visitante.tscn` | c437a4b0-bc01-4e79-933b-218699454b53 |
| Horno de fundición | stone smelting furnace, beehive shape, iron door grate with embers, stone chimney | 2000 | 1917 | ×1,3 (girado 22°) | `assets/world/props/horno_fundicion.glb` | `scenes/interactables/furnace.tscn` | d20d8aba-2b12-4120-8ea9-1fc5818e3c18 |
| Estatua de revivir | weathered stone angel on plinth, folded wings, iron lantern, moss and snow | 2500 | 2459 | ×2,6 | `assets/world/props/estatua_angel.glb` | `scenes/interactables/statue.tscn` | 2dba666d-027f-45f2-acac-4ecbe799ef0e |

Notas:
- De la lista T1 a T8 de `Docs/PLAN_MODELOS.md`: T1 (fragmento), T2 (hacha de cuerno) y T3 (elfo) ya estaban generados e integrados antes de este lote, aunque no figuraban en esta tabla. T4 (muñeco de nieve) y T5 (fantasma) usan modelos libres. T6 (saco) va dentro del modelo de Krampus. T7 (Krampus forma final) no se generó: sustituir el Krampus animado por uno estático perdería sus 12 clips. T8 (taller) ya existe como POI.
- Blender 5.3 incrusta un perfil ICC en los JPEG y Godot 4.7 no los lee (el modelo sale blanco). Por eso `a_juego.py` exporta ahora en WebP.
- Capturas: `tools/captura/captura_modelos_tripo.gd` → `tools/logs/captura/modelos_tripo_*.png`.
