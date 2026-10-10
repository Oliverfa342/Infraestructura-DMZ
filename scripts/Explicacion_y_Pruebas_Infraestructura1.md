# Configuración y pruebas realizadas — Infraestructura 1

**Estudiante:** Oliver Eliam Aquino Paulino  
**Matrícula:** 2024-1571  
**Entorno:** GNS3, FortiGate 7.0.9 (configuración por GUI), IOSvL2, Alpine Linux y MariaDB.

## Servicios

- **WEB-CAJA (10.15.72.2):** aplicación web para registrar ventas.
- **WEB-INVENTARIO (10.15.72.3):** aplicación web para consultar existencias.
- **DB-SERVER (10.15.72.4):** MariaDB; base `redes1571`, tablas `productos` y `ventas`.
- **USER-PC-1 (VLAN 10):** acceso autorizado a Caja, Inventario bloqueado, SSH hacia DMZ bloqueado.
- **USER-PC-2 (VLAN 20):** acceso a Caja, Inventario y SSH a los servidores.

## Pruebas y evidencia registrada

| Prueba | Resultado observado |
|---|---|
| `show vlan brief` en SW1 | VLAN 10 y VLAN 20 presentes |
| `show interfaces trunk` en SW1 | Gi0/0 trunk 802.1Q con VLAN 10 y 20, nativa 999 |
| `show vlan brief` en SW2 | VLAN 30 para los tres servidores |
| `ip -4 addr show eth0` en usuarios | Direcciones 10.15.71.2/25 y 10.15.71.130/25 |
| Ingreso desde VLAN 10 a `http://10.15.72.3/` | Página de acceso denegado por violación de política |
| Ingreso a `http://10.15.72.2/` | Sistema de Caja visible; venta de mouse registrada |
| Consulta a `redes1571.productos` | Tabla con Teclado, Mouse, Monitor y existencias |
| Consulta a `redes1571.ventas` | Venta registrada con importe de RD$650 |
| SSH desde VLAN 20 a 10.15.72.4 | Sesión establecida |
| SSH desde VLAN 10 a 10.15.72.4 | Política VLAN10-SSH-DENY y logs de denegación |
| Tráfico DMZ hacia Internet general | `DMZ-INTERNET-DENY` visible en FortiGate Forward Traffic |
| Tráfico de DMZ hacia LAN | `Implicit Deny` para flujos sin regla de autorización |
| Web Filter | Registros `blocked` de clientes VLAN 10 hacia `10.15.72.3` |

La conexión a Internet de la DMZ se limita mediante una regla HTTPS hacia el objeto FQDN `UPDATE-ALPINE` y una regla de denegación general. En pruebas anteriores se vieron errores DNS intermitentes; no se debe confundir un fallo DNS con una denegación de política. Los bloqueos se verifican con el identificador de política en los logs.

## Aplicaciones y base de datos

La captura de Caja muestra el registro de una venta de **Mouse**, cantidad 1, total **RD$650**. La tabla de ventas en MariaDB registró la operación. También se observaron los productos **Teclado**, **Mouse** y **Monitor** con sus cantidades de existencias.

## Configuración y comprobación del firewall

Se realizó a través de **la interfaz gráfica de FortiGate**, de acuerdo con el requisito del docente. El archivo `.conf` adjunto al repositorio es únicamente un **extracto sanitizado para documentación**, no un procedimiento de configuración por CLI.

## Material de apoyo

- [Video demostrativo](https://youtu.be/dV10_KzzoZs)
- [Comandos de prueba](Comandos_Pruebas_Infraestructura1.txt)
- [Direccionamiento y puertos](Direccionamiento_y_Puertos_Infraestructura1.txt)
- [Políticas documentadas](../running-configs/FortiGate_configuracion_relevante_SANITIZADA.conf)
