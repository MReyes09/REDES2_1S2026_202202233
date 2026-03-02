# DOCUMENTACION

## Tabla Subredes VLANs (192.168.33.0/24 VLSM)
Asigna gateways en primera usable (SVI o router-on-stick). Usa rangos para PCs/DHCP.

| VLAN | Subred | Máscara | Gateway |
|------|--------|---------|---------|
| 10 | 192.168.33.0/29 | 255.255.255.248 | 192.168.33.1 |
| 20 | 192.168.33.4/29 | 255.255.255.248 | 192.168.33.5 |
| 30 | 192.168.33.8/29 | 255.255.255.248 | 192.168.33.9 |
| 40 | 192.168.33.12/29 | 255.255.255.248 | 192.168.33.13 |
| 99 | 192.168.33.16/29 | 255.255.255.248 | 192.168.33.17 |

Queda espacio libre: 192.168.33.80/24 en adelante para expansiones. 

## Tabla Enlaces Punto-a-Punto (10.4.33.0/24 FLSM /30)
Para ~20 enlaces (ej. MAN entre 4 MSW3650: 6 enlaces; Core-Distribución IZQ: 2; inter-VLAN routers; etc.). Cada /30: End A (.1), End B (.2).

10.4.33.0/30

#### Tabla para el edificio izquierdo
| No. | Subred | Switch1 | Puerto1 | ip | Switch2 | Puerto 2| ip |
|-----|--------|---------|---------|----|---------|---------|----|
| 1. | 10.4.33.0/30 | MS 1 | gi1/0/1 | 10.4.33.1 | MS6 | fa0/3 | 10.4.33.2 |
| 2. | 10.4.33.4/30 | MS 1 | gi1/0/2 | 10.4.33.5 | MS6 | fa0/4 | 10.4.33.6 |
| 3. | 10.4.33.8/30 | MS 1 | gi1/0/3 | 10.4.33.9 | MS6 | fa0/5 | 10.4.33.10 |
| 4. | 10.4.33.12/30 | MS 1 | gi1/0/4 | 10.4.33.13 | MS0 | fa0/6 | 10.4.33.14 |
| 5. | 10.4.33.16/30 | MS 1 | gi1/0/5 | 10.4.33.17 | MS0 | fa0/7 | 10.4.33.18 |
| 6. | 10.4.33.20/30 | MS 1 | gi1/0/6 | 10.4.33.21 | MS0 | fa0/8 | 10.4.33.22 |
| 7. | 10.4.33.24/30 | MS 0 | fa0/3 | 10.4.33.25 | MS6 | fa0/6 | 10.4.33.26 |
| 8. | 10.4.33.28/30 | MS 0 | fa0/4 | 10.4.33.29 | MS6 | fa0/7 | 10.4.33.30 |
| 9. | 10.4.33.32/30 | MS 0 | fa0/5 | 10.4.33.33 | MS6 | fa0/8 | 10.4.33.34 |

