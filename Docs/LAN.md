# Multijugador LAN: PC y Quest

El juego usa **ENet** en el puerto **24912** y descubre partidas por **UDP broadcast** en el
**24913**. No hay internet ni cuentas: todo va por la red local.

## Lo mínimo para que funcione

1. PC y Quest en la **misma Wi-Fi** (misma red, sin "aislamiento de clientes" de invitados).
2. En el PC que hospeda, ejecutar una vez como administrador:

   ```powershell
   powershell -ExecutionPolicy Bypass -File tools/lan_firewall.ps1
   ```

   El script quita las reglas de **bloqueo** que Windows creó para `12-12-12.exe` y abre
   TCP/UDP 24912 y UDP 24913. Las reglas de bloqueo tienen prioridad sobre las de permiso: si
   existen, el juego no recibe conexiones aunque el puerto esté "abierto".
3. Crear la partida en el PC. El lobby muestra **"IP del host: 192.168.x.x"**. Esa es la IP que
   hay que usar en el visor.
4. En el Quest, abrir Multijugador. Si aparece la partida en la lista, pulsarla con el rayo.
   Si no aparece (algunos routers filtran el broadcast), escribir la IP con el **teclado
   numérico** del panel y pulsar **Unirse por IP**.

## Si no conecta

| Síntoma | Qué mirar |
|---|---|
| El Quest no ve la partida en la lista | Es normal en algunas redes. Usa la IP manual del lobby. |
| "No se pudo conectar" tras 10 s | IP incorrecta, equipos en redes distintas, firewall o aislamiento de clientes. |
| El PC ve al Quest pero el Quest no al PC | Ejecuta `tools/lan_firewall.ps1` en el PC (reglas Block). |
| Funciona en el mismo PC pero no entre PCs | Firewall del PC que hospeda y perfil de red (Público/Privado). |
| La Wi-Fi es de invitados | Suele aislar clientes; usa una red normal o un punto de acceso propio. |

Comprobar la IP del PC:

```powershell
Get-NetIPAddress -AddressFamily IPv4 |
  Where-Object { $_.IPAddress -notlike "127.*" -and $_.IPAddress -notlike "169.254.*" }
```

Comprobar que el puerto del juego escucha (con el host abierto):

```powershell
Get-NetUDPEndpoint -LocalPort 24912,24913
```

## Pruebas automáticas

```powershell
# Host y cliente en el mismo PC (loopback)
godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/test_partida_completa_lan.gd -- host
godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/test_partida_completa_lan.gd -- client

# Cliente contra la IP de la LAN
godot --headless --xr-mode off --path D:/12-12-12 --script res://scripts/test/test_partida_completa_lan.gd -- client 192.168.10.103
```

El host imprime `RESULTADO test_partida_completa_lan: 0 FAIL`; el cliente, lo mismo. Estas
pruebas usan dos procesos en el mismo PC: no sustituyen a probar con el visor, pero verifican
que la replicación y el descubrimiento funcionan por la interfaz real.
