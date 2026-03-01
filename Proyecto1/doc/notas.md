Aquí tienes una lista clara y ordenada de pasos para completar tu proyecto de Redes de Computadoras 2 "Chapin Red". Como ya tienes la topología lista en Cisco Packet Tracer y X=33 (para redes 192.168.33.0/24 y 10.4.33.0/24), nos enfocamos en lo pendiente: subnetting, configuraciones clave y pruebas. Sigue este orden secuencial para evitar errores y maximizar puntos según la rúbrica (VTP, VLANs, agregación, enrutamiento, DHCP, ACLs, etc.). 

## Pasos Iniciales (Preparación)
- **Verifica la topología**: Asegúrate de que incluya 4 edificios con MSW Cisco 3650 (interconexión MAN con fibra Gigabit), edificio izquierdo (arquitectura 3 capas: Core/Distribución/Acceso con 3560 y 2960, 5 enlaces LACP), edificio derecho (3 enlaces PAgP), VLANs (Naranja, Verde, ADMIN distribuidas), servidores DHCP (uno por edificio) y dispositivos finales (PCs/laptops). Guarda el .pkt. 
- **Crea repositorio GitHub**: Usa nombre "REDES21S2026[tu_carnet]", carpeta "Proyecto1" para .pkt y README.md inicial. Sube la topología básica con commits diarios. 

## Subnetting y Direccionamiento (Base IP)
- Calcula subredes para 192.168.33.0/24 (5 VLANs: Naranja/Verde IZQ/DER + ADMIN) usando VLSM (considera hosts por VLAN: PCs + gateway + broadcast). Ejemplo: subred más grande para VLANs con más dispositivos.
- Subnetea 10.4.33.0/24 en /30 (FLSM) para enlaces punto-a-punto (entre MSW 3650, Core/Distribución, routers).
- Documenta en tabla (red base, máscara, hosts, gateway, rango usable, broadcast) para README.md. 

## Configuraciones Básicas (Capa 2)
- Configura VLANs (nros. lógicos ej. 10=NaranjaIZQ, 20=VerdeIZQ, 30=NaranjaDER, 40=VerdeDER, 99=ADMIN) con nombres "VLAN[Color][Edificio][Carnet]".
- Implementa VTP: elige 1 switch como Server (dominio/contraseña propia), resto Clients. Verifica propagación con "show vlan brief". 
- Configura trunks (802.1Q) entre switches/routers donde sea necesario.

## Agregación de Enlaces
- Edificio izquierdo: 5 enlaces LACP (modo active/passive) en port-channel capa 2 (entre Access y Distribución/Core).
- Edificio derecho: 3 enlaces PAgP (desirable/auto) en port-channel capa 2 (similar).
- Prueba tolerancia: deshabilita 1 puerto por bundle, verifica sin pérdida de paquetes (ping continuo). 

## Enrutamiento Dinámico
- Como carnet termina en 33 (impar): usa EIGRP para todo (inter-edificios MAN y inter-VLAN en MSW multicapa).
- Configura autonomous systems iguales, anuncia redes/VLANs. Verifica con "show ip route" y adyacencias. 

## Servicios DHCP
- Servidor DHCP izquierdo: pools para VLANs Naranja/Verde IZQ (rangos de tu subnetting, default-gateway, DNS ficticio).
- Servidor DHCP derecho: igual para DER.
- En routers/interfaces VLAN: "ip helper-address [IP servidor DHCP]".
- Configura PCs/laptops en DHCP (no estático). Verifica "ipconfig /renew". 

## Seguridad ACLs
- Crea ACLs extendidas (nros. 100-199):
  - VLAN Naranja: permit intra-Naranja (mismo edificio y entre), deny a Verde/ADMIN.
  - VLAN Verde: permit intra-Verde, deny a Naranja/ADMIN.
  - VLAN ADMIN: permit out a todas, deny in desde otras (unidireccional).
- Aplícalas en interfaces relevantes (in/out en routers/MSW). Prueba con ping: éxito donde debe, fail donde no. 
- Configura STP en todos capa 2 para anti-bucles.

## Pruebas y Verificación
- Prueba conectividad: ping intra-VLAN (ok), inter-VLAN permitida (ok), bloqueada (fail), ADMIN out (ok)/in (fail), DHCP auto-asignación, fallos en bundles.
- Capturas: topología, "show run" relevantes, tablas rutas, ACL hits, VLANs, port-channels. 

## Finalización y Documentación
- Crea README.md: topología (captura), subnetting (tablas), comandos clave (VTP, LACP/PAgP, EIGRP, DHCP, ACLs con explicaciones), pruebas.
- Sube todo a GitHub, .pkt funcional. Prepara para eval oral (explica decisiones). 

Sigue estos pasos en orden; cada uno desbloquea el siguiente. Una vez listos los comandos/configs, pásamelos para generar el .md formateado. ¡Avanza paso a paso para no perder puntos en penalizaciones! 