## Instrucciones de Uso
- Asigna VLANs: 10=NaranjaIZQ, 20=VerdeIZQ, 30=NaranjaDER, 40=VerdeDER, 99=ADMIN.
- En Packet Tracer: configura SVIs en MSW con gateway, pools DHCP con estos rangos (excluye gateway).
- Copia tablas a README.md con capturas. Ajusta distribución PCs si tu topología difiere (dime #PCs por VLAN exactos para recalcular). 

---

# Configuración de Switches Edificio Izquierdo

## Configuraciones básicas, creación y distribución de vlans
### Multislayer Switch 1

```bash
!Configuraciones comunes

enable
conf t
hostname MULT_SWITCH_1
banner motd #
*************************************************
* ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO *
*************************************************
#
!VLANS 

vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode server

vlan 10
 name VLAN_Naranja_EdificioIZQ_33
vlan 20
 name VLAN_Verde_EdificioIZQ_33
exit

interface range Gig1/0/1-6
switchport mode trunk
switchport trunk allowed vlan 10,20
exit

!STP
spanning-tree mode pvst

do wr
```
### Multislayer Switch 0
```bash
!Configuraciones comunes

enable
conf t
hostname MULT_SWITCH_0
banner motd #
*************************************************
* ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO *
*************************************************
#
vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode client

interface range Fa0/1-8
switchport mode trunk
switchport trunk allowed vlan 10,20
exit

!STP
spanning-tree mode pvst

do wr
```

### Multislayer Switch 6
```bash
!Configuraciones comunes

enable
conf t
hostname MULT_SWITCH_6
banner motd #
*************************************************
* ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO *
*************************************************
#
vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode client

interface range Fa0/1-8
switchport mode trunk
switchport trunk allowed vlan 10,20
exit

!STP
spanning-tree mode pvst

do wr

```

### Switch 0
```bash

enable
conf t
hostname SWITCH_0
banner motd #
*************************************************
*            BIENVENIDO AL SWITCH 0             *
*************************************************
#
vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode client

interface range Fa0/3-4
switchport mode trunk
switchport trunk allowed vlan 10,20
exit

interface Fa0/1
switchport mode access
switchport access vlan 10

interface Fa0/2
switchport mode access
switchport access vlan 20

!STP
spanning-tree mode pvst

do wr
```

### Switch 1
```bash

enable
conf t
hostname SWITCH_1
banner motd #
*************************************************
*            BIENVENIDO AL SWITCH 1             *
*************************************************
#
vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode client

interface range Fa0/3-4
switchport mode trunk
switchport trunk allowed vlan 10,20
exit

interface Fa0/1
switchport mode access
switchport access vlan 10

interface Fa0/2
switchport mode access
switchport access vlan 20

!STP
spanning-tree mode pvst

do wr
```

## Configuraciones de LACP

### Switch 0
```bash
enable
conf t
interface range fa0/3 - 4
 channel-group 1 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 1
 description LACP-SW0-a-MLS0
 switchport mode trunk
 switchport trunk allowed vlan 10,20
end
wr

```
### Switch 1
```bash
enable
conf t
interface range f0/3-4
channel-group 2 mode active
switchport mode trunk
switchport trunk allowed vlan 10,20
exit
interface port-channel 2
decription LACP-SW1-a-MLS6
switchport mode trunk
switchport trunk allowed vlan 10,20
end
wr
```


### Multislayer Switch 0
```bash
enable
conf t

interface range fa0/1 - 2
 channel-group 1 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 1
 description LACP-MLS0-a-SW0
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface range f0/3-5
 channel-group 3 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 exit
interface port-channel 3
 description LACP-MLS0-a-MLS6
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface range fa0/6-8
 channel-group 5 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 5
 description LACP-MLS0-a-MLS1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
end
wr


```

### Multislayer Switch 6
```bash
enable
conf t

interface range fa0/1 - 2
 channel-group 2 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 2
 description LACP-MLS6-a-SW1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface range f0/6-8
 channel-group 3 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 exit
interface port-channel 3
 description LACP-MLS6-a-MLS0
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface range f0/3-5
 channel-group 4 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 4
 description LACP-MLS6-a-MLS1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
end
wr
```

### Multislayer Switch 1
```bash
interface range Gig1/0/1-3
 channel-group 4 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 4
 description LACP-MLS1-a-MLS6
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

interface range Gig1/0/4-6
 channel-group 5 mode active
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
interface port-channel 5
 description LACP-MLS1-a-MLS0
 switchport mode trunk
 switchport trunk allowed vlan 10,20
end
wr
```

## Configuración EIGRIP

---
# Configuración de Switches Edificio Derecho

## Configuraciones básicas, creación y distribución de vlans

### Multilayer Switch  11

```bash

enable
conf t
hostname MULT_SWITCH_11
banner motd #
*************************************************
* ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO *
*************************************************
#
!VLANS 

vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode server

vlan 30
 name VLAN_Naranja_EdificioDER_33
vlan 40
 name VLAN_Verde_EdificioDER_33
exit

interface range Gig0/1-2
switchport mode trunk
switchport trunk allowed vlan 30,40
exit

interface range Fa0/1-2
switchport mode trunk
switchport trunk allowed vlan 30,40

!STP
spanning-tree mode pvst

do wr
```

## Switch 2
```bash

enable
conf t
hostname SWITCH_2
banner motd #
*************************************************
*            BIENVENIDO AL SWITCH 2             *
*************************************************
#
vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode client

interface range Fa0/3
switchport mode trunk
switchport trunk allowed vlan 30,40
exit

interface Fa0/1
switchport mode access
switchport access vlan 40

interface Fa0/2
switchport mode access
switchport access vlan 30

!STP
spanning-tree mode pvst

do wr
```

## Switch 3
```bash

enable
conf t
hostname SWITCH_3
banner motd #
*************************************************
*            BIENVENIDO AL SWITCH 3             *
*************************************************
#
vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode client

interface range Fa0/3
switchport mode trunk
switchport trunk allowed vlan 30,40
exit

interface Fa0/1
switchport mode access
switchport access vlan 40

interface Fa0/2
switchport mode access
switchport access vlan 30

!STP
spanning-tree mode pvst

do wr
```

