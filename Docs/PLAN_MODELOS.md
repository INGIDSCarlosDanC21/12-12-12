# Plan de modelos 3D: descargar lo común, generar solo lo único

Decisión del usuario (2026-11-02): **no gastar Tripo en lo que se encuentra en línea**. Lo común se descarga de bibliotecas libres; Tripo solo para piezas específicas del juego que no existen en otro lugar. Los créditos de Tripo (1700) sobran y no hay que gastarlos por gastarlos.

## A. Descargar (solo CC0 o equivalente libre; anotar licencia y URL por archivo)
Fuentes preferidas: Quaternius (quaternius.com / itch.io, CC0), KayKit de Kay Lousberg (CC0, GLB), Kenney (kenney.nl, CC0, GLB), Poly Haven (CC0), OpenGameArt (revisar licencia). Preferir **modelos sueltos en GLB**; si solo hay FBX/OBJ, convertir con Blender. La conexión es lenta (~0,5 MB/s): bajar solo los paquetes o modelos que se usan.

| Necesidad | Qué buscar |
|---|---|
| Vaca, cerdo, gallina (animados/rigeados) | Quaternius "LowPoly Animated Animals" / "Farm Animals" |
| Ermitaño y viajero (humanoides rigeados) | KayKit Adventurers / Quaternius Ultimate Characters |
| Hachas, picos, martillo, cuchillo, cubo | KayKit "RPG Tools Bits" (ya hay un hacha de hierro de Tripo del piloto; se conserva) |
| Escopeta de doble cañón, pistola | Quaternius "Ultimate Guns" o similar |
| Linterna antigua, antorcha | KayKit RPG Tools Bits / Kenney |
| Botella de leche, galletas, carne, fruta, cantimplora, pastel | Kenney Food Kit / Quaternius Food |
| Caja de munición, tronco cortado, mineral, lingote | Quaternius / Kenney Survival Kit |
| Árbol de Navidad, cofres, mesa de trabajo, barricada, pinchos, cama/mesa/silla | Kenney Furniture/Survival, KayKit Dungeon, Quaternius |
| Granero, cabaña, entrada de mina, capilla, campamento, establo, torre, vallas | Kenney Fantasy Town/Survival Kit (ya en el proyecto), KayKit, Quaternius Village |
| Pinos, abetos, arbustos, rocas, tocones, troncos caídos | Quaternius Ultimate Nature, Kenney Nature Kit |

## B. Generar con Tripo (solo lo único del juego)
Ajustes fijos: ver `Docs/PLAN_TRIPO.md` sección 9 (textura 2K, sin Ultra, polígonos indicados). Máximo 80 créditos por modelo.

| id | Modelo | Prompt base (+ estilo base) | Polys | Rig | Escala real |
|---|---|---|---|---|---|
| T1 | Fragmento de Krampus | broken demon horn shard, jagged broken end, dark bone with faint glowing red ember cracks | 800 | no | 0,25 m |
| T2 | Hacha de cuerno (arma del final secreto) | two-handed battle axe forged from a giant black demon horn, crimson glowing runes, bone and iron binding, leather wrapped handle, lying horizontal | 2500 | no | 0,9 m |
| T3 | Elfo corrupto | corrupted christmas elf, thin hunched humanoid, torn green and red clothes, cracked porcelain mask, long sharp ears, T-pose | 6000 | Humanoide | 1,0 m |
| T4 | Muñeco de nieve maldito | cursed snowman, three stacked cracked snow balls, glowing coal eyes, rotten carrot nose, twig arms, torn scarf, arms out T-pose | 5000 | Humanoide | 1,8 m |
| T5 | Fantasma navideño | christmas ghost, tattered translucent white cloth figure wearing a santa hat, hollow black eyes, floating, no legs | 3000 | no | 1,7 m |
| T6 | Saco de Krampus | huge burlap sack tied with rope and chains, bulging, dark stains | 1500 | no | 1,2 m |
| T7 | Krampus forma final | krampus final form, giant demon goat man, long curved horns, chains, ragged fur, burning red eyes, muscular, T-pose | 10000 | Humanoide | 3,0 m |
| T8 | Taller de Santa en ruinas | ruined santa workshop, crooked wooden building with chimney, broken toy sign, snow on roof, isometric, no ground | 5000 | no | 12 m |

Total estimado: 8 × 25 + 3 rigs × 20 = unos 260 créditos.

## C. Destinos en el proyecto
- Animales y NPC: `assets/characters/<nombre>/`
- Herramientas y objetos: `assets/world/props/`
- Vegetación y rocas: `assets/world/vegetacion_tripo/` (Tripo) o `assets/externos/modelos/<pack>/` (descargados)
- Cada archivo nuevo: línea en `assets/externos/CREDITOS.md` (fuente, URL, licencia) y, si es de Tripo, fila en `Docs/TRIPO_REGISTRO.md`.
- Los descargados no se cablean en escenas hasta el día 13.
