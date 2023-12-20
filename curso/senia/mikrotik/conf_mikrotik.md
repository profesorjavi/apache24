# Configuración de Mikrotik del proyecto
Las IPs habrá que adaptarla a la topología de red del proyeto en concreto

## Configuración de las interfaces
```mikrotik
interface set ether4 name=ADMIN

interface set ether1 name=INET

interface set ether2 name=LAN

interface set ether3 name=DMZ
```
## Configuración de las IPs
La direccion de INET la obtenemos por DHCP ya viene preconfigurado, esta en la red 192.168.1.0

```mikrotik
ip address add interface=LAN address=192.168.10.1 netmask=255.255.255.0 disabled=no
 
ip address add interface=DMZ address=10.0.1.1 netmask=255.255.255.0 disabled=no

ip address add interface=ADMIN address=192.168.8.1 netmask=255.255.255.0 disabled=no
```

## Configuración NAT
Configuración del NAT para que LAN puedan salir por INET
```mikrotik
ip firewall nat add chain=srcnat src-address=0.0.0.0/0 dst-address=0.0.0.0/0 out-interface=INET action=masquerade disabled=no
```

### Reglas para el Port Fordwardind
Anadir reglas NAT para la redirección de puertos 80 de INET a servidore WEB DMZ
```mikrotik
ip firewall nat add chain=dstnat protocol=tcp dst-port=80 in-interface=INET action=dst-nat to-addresses=10.0.1.2
```
### Reglas de filtrado
Añadir reclas FILTER para redirigir las peticiones de la LAN a la DMZ
```mikrotik
ip firewall filter add chain=forward dst-address=10.0.1.2 protocol=tcp dst-port=80 in-interface=LAN action=accept
``
```mikrotik
Regla para que un pc pueda acceder por ssh
    ip firewall filter add chain=forward src-address=192.168.10.9  protocol=tcp dst-port=22 in-interface=LAN out-interface=DMZ action=accept
```
## Regla para permitir paquetes de conexion y relacionados ----->>> no la ponemos.

## Bloquear el resto de tráfico de la LAN a la DMZ
```mikrotik
ip firewall filter add chain=forward out-interface=DMZ in-interface=LAN action=reject
```
## Bloquear el acceso de la DMZ a la LAN dejando que si puedan salir a internet
```mikrotik
ip firewall filter add chain=forward out-interface=LAN in-inte`face=DMZ action=reject
```