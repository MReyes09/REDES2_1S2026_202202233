# DOCUMENTACION

## Tabla Subredes VLANs (192.168.33.0/24 VLSM)
Asigna gateways en primera usable (SVI o router-on-stick). Usa rangos para PCs/DHCP.

| VLAN | Subred | Máscara | Gateway | Rango Usable PCs | Broadcast | Hosts PCs |
|------|--------|---------|---------|------------------|-----------|-----------|
| 1 (NaranjaIZQ) | 192.168.33.0/27 | 255.255.255.224 | 192.168.33.1 | 192.168.33.2 - .30 | 192.168.33.31 | 3   |
| 2 (VerdeIZQ) | 192.168.33.32/28 | 255.255.255.240 | 192.168.33.33 | 192.168.33.34 - .46 | 192.168.33.47 | 2   |
| 3 (NaranjaDER) | 192.168.33.48/28 | 255.255.255.240 | 192.168.33.49 | 192.168.33.50 - .62 | 192.168.33.63 | 2   |
| 4 (VerdeDER) | 192.168.33.64/30 | 255.255.255.252 | 192.168.33.65 | 192.168.33.66 - .69 | 192.168.33.71 | 1   |
| 5 (ADMIN) | 192.168.33.72/30 | 255.255.255.252 | 192.168.33.73 | 192.168.33.74 - .77 | 192.168.33.79 | 1   |

Queda espacio libre: 192.168.33.80/24 en adelante para expansiones. 

## Tabla Enlaces Punto-a-Punto (10.4.33.0/24 FLSM /30)
Para ~20 enlaces (ej. MAN entre 4 MSW3650: 6 enlaces; Core-Distribución IZQ: 2; inter-VLAN routers; etc.). Cada /30: End A (.1), End B (.2).

| Enlace # | Subred | End A | End B |
|----------|--------|-------|-------|
| 1 (ej. MAN Edif1-2) | 10.4.33.0/30 | 10.4.33.1 | 10.4.33.2   |
| 2 | 10.4.33.4/30 | 10.4.33.5 | 10.4.33.6   |
| 3 | 10.4.33.8/30 | 10.4.33.9 | 10.4.33.10   |
| ... | ... | ... | ... |
| 20 | 10.4.33.76/30 | 10.4.33.77 | 10.4.33.78   |

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

