# Movimiento: valores de referencia por plataforma (M-01)

Estado: **referencia medida en código**, no ajustada por sensación. El ajuste fino (aceleración,
inercia, giro) lo decide el usuario jugando en cada plataforma. La prueba
`scripts/test/test_fase1_base.gd` comprueba que estos valores son coherentes entre plataformas.

## Valores actuales

| Parámetro | PC (`scripts/player/pc_rig.gd`) | VR (`scripts/player/vr_rig.gd`) | Táctil (`scripts/ui/touch_stick.gd`) |
|---|---|---|---|
| Andar (m/s) | 2.2 (`walk_speed`) | 2.2 (`walk_speed`) | Usa la velocidad del rig de PC |
| Correr (m/s) | 4.5 (`sprint_speed`) | 4.5 (`sprint_speed`) | Igual que PC |
| Agacharse (m/s) | 1.2 (`crouch_speed`) | 1.2 (`crouch_speed`) | Igual que PC |
| Salto (m/s) | 4.5 (`jump_velocity`) | No hay salto en `vr_rig.gd` | Igual que PC |
| Aceleración en suelo (s) | 0.15 (`ground_accel_time`) | Lo lleva `XRToolsMovementDirect` | Igual que PC |
| Fricción en suelo | 20 (`ground_friction`) | Lo lleva `XRToolsMovementDirect` | Igual que PC |
| Aceleración en aire | 3.0 (`air_acceleration`) | No aplica (VR sin salto) | Igual que PC |
| Modo de desplazamiento | Siempre continuo | `Settings.vr_smooth_move` (por defecto `true`: continuo; `false`: teletransporte) | Siempre continuo |
| Zona muerta del stick | — | — | 0.12 (`deadzone`) |
| Giro | Ratón: `Settings.mouse_sensitivity`; mando: `gamepad_look_speed_deg` 160 | Suave o por pasos según `Settings.vr_snap_turn` (por defecto pasos de 30°) | Arrastre en pantalla (`touch_look`) |

## Notas

- Táctil no tiene un rig propio: el stick mueve el mismo `pc_rig`, así que comparte velocidades y
  aceleración con PC.
- VR: `walk_speed` y `sprint_speed` se pasan a XR Tools (`movement_direct.max_speed` y el multiplicador de
  correr), por eso los valores coinciden con PC. Con `Settings.vr_smooth_move = false` el desplazamiento
  pasa a teletransporte (`function_teleport`).
- El giro en VR por defecto es a pasos (`vr_snap_turn = true`, 30°). Para Quest 2 conviene probar el giro
  suave, porque el paso brusco puede descolocar; es decisión del usuario tras jugar.

## Qué falta para cerrar M-01

1. Partida corta en Quest 2 y en PC: caminar, correr, saltar y girar. Anotar si el arranque o la parada
   se sienten bruscos.
2. Prueba en móvil (Android) con el stick táctil: zona muerta y tamaño de botones (se cierra con U-07).
3. Solo después, cambiar valores. Todo cambio se hace en un único lugar por plataforma y se vuelve a
   ejecutar `test_fase1_base.gd`.
