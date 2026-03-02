# DOCUMENTACION

## Tabla Subredes VLANs (192.188.33.0/24 VLSM)
Asigna gateways en primera usable (SVI o router-on-stick). Usa rangos para PCs/DHCP.

| VLAN | Subred           | Máscara         | Gateway       | PCs (.2-.6 usable) |
| ---- | ---------------- | --------------- | ------------- | ------------------ |
| 10   | 192.188.33.0/29  | 255.255.255.248 | 192.188.33.1  | .2 a .6            |
| 20   | 192.188.33.8/29  | 255.255.255.248 | 192.188.33.9  | .10 a .14          |
| 30   | 192.188.33.16/29 | 255.255.255.248 | 192.188.33.17 | .18 a .22          |
| 40   | 192.188.33.24/29 | 255.255.255.248 | 192.188.33.25 | .26 a .30          |
| 99   | 192.188.33.32/29 | 255.255.255.248 | 192.188.33.33 | .34 a .38          |

Queda espacio libre: 192.188.33.80/24 en adelante para expansiones. 

---
## Tabla Enlaces Punto-a-Punto (10.4.33.0/24 FLSM /30)
Para ~20 enlaces (ej. MAN entre 4 MSW3650: 6 enlaces; Core-Distribución IZQ: 2; inter-VLAN routers; etc.). Cada /30: End A (.1), End B (.2).

10.4.33.0/30

### Tabla port channel edificio izquierdo
| MS | No | Puertos |
|----|----|---------|
| MS1 | 4 | gi1/0/1-3 |
| MS1 | 5 | gi1/0/4-6 |
| MS0 | 1 | fa0/1-2 |
| MS0 | 3 | fa0/3-5 |
| MS0 | 5 | f0/6-8 |
| MS6 | 2 | fa0/1-2 |
| MS6 | 3 | fa0/6-8 |
| MS6 | 4 | f0/3-5 |

#### Tabla para el edificio izquierdo
| No. | Subred | Switch1 | No-Portchannel | ip | Switch2 | No-Portchannel | ip |
|-----|--------|---------|----------------|----|---------|----------------|----|
| 1. | 10.4.33.0/30 | MS6 | 4 | 10.4.33.1 | MS1 | 4 | 10.4.33.2 |
| 2. | 10.4.33.4/30 | MS0 | 5 | 10.4.33.5 | MS1 | 5 | 10.4.33.6 |

### Tabla para el edificio derecho
| No. | Subred | Switch1 | Puerto1 | ip | Switch2 | Puerto 2| ip |
|-----|--------|---------|---------|----|---------|---------|----|
| 1. | 10.4.33.36/30 | MS3 | Gi1/0/1 | 10.4.33.37 | MS9 | fa0/5 | 10.4.33.38 |
| 2. | 10.4.33.40/30 | MS3 | Gi1/0/2 | 10.4.33.41 | MS9 | fa0/6 | 10.4.33.42 |
| 3. | 10.4.33.44/30 | MS3 | Gi1/0/3 | 10.4.33.45 | MS9 | fa0/7 | 10.4.33.46 |
| 4. | 10.4.33.48/30 | MS9 | fa0/1 | 10.4.33.49 | R1 | fa0/0/0 | 10.4.33.50 |
| 5. | 10.4.33.52/30 | MS9 | fa0/2 | 10.4.33.53 | R1 | fa0/0/1 | 10.4.33.54 |
| 6. | 10.4.33.56/30 | MS9 | fa0/3 | 10.4.33.57 | R1 | fa0/0/2 | 10.4.33.58 |
| 7. | 10.4.33.60/30 | MS9 | fa0/4 | 10.4.33.61 | R1 | fa0/0/3 | 10.4.33.62 |
| 8. | 10.4.33.64/30 | R1 | Gi0/0 | 10.4.33.65 | MS11 | Gi0/1 | 10.4.33.66 |
| 9. | 10.4.33.68/30 | R1 | Gi0/1 | 10.4.33.69 | MS11 | Gi0/2 | 10.4.33.70 |

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
ip routing
hostname MULT_SWITCH_1
banner motd #
*************************************************
* ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO *
*************************************************
#

!STP
spanning-tree mode pvst

do wr
```
### Multislayer Switch 0
```bash
!Configuraciones comunes

enable
conf t
ip routing
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
ip routing

hostname MULT_SWITCH_6
banner motd #
*************************************************
* ACCESO RESTRINGIDO - SOLO PERSONAL AUTORIZADO *
*************************************************
#
vlan 10
 name VLAN_Naranja_EdificioIZQ_33
vlan 20
 name VLAN_Verde_EdificioIZQ_33
exit

vtp domain 202202233
vtp password 202202233
vtp version 2
vtp mode server

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

ip routing

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
 channel-protocol lacp
 channel-group 5 mode active
 no switchport
exit
interface port-channel 5
 description LACP-MLS0-a-MLS1
 ip address 10.4.33.5 255.255.255.252
 no shutdown
end
wr


```

### Multislayer Switch 6
```bash
enable
conf t

ip routing

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
 channel-protocol lacp
 channel-group 4 mode active
 no switchport
exit
interface port-channel 4
 description LACP-MLS6-a-MLS1
 ip address 10.4.33.1 255.255.255.252
end
wr
```

### Multislayer Switch 1
```bash
interface range Gig1/0/1-3
 channel-protocol lacp
 channel-group 4 mode active
 no switchport
exit
interface port-channel 4
 description LACP-MLS1-a-MLS6
 ip address 10.4.33.2 255.255.255.252
exit

interface range Gig1/0/4-6
 channel-protocol lacp
 channel-group 5 mode active
 no switchport
exit
interface port-channel 5
 description LACP-MLS1-a-MLS0
 ip address 10.4.33.6 255.255.255.252
end
wr
```

## Configuración EIGRP

### Multislayer Switch 6
```bash
interface vlan 10
 ip address 192.188.33.1 255.255.255.248
 no shutdown
interface vlan 20
 ip address 192.188.33.9 255.255.255.248
 no shutdown
exit

router eigrp 1
 network 192.188.33.0 0.0.0.255
 network 10.4.33.0 0.0.0.3
 no auto-summary
end 
wr
```

### Multislayer Switch 1
```bash

router eigrp 1
 network 10.4.33.0 0.0.0.3
 no auto-summary
end
wr
```

### Multislayer Switch 0
```bash

interface vlan 10
 ip address 192.188.33.1 255.255.255.248
 no shutdown
interface vlan 20
 ip address 192.188.33.9 255.255.255.248
 no shutdown
exit

router eigrp 1
 network 192.188.33.0 0.0.0.255
 network 10.4.33.0 0.0.0.3
 no auto-summary
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
ip routing
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
