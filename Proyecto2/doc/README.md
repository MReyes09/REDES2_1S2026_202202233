<div align="center">

UNIVERSIDAD SAN CARLOS DE GUATEMALA  
FACULTAD DE INGENIERÍA  
LABORATORIO DE REDES DE COMPUTADORAS 2  
SECCIÓN N  

**Documentación de configuración de red**  
(VLANs, enlaces punto-a-punto, EtherChannel, EIGRP, DHCP, ACLs)

**Estudiante:** Matthew Emmanuel Reyes Melgar  
**Carné:** 202202233  

Guatemala — 22 Abril 2026

</div>

---

<H1> Índice </H1>

- [Topología](#topología)
  - [TELCOM UNO](#telcom-uno)
  - [Redes Nacionales](#redes-nacionales)
  - [Conexiones Futuras](#conexiones-futuras)
  - [Vista General](#vista-general)
- [Tabla de Subredes](#tabla-de-subredes)
  - [Tabla de relacion VLAN - NOMBRE](#tabla-de-relacion-vlan---nombre)
  - [Tabla ISP 1](#tabla-isp-1)
    - [Tabla Port Channel](#tabla-port-channel)
    - [Tabla Punto a Punto](#tabla-punto-a-punto)
  - [Tabla ISP 2](#tabla-isp-2)
    - [Tabla de Port Channel](#tabla-de-port-channel)
    - [Tabla IPS Punto a Punto](#tabla-ips-punto-a-punto)
  - [Tabla ISP 3](#tabla-isp-3)
    - [Tabla Port Channel](#tabla-port-channel-1)
    - [Tabla Punto a Punto](#tabla-punto-a-punto-1)
  - [Tabla Conexion Central ISPs](#tabla-conexion-central-isps)
    - [Tabla Punto a Punto](#tabla-punto-a-punto-2)
- [Configuraciones](#configuraciones)
  - [ISP1 Telecom Uno](#isp1-telecom-uno)
    - [VLANS](#vlans)
      - [Switch 0](#switch-0)
      - [Switch 1](#switch-1)
    - [OSPF](#ospf)
      - [Router 0](#router-0)
      - [Multilayer Switch 4](#multilayer-switch-4)
      - [Router 1](#router-1)
      - [Multilayer Switch 5](#multilayer-switch-5)
      - [Multilayer Switch 3](#multilayer-switch-3)
    - [DHCP](#dhcp)
      - [R0](#r0)
      - [R1](#r1)
    - [ACL](#acl)
      - [R0](#r0-1)
    - [Servers](#servers)
      - [DNS](#dns)
      - [HTTP](#http)
  - [ISP2 Redes Nacionales](#isp2-redes-nacionales)
    - [VLANS](#vlans-1)
      - [Switch 2](#switch-2)
    - [OSPF](#ospf-1)
      - [R2](#r2)
      - [R3](#r3)
      - [Multilayer Switch 6](#multilayer-switch-6)
      - [Multilayer Switch 7](#multilayer-switch-7)
      - [Multilayer Switch 8](#multilayer-switch-8)
    - [DHCP](#dhcp-1)
      - [R2](#r2-1)
      - [R3](#r3-1)
    - [HSRP](#hsrp)
      - [R2 y R3](#r2-y-r3)
  - [ISP3 Conexiones Futuras](#isp3-conexiones-futuras)
    - [VLANS](#vlans-2)
      - [Multilayer Switch 12](#multilayer-switch-12)
      - [Multilayer Switch 10](#multilayer-switch-10)
      - [Multilayer Switch 9](#multilayer-switch-9)
      - [Multilayer Switch 13](#multilayer-switch-13)
    - [EIGRP](#eigrp)
      - [Multilayer Switch 11](#multilayer-switch-11)
      - [Multilayer Switch 10](#multilayer-switch-10-1)
      - [Multilayer Switch 9](#multilayer-switch-9-1)
      - [Multilayer Switch 13](#multilayer-switch-13-1)
      - [Multilayer Switch 12](#multilayer-switch-12-1)
    - [DHCP](#dhcp-2)
      - [Multilayer 9 y 13](#multilayer-9-y-13)
      - [Multilayer 10 y 12](#multilayer-10-y-12)
    - [ACLS](#acls)
      - [Multilayer Switch 9 y 13](#multilayer-switch-9-y-13)
  - [Interconexion ISPs](#interconexion-isps)
    - [Multilayer Switch 3](#multilayer-switch-3-1)
    - [Multilayer Switch 7](#multilayer-switch-7-1)
    - [Multilayer Switch 0](#multilayer-switch-0)
    - [Multilayer Switch 1](#multilayer-switch-1)
    - [Multilayer Switch 11](#multilayer-switch-11-1)
    - [Multilayer Switch 2](#multilayer-switch-2)

---

# Topología

## TELCOM UNO
![Telcom uno](../img/TELCOM_UNO.png)

## Redes Nacionales
![Redes Nacionales](../img/REDES_NACIONALES.png)

## Conexiones Futuras
![Conexiones Futuras](../img/CONEXIIONES_FUTURAS.png)

## Vista General
![Vista General](../img/VISTA_GENERAL.png)

# Tabla de Subredes
Asigna gateways en primera usable (SVI o router-on-stick). Usa rangos para PCs/DHCP.

## Tabla de relacion VLAN - NOMBRE

| VLAN | NOMBRE |
|------|--------|
|  10  | CLIENTE |
|  20  | ADMINISTRACION |
|  30  | VENTAS |
|  40  | FACTURACION |
|  50  | SOPORTE |
|  60  | SEGURIDAD |

## Tabla ISP 1

La red utilizada para esta red es 172.16.13.0/24,

| VLAN | Subred           | Máscara         | Gateway       | PCs (usable) |
| ---- | ---------------- | --------------- | ------------- | ------------------ |
|  10  | 172.16.13.0/28  | 255.255.255.240 | 172.16.13.1  | .2 a .14            |
|  20  | 172.16.13.16/28  | 255.255.255.240 | 172.16.13.17  | .18 a .30            |

### Tabla Port Channel
| MS | No | Puertos |
|----|----|---------|
| MS4 | 1 | fa0/3-4 |
| MS5 | 2 | fa0/5-7 |

### Tabla Punto a Punto

| No. | Subred | Dispositivo | Puerto | ip | Dispositivo | Puerto | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 172.16.13.0/28 | R0 | fa1/0 | 172.16.13.1 | | | |
| 2. | 172.16.13.32/30 | R0 | fa0/0 | 172.16.13.33 | MS4 | fa0/1 | 172.16.13.34 |
| 3. | 172.16.13.16/28 | R1 | fa1/0 | 172.16.13.17 | | | |
| 2. | 172.16.13.40/30 | R1 | fa0/0 | 172.16.13.41 | MS5 | fa0/1 | 172.16.13.42 |

| No. | Subred | Dispositivo | GROUP | ip | Dispositivo | GROUP | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 172.16.13.36/30 | MS4 | 1 | 172.16.13.37 | MS3 | 1 | 172.16.13.38 |
| 2. | 172.16.13.44/30 | MS5 | 2 | 172.16.13.45 | MS3 | 2 | 172.16.13.46 |

## Tabla ISP 2

La red utilizada para esta red es 172.16.23.0/24,

| VLAN | Subred           | Máscara         | Gateway       | PCs (usable) |
| ---- | ---------------- | --------------- | ------------- | ------------------ |
|  30  | 172.16.23.0/28  | 255.255.255.240 | 172.16.23.1  | .2 a .14 |
|  40  | 172.16.23.16/28  | 255.255.255.240 | 172.16.23.17  | .18 a .30 |

### Tabla de Port Channel
| MS | No | Puertos |
|----|----|---------|
| MS8 | 1 | fa0/3-5 |
| MS8 | 2 | fa0/6-8 |
| MS6 | 1 | fa0/3-5 |
| MS6 | 3 | fa0/22-24 |
| MS7 | 2 | fa0/6-8 |
| MS7 | 3 | fa0/22-24 |


### Tabla IPS Punto a Punto

| No. | Subred | Dispositivo | Puerto | ip | Dispositivo | Puerto | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 172.16.23.0/28 | R2-R3 | fa0/0 | 172.16.23.1 | | | |
| 2. | 172.16.23.16/28 | R2-R3 | fa0/0 | 172.16.23.17 | | | |
| 3. | 172.16.23.32/30 | R2 | fa1/0 | 172.16.23.33 | MS8 | fa0/1 | 172.16.23.34 |
| 4. | 172.16.23.36/30 | R3 | fa1/0 | 172.16.23.37 | MS8 | fa0/2 | 172.16.23.38 |

| No. | Subred | Dispositivo | GROUP | ip | Dispositivo | GROUP | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 172.16.23.40/30 | MS8 | 1 | 172.16.23.42 | MS6 | 1 | 172.16.23.41 |
| 2. | 172.16.23.44/30 | MS8 | 2 | 172.16.23.45 | MS7 | 2 | 172.16.23.46 |
| 3. | 172.16.23.48/30 | MS6 | 3 | 172.16.23.49 | MS7 | 3 | 172.16.23.50 |

## Tabla ISP 3

La red utilizada para esta red es 172.16.33.0/24

| VLAN | Subred           | Máscara         | Gateway       | PCs (usable) |
| ---- | ---------------- | --------------- | ------------- | ------------------ |
|  50  | 172.16.33.0/28  | 255.255.255.240 | 172.16.33.1  | .2 a .14 |
|  60  | 172.16.33.16/28  | 255.255.255.240 | 172.16.33.17  | .18 a .30 |

| Dispositivo  | Subred          | Máscara         | Gateway (SVI) | Hosts usables |
| ------------ | --------------- | --------------- | ------------- | ------------- |
| MS12         | 172.16.33.0/29  | 255.255.255.248 | 172.16.33.1   | .2 a .6       |
| MS10         | 172.16.33.8/29  | 255.255.255.248 | 172.16.33.9   | .10 a .14     |
| WR0          | 172.16.33.16/29 | 255.255.255.248 | 172.16.33.17  | .18 a .22     |

| Dispositivo | Subred          | Máscara         | Gateway (SVI) | Hosts usables |
| ----------- | --------------- | --------------- | ------------- | ------------- |
| MS9         | 172.16.33.16/29 | 255.255.255.248 | 172.16.33.17  | .18 a .22     |
| MS13        | 172.16.33.24/29 | 255.255.255.248 | 172.16.33.25  | .26 a .30     |

### Tabla Port Channel
| MS | No | Puertos |
|----|----|---------|
| MS11 | 1 | fa0/3-5 |
| MS11 | 2 | fa0/6-8 |
| MS13 | 1 | fa0/3-5 |
| MS12 | 2 | fa0/6-8 |

### Tabla Punto a Punto

| No. | Subred | Dispositivo | Puerto | ip | Dispositivo | Puerto | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 172.16.33.32/28| MS11 | fa0/1 | 172.16.33.33 | MS10 | fa0/1 | 172.16.33.34 |
| 2. | 172.16.33.36/28| MS11 | fa0/2 | 172.16.33.37 | MS9 | fa0/2 | 172.16.33.38 |
| 3. | 172.16.33.40/28| MS11 | fa0/9 | 172.16.33.41 | WR0 | Internet | 172.16.33.42 |

| No. | Subred | Dispositivo | GROUP | ip | Dispositivo | GROUP | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 172.16.33.44/30 | MS11 | 1 | 172.16.33.45 | MS13 | 1 | 172.16.33.46 |
| 2. | 172.16.33.48/30 | MS11 | 2 | 172.16.33.49 | MS12 | 2 | 172.16.33.50 |

## Tabla Conexion Central ISPs

### Tabla Punto a Punto

| No. | Subred | Dispositivo | Puerto | ip | Dispositivo | Puerto | ip |
|-----|--------|-------------|--------|----|-------------|--------|----|
| 1. | 192.168.33.0/30 | MS7 | G10/1 | 192.168.33.1 | MS1 | Gi1/0/1 | 192.168.33.2 |
| 2. | 192.168.33.4/30 | MS3 | Gi0/1 | 192.168.33.5 | MS0 | Gi1/0/1 | 192.168.33.6 |
| 3. | 192.168.33.8/30 | MS1 | Gi1/1/2 | 192.168.33.9 | MS0 | Gi1/1/1 | 192.168.33.10 |
| 4. | 192.168.33.12/30 | MS2 | Gi1/1/2 | 192.168.33.13 | MS0 | Gi1/1/2 | 192.168.33.14 |
| 5. | 192.168.33.16/30 | MS2 | Gi1/1/1 | 192.168.33.17 | MS1 | Gi1/1/1 | 192.168.33.18 |
| 4. | 192.168.33.20/30 | MS2 | Gi1/0/1 | 192.168.33.21 | MS11 | Gi0/1 | 192.168.33.22 |

# Configuraciones

## ISP1 Telecom Uno

### VLANS

#### Switch 0
```bash
enable
conf t
hostname SW0

vlan 10
 name CLIENTE
exit

inter fa0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
exit

inter range fa0/2-5
 switchport mode access
 switchport access vlan 10
 no shutdown
end
wr
```

#### Switch 1
```bash
enable
conf t
hostname SW1

vlan 20
 name ADMINISTRACION
exit

inter fa0/1
 switchport mode trunk
 switchport trunk allowed vlan all
 no shutdown
exit

inter range fa0/2-4
 switchport mode access
 switchport access vlan 20
 no shutdown
end
wr
```

### OSPF
#### Router 0
```bash
enable
conf t
hostname R0

interface fa1/0
 no shutdown
exit

interface fa1/0.10
 encapsulation dot1Q 10
 ip address 172.16.13.1 255.255.255.240
exit

interf fa0/0
 ip add 172.16.13.33 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 1.1.1.1
 network 172.16.13.0 0.0.0.15 area 0
 network 172.16.13.32 0.0.0.3 area 0

end
wr
```

#### Multilayer Switch 4
```bash
enable
conf t
hostname MS4

ip routing

interf fa0/1
 no switchport
 ip add 172.16.13.34 255.255.255.252
 no shutdown
exit

interface range fa0/3 - 4
 channel-protocol lacp
 channel-group 1 mode active
 no switchport
exit
interface port-channel 1
 no switchport
 description LACP-MS4-a-MS3
 ip add 172.16.13.37 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 2.2.2.2
 network 172.16.13.32 0.0.0.15 area 0
 network 172.16.13.36 0.0.0.3 area 0
end
wr

```

#### Router 1
```bash
enable
conf t
hostname R1

interface fa1/0
 no shutdown
exit

interface fa1/0.20
 encapsulation dot1Q 20
 ip address 172.16.13.17 255.255.255.240
exit

interf fa0/0
 ip add 172.16.13.41 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 3.3.3.3
 network 172.16.13.16 0.0.0.15 area 0
 network 172.16.13.40 0.0.0.3 area 0

end
wr
```

#### Multilayer Switch 5
```bash
enable
conf t
hostname MS5

ip routing

interf fa0/1
 no switchport
 ip add 172.16.13.42 255.255.255.252
 no shutdown
exit

interface range fa0/5 - 7
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit
interface port-channel 2
 no switchport
 description LACP-MS5-a-MS3
 ip add 172.16.13.45 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 4.4.4.4
 network 172.16.13.40 0.0.0.3 area 0
 network 172.16.13.44 0.0.0.3 area 0
end
wr

```

#### Multilayer Switch 3
```bash
enable
conf t
hostname MS3

ip routing

interface range fa0/2 - 4
 channel-protocol lacp
 channel-group 1 mode active
 no switchport
exit
interface port-channel 1
 no switchport
 description LACP-MS4-a-MS3
 ip add 172.16.13.38 255.255.255.252
 no shutdown
exit

interface range fa0/5 - 7
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit
interface port-channel 2
 no switchport
 description LACP-MS5-a-MS3
 ip add 172.16.13.46 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 5.5.5.5
 network 172.16.13.36 0.0.0.3 area 0
 network 172.16.13.44 0.0.0.3 area 0
end 
wr

```

### DHCP

#### R0
```bash
conf t
interface fa1/0.10
 ip helper-address 172.16.23.18
end
wr
```

#### R1
```bash
conf t
interface fa1/0.20
 ip helper-address 172.16.23.18
end
wr
```

### ACL

#### R0
```bash
conf t
no ip access-list extended VLAN10_ACL

ip access-list extended VLAN10_ACL
 permit udp any any eq 67
 permit udp any any eq 68
 permit ip any host 172.16.23.18
 permit ip host 172.16.23.18 any
 permit ip any host 172.16.13.18
 permit ip host 172.16.13.18 any
 permit ospf any any
 permit ip 172.16.13.0 0.0.0.15 172.16.23.0 0.0.0.15
 permit ip 172.16.23.0 0.0.0.15 172.16.13.0 0.0.0.15
 permit ip 172.16.13.0 0.0.0.15 172.16.33.0 0.0.0.7
 permit ip 172.16.13.0 0.0.0.15 172.16.33.8 0.0.0.7
 permit ip 172.16.13.0 0.0.0.15 172.16.33.16 0.0.0.7
 permit ip 172.16.13.0 0.0.0.15 172.16.33.24 0.0.0.7
 deny ip any any

interface fa1/0.10
 ip access-group VLAN10_ACL in
exit

no ip access-list extended VLAN10_OUT_ACL

ip access-list extended VLAN10_OUT_ACL
 remark DHCP y DNS siempre pasan
 permit udp any any eq 67
 permit udp any any eq 68
 permit ip host 172.16.23.18 any
 permit ip host 172.16.13.18 any
 remark VENTAS -> CLIENTE
 permit ip 172.16.23.0 0.0.0.15 172.16.13.0 0.0.0.15
 remark SOPORTE -> CLIENTE
 permit ip 172.16.33.0 0.0.0.7 172.16.13.0 0.0.0.15
 permit ip 172.16.33.8 0.0.0.7 172.16.13.0 0.0.0.15
 permit ip 172.16.33.16 0.0.0.7 172.16.13.0 0.0.0.15
 remark SEGURIDAD -> CLIENTE
 permit ip 172.16.33.24 0.0.0.7 172.16.13.0 0.0.0.15
 remark Bloquear ADMIN y cualquier otro
 deny ip any any

interface fa1/0.10
 ip access-group VLAN10_OUT_ACL out
exit
end
wr
```

### Servers

#### DNS

| Parámetro        | Valor                                       |
|------------------|---------------------------------------------|
| Rol              | Servidor DNS central para toda la topología |
| IP Address       | 172.16.13.18 (ejemplo en VLAN 20)           |
| Subnet Mask      | 255.255.255.240 (/28)                       |
| Default Gateway  | 172.16.13.17                                |
| DNS Service      | On                                          |
| Registro A (Name)| www.proyecto2202202233.com                  |
| Registro A (IP)  | 172.16.13.19 (IP del servidor HTTP)         |
| Tipo de registro | A Record                                    |

#### HTTP

| Parámetro        | Valor                                       |
|------------------|---------------------------------------------|
| Rol              | Servidor HTTP (página del proyecto)         |
| IP Address       | 172.16.13.19                                |
| Subnet Mask      | 255.255.255.240 (/28)                       |
| Default Gateway  | 172.16.13.17                                |
| HTTP Service     | On                                          |
| HTTPS Service    | Opcional (On/Off)                           |
| Archivo servido  | index.html                                  |

**Contenido de `index.html`**

```html
<html>
<head>
  <title>Proyecto 2 - 202202233</title>
</head>
<body>
  <h1>Proyecto 2 - Redes de Computadoras 2</h1>
  <p>Estudiante: Matthew Emmanuel Reyes Melgar</p>
  <p>Carné: 202202233</p>
  <p>Curso: Laboratorio de Redes de Computadoras 2</p>
  <p>Descripción: Infraestructura de 3 ISP interconectados con BGP, OSPF/EIGRP, DNS, DHCP y HTTP.</p>
</body>
</html>
```

## ISP2 Redes Nacionales

### VLANS

#### Switch 2
```bash
enable
conf t
hostname SW2

vlan 30
 name VENTAS
exit

vlan 40
 name FACTURACION
exit

inter range fa0/1-3
 switchport mode access
 switchport access vlan 30
 no shutdown
exit

inter range fa0/4-6
 switchport mode access
 switchport access vlan 40
 no shutdown
exit

inter range fa0/7-8
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown
end
wr
```

### OSPF

#### R2
```bash
enable
conf t
hostname R2

interface fa1/0
 no shutdown
exit

interface fa0/0
 no shutdown
exit

interface fa0/0.30
 encapsulation dot1Q 30
 ip address 172.16.23.1 255.255.255.240
exit

interf fa0/0.40
 encapsulation dot1Q 40
 ip add 172.16.23.17 255.255.255.240
exit

interf fa1/0
 ip add 172.16.23.33 255.255.255.252
exit

router ospf 1
 router-id 6.6.6.6
 network 172.16.23.0 0.0.0.15 area 0
 network 172.16.23.16 0.0.0.15 area 0
 network 172.16.23.32 0.0.0.3 area 0

end
wr
```

#### R3
```bash
enable
conf t
hostname R3

interface fa1/0
 no shutdown
exit

interface fa0/0
 no shutdown
exit

interface fa0/0.30
 encapsulation dot1Q 30
 ip address 172.16.23.1 255.255.255.240
exit

interf fa0/0.40
 encapsulation dot1Q 40
 ip add 172.16.23.17 255.255.255.240
exit

interf fa1/0
 ip add 172.16.23.37 255.255.255.252
exit

router ospf 1
 router-id 7.7.7.7
 network 172.16.23.0 0.0.0.15 area 0
 network 172.16.23.16 0.0.0.15 area 0
 network 172.16.23.36 0.0.0.3 area 0

end
wr
```

#### Multilayer Switch 6
```bash
enable
conf t
hostname MS6

ip routing

interface range fa0/3 - 5
 channel-protocol lacp
 channel-group 1 mode active
 no switchport
exit

interface port-channel 1
 no switchport
 ip address 172.16.23.41 255.255.255.252
 no shutdown
exit

interface range fa0/22 - 24
 no switchport
 channel-protocol lacp
 channel-group 3 mode active
exit

interface port-channel 3
 no switchport
 ip address 172.16.23.49 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 9.9.9.9
 network 172.16.23.40 0.0.0.3 area 0
 network 172.16.23.48 0.0.0.3 area 0
exit

end
wr
```

#### Multilayer Switch 7
```bash
enable
conf t
hostname MS7

ip routing

interface range fa0/6 - 8
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit

interface port-channel 2
 no switchport
 ip address 172.16.23.46 255.255.255.252
 no shutdown
exit

interface range fa0/22 - 24
 channel-protocol lacp
 channel-group 3 mode active
 no switchport
exit

interface port-channel 3
 no switchport
 ip address 172.16.23.50 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 10.10.10.10
 network 172.16.23.44 0.0.0.3 area 0
 network 172.16.23.48 0.0.0.3 area 0
exit

end
wr
```

#### Multilayer Switch 8
```bash
enable
conf t
hostname MS8

ip routing

interface fa0/1
 no switchport
 ip address 172.16.23.34 255.255.255.252
 no shutdown
exit

interface fa0/2
 no switchport
 ip address 172.16.23.38 255.255.255.252
 no shutdown
exit

interface range fa0/3 - 5
 channel-protocol lacp
 channel-group 1 mode active
 no switchport
exit

interface port-channel 1
 no switchport
 ip address 172.16.23.42 255.255.255.252
 no shutdown
exit

interface range fa0/6 - 8
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit

interface port-channel 2
 no switchport
 ip address 172.16.23.45 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 8.8.8.8
 network 172.16.23.32 0.0.0.3 area 0
 network 172.16.23.36 0.0.0.3 area 0
 network 172.16.23.40 0.0.0.3 area 0
 network 172.16.23.44 0.0.0.3 area 0
exit

end
wr
```


### DHCP

#### R2
```bash
conf t
interface fa0/0.40
 ip helper-address 172.16.23.18
exit

interface fa0/0.30
 ip helper-address 172.16.23.18
end
wr
```

#### R3
```bash
conf t
interface fa0/0.40
 ip helper-address 172.16.23.18
exit

interface fa0/0.30
 ip helper-address 172.16.23.18
end
wr
```


### HSRP

#### R2 y R3
```bash
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 172.16.23.2 255.255.255.240
 ip helper-address 172.16.23.18
 ip access-group VLAN30_ACL in
 standby version 2
 standby 30 ip 172.16.23.1
 standby 30 priority 120
 standby 30 preempt
!
interface FastEthernet0/0.40
 encapsulation dot1Q 40
 ip address 172.16.23.19 255.255.255.240
 ip helper-address 172.16.23.18
 ip access-group VLAN40_ACL in
 standby version 2
 standby 40 ip 172.16.23.17
 standby 40 priority 120
 standby 40 preempt
!
```

## ISP3 Conexiones Futuras

### VLANS

#### Multilayer Switch 12
```bash
enable
conf t
hostname MS12

ip routing

vlan 50
 name SOPORTE
exit

interface fa0/24
 switchport mode access
 switchport access vlan 50
exit

interface vlan 50
 ip address 172.16.33.1 255.255.255.240
 no shutdown
exit

end
wr

```

#### Multilayer Switch 10
```bash

enable
conf t
hostname MS10

ip routing

vlan 50
 name SOPORTE
exit

interface fa0/24
 switchport mode access
 switchport access vlan 50
exit

interface vlan 50
 ip address 172.16.33.9 255.255.255.248
 no shutdown
exit

end
wr
```

#### Multilayer Switch 9
```bash
enable
conf t
hostname MS9

ip routing

vlan 60
 name SEGURIDAD
exit

interface fa0/24
 switchport mode access
 switchport access vlan 60
exit

interface vlan 60
 ip address 172.16.33.17 255.255.255.248
 no shutdown
exit

end
wr
```

#### Multilayer Switch 13
```bash
enable
conf t
hostname MS13

ip routing

vlan 60
 name SEGURIDAD
exit

interface fa0/24
 switchport mode access
 switchport access vlan 60
exit

interface vlan 60
 ip address 172.16.33.25 255.255.255.248
 no shutdown
exit

end
wr
```

### EIGRP

#### Multilayer Switch 11
```bash
interf fa0/1
 no switchport
 ip add 172.16.33.33 255.255.255.252
 no shutdown
exit

interf fa0/2
 no switchport
 ip add 172.16.33.37 255.255.255.252
 no shutdown
exit

interf fa0/9
 no switchport
 ip add 172.16.33.41 255.255.255.252
 no shutdown
exit

interface range fa0/3-5
 channel-protocol lacp
 channel-group 1 mode active
 no switchport
exit
interface port-channel 1
 no switchport
 ip add 172.16.33.45 255.255.255.252
 no shutdown
exit

interface range fa0/6-8
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit
interface port-channel 2
 no switchport
 ip add 172.16.33.49 255.255.255.252
 no shutdown
exit

router eigrp 1
 no auto-summary
 network 172.16.33.32 0.0.0.3
 network 172.16.33.36 0.0.0.3
 network 172.16.33.40 0.0.0.3
 network 172.16.33.44 0.0.0.3
 network 172.16.33.48 0.0.0.3
end
wr
```

#### Multilayer Switch 10
```bash

interface fa0/1
 no switchport
 ip address 172.16.33.34 255.255.255.252
 no shutdown
exit

router eigrp 1
 no auto-summary
 network 172.16.33.8 0.0.0.7
 network 172.16.33.32 0.0.0.3
end
wr
```

#### Multilayer Switch 9
```bash
interface fa0/2
 no switchport
 ip address 172.16.33.38 255.255.255.252
 no shutdown
exit

router eigrp 1
 no auto-summary
 network 172.16.33.16 0.0.0.7
 network 172.16.33.36 0.0.0.3
end
wr
```

#### Multilayer Switch 13
```bash
interface range fa0/3-5
 channel-protocol lacp
 channel-group 1 mode active
 no switchport
exit
interface port-channel 1
 no switchport
 ip address 172.16.33.46 255.255.255.252
 no shutdown
exit

router eigrp 1
 no auto-summary
 network 172.16.33.24 0.0.0.7
 network 172.16.33.44 0.0.0.3
end
wr
```

#### Multilayer Switch 12
```bash

interface range fa0/6-8
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit
interface port-channel 2
 no switchport
 ip address 172.16.33.50 255.255.255.252
 no shutdown
exit

router eigrp 1
 no auto-summary
 network 172.16.33.0 0.0.0.7
 network 172.16.33.48 0.0.0.3
end
wr
```

### DHCP

#### Multilayer 9 y 13
```bash
conf t
interface vlan 60
 ip helper-address 172.16.23.18
end
wr
```

#### Multilayer 10 y 12
```bash
conf t
interface vlan 50
 ip helper-address 172.16.23.18
end
wr
```

### ACLS

#### Multilayer Switch 9 y 13
```bash
conf t
no ip access-list extended VLAN60_ACL

ip access-list extended VLAN60_ACL
 remark DHCP y DNS siempre pasan hacia hosts SEGURIDAD
 permit udp any any eq 67
 permit udp any any eq 68
 permit ip host 172.16.23.18 any
 permit ip host 172.16.13.18 any
 remark Solo echo-reply (retorno de pings de SEGURIDAD)
 permit icmp any 172.16.33.16 0.0.0.7 echo-reply
 permit icmp any 172.16.33.24 0.0.0.7 echo-reply
 remark TCP established (retorno de conexiones de SEGURIDAD)
 permit tcp any 172.16.33.16 0.0.0.7 established
 permit tcp any 172.16.33.24 0.0.0.7 established
 remark Bloquear todo lo demas
 deny ip any any

interface vlan 60
 no ip access-group VLAN60_ACL in
 ip access-group VLAN60_ACL out
exit
end
wr
```

## Interconexion ISPs

### Multilayer Switch 3
```bash
enable
conf t

interface GigabitEthernet0/1
 no switchport
 ip address 192.168.33.5 255.255.255.252
 no shutdown
exit

router bgp 65004
 bgp log-neighbor-changes
 ! Redes internas de ISP1 que MS3 anuncia al core
 network 172.16.13.0 mask 255.255.255.0

 ! Vecino BGP hacia MS0 (core – AS 65002)
 neighbor 192.168.33.6 remote-as 65002
exit

router ospf 1
 router-id 5.5.5.5
 log-adjacency-changes
 ! Backbones internos de ISP1
 network 172.16.13.36 0.0.0.3 area 0
 network 172.16.13.44 0.0.0.3 area 0

 ! Redistribuir las rutas BGP (core/otros ISPs) hacia el OSPF interno
 redistribute bgp 65004 subnets
exit

end
wr
```

### Multilayer Switch 7
```bash
enable
conf t
hostname MS7

ip routing

interface GigabitEthernet0/1
 no switchport
 ip address 192.168.33.1 255.255.255.252
 no shutdown
exit

interface range FastEthernet0/6 - 8
 channel-protocol lacp
 channel-group 2 mode active
 no switchport
exit

interface port-channel 2
 no switchport
 ip address 172.16.23.46 255.255.255.252
 no shutdown
exit

interface range FastEthernet0/22 - 24
 channel-protocol lacp
 channel-group 3 mode active
 no switchport
exit

interface port-channel 3
 no switchport
 ip address 172.16.23.50 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 10.10.10.10
 log-adjacency-changes
 network 172.16.23.44 0.0.0.3 area 0
 network 172.16.23.48 0.0.0.3 area 0

 ! Redistribuir rutas BGP del core hacia el OSPF interno (ISP2)
 redistribute bgp 65001 subnets
exit

router bgp 65001
 bgp log-neighbor-changes

 ! Redes internas de ISP2 que MS7 anuncia al core (vistas desde MS7)
 network 172.16.23.0 mask 255.255.255.0

 ! Vecino BGP hacia MS1 (core – AS 65003)
 neighbor 192.168.33.2 remote-as 65003
exit

! Ruta por defecto desde ISP2 hacia el core
ip route 0.0.0.0 0.0.0.0 192.168.33.2

end
wr
```

### Multilayer Switch 0
```bash
enable
conf t

interface GigabitEthernet1/0/1
 no switchport
 ip address 192.168.33.6 255.255.255.252
 no shutdown
exit

interface GigabitEthernet1/1/1
 no switchport
 ip address 192.168.33.10 255.255.255.252
 no shutdown
exit

interface GigabitEthernet1/1/2
 no switchport
 ip address 192.168.33.14 255.255.255.252
 no shutdown
exit

router bgp 65002
 bgp log-neighbor-changes
 ! Redes que anuncia MS0 en BGP
 network 172.16.13.0 mask 255.255.255.0
 network 172.16.23.0 mask 255.255.255.0
 network 192.168.33.4 mask 255.255.255.252
 network 192.168.33.8 mask 255.255.255.252

 ! Vecino hacia MS3 (ISP1 – AS 65004)
 neighbor 192.168.33.5 remote-as 65004

 ! Vecino hacia MS1 (core – AS 65003)
 neighbor 192.168.33.9 remote-as 65003

 neighbor 192.168.33.13 remote-as 65006

exit

! Ruta hacia la red interna de ISP1 (señala a MS3)
ip route 172.16.13.0 255.255.255.0 192.168.33.5

end
wr
```

### Multilayer Switch 1
```bash
enable
conf t
hostname MS1

interface GigabitEthernet1/0/1
 no switchport
 ip address 192.168.33.2 255.255.255.252
 no shutdown
exit

interface GigabitEthernet1/1/2
 no switchport
 ip address 192.168.33.9 255.255.255.252
 no shutdown
exit

interface GigabitEthernet1/1/1
 no switchport
 ip address 192.168.33.18 255.255.255.252
 no shutdown
exit

router bgp 65003
 bgp log-neighbor-changes

 ! Redes que anuncia MS1 en BGP (core / ISP2)
 network 172.16.23.0 mask 255.255.255.0
 network 192.168.33.8 mask 255.255.255.252

 ! Vecino hacia MS7 (frontera ISP2 – AS 65001)
 neighbor 192.168.33.1 remote-as 65001

 ! Vecino hacia MS0 (core – AS 65002)
 neighbor 192.168.33.10 remote-as 65002

 neighbor 192.168.33.17 remote-as 65006

exit

! Ruta hacia la red interna de ISP2 (señala a MS7)
ip route 172.16.23.0 255.255.255.0 192.168.33.1

end
wr
```

### Multilayer Switch 11
```bash
interf gi0/1
 no switchport
 ip add 192.168.33.22 255.255.255.252
 no shutdown
exit

! EIGRP interno ISP3
router eigrp 1
 no auto-summary
 network 172.16.33.32 0.0.0.3
 network 172.16.33.36 0.0.0.3
 network 172.16.33.40 0.0.0.3
 network 172.16.33.44 0.0.0.3
 network 172.16.33.48 0.0.0.3
exit

ip route 172.16.33.0 255.255.255.0 null0
! BGP en el border de ISP3
router bgp 65005
 bgp log-neighbor-changes

 ! redes internas de ISP3 que anuncia al core
 network 172.16.33.0 mask 255.255.255.0

 ! (opcional pero prolijo) anunciar también los enlaces al core para visibilidad completa
 network 192.168.33.20 mask 255.255.255.252

 ! vecino hacia MS2 (core – AS 65002)
 neighbor 192.168.33.21 remote-as 65002
exit

! Redistribuir las rutas BGP (otros ISPs/core) hacia el EIGRP interno
router eigrp 1
 redistribute bgp 65005 metric 10000 100 255 1 1500
exit

end
wr

```

### Multilayer Switch 2
```bash
enable
conf t
hostname MS2

ip routing

interface Gi1/1/2
 no switchport
 ip address 192.168.33.13 255.255.255.252
 no shutdown
exit

interface Gi1/1/1
 no switchport
 ip address 192.168.33.17 255.255.255.252
 no shutdown
exit

interface Gi1/0/1
 no switchport
 ip address 192.168.33.21 255.255.255.252
 no shutdown
exit

router bgp 65006
 bgp log-neighbor-changes

 ! redes que MS2 anuncia en el core
 ! (tránsito entre ISPs; puedes anunciar solo los /30 de core)
 network 192.168.33.12 mask 255.255.255.252
 network 192.168.33.16 mask 255.255.255.252
 network 192.168.33.20 mask 255.255.255.252

 ! vecino hacia MS0 (ya existe en MS0)
 neighbor 192.168.33.14 remote-as 65002

 ! vecino hacia MS1 (core – AS 65003)
 neighbor 192.168.33.18 remote-as 65003

 ! vecino hacia MS11 (ISP3 – AS 65005)
 neighbor 192.168.33.22 remote-as 65005
exit

end
wr
```