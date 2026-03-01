El subnetting para tu proyecto usa 192.168.33.0/24 (VLANs, VLSM para 5 subredes con 9 PCs totales) y 10.4.33.0/24 (enlaces punto-a-punto, FLSM /30). Con 9 PCs, distribuí hosts lógicamente: VLAN NaranjaIZQ (3 PCs), VerdeIZQ (2), NaranjaDER (2), VerdeDER (1), ADMIN (1); cada subred suma +2 (network/broadcast). Esto optimiza espacio (/27 a /30). 

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