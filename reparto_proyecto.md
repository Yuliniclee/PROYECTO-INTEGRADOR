# Reparto de Trabajo - Proyecto Integrador

**Curso:** Redes de Computadoras 2
**Universidad:** USAC - Facultad de Ingenieria
**Modalidad:** Pareja
**Integrante 1:** Carne 202300659
**Integrante 2:** Carne 202300673
**Valor de D (personalizacion):** 9 (ultimo digito del carne del Integrante 1)
**Archivo entregable:** `ProyectoIntegrador_202300659_202300673.pkt`

---

## 1. Acuerdos iniciales del proyecto

### 1.1 Tabla de direccionamiento consolidada (D = 9)

| Segmento | Red | Mascara | Dispositivo | Interfaz | IP |
|---|---|---|---|---|---|
| R-EDGE <-> ISP | 200.10.9.0 | /30 | R-EDGE | g0/0 | 200.10.9.1 |
| | | | ISP | g0/0 | 200.10.9.2 |
| R-EDGE <-> R-HQ1 | 10.9.2.0 | /30 | R-EDGE | g0/1 | 10.9.2.2 |
| | | | R-HQ1 | g0/0 | 10.9.2.1 |
| R-HQ1 <-> R-HQ2 | 10.9.1.0 | /30 | R-HQ1 | g0/1 | 10.9.1.1 |
| | | | R-HQ2 | g0/0 | 10.9.1.2 |
| R-HQ1 <-> R-BRANCH1 | 10.9.12.0 | /30 | R-HQ1 | g0/2 | 10.9.12.1 |
| | | | R-BRANCH1 | g0/0 | 10.9.12.2 |
| R-HQ2 <-> R-BRANCH2 | 10.9.23.0 | /30 | R-HQ2 | g0/1 | 10.9.23.1 |
| | | | R-BRANCH2 | g0/0 | 10.9.23.2 |
| R-HQ2 <-> R-DATACENTER (principal) | 10.9.30.0 | /30 | R-HQ2 | g0/2 | 10.9.30.1 |
| | | | R-DATACENTER | g0/0 | 10.9.30.2 |
| R-HQ1 <-> R-DATACENTER (redundante) | 10.9.40.0 | /30 | R-HQ1 | g0/3 | 10.9.40.1 |
| | | | R-DATACENTER | g0/1 | 10.9.40.2 |
| LAN-HQ1 | 192.168.109.0 | /24 | R-HQ1 | fa0/0 | 192.168.109.1 |
| | | | PC-HQ1 | NIC | 192.168.109.10 |
| LAN-HQ2 | 192.168.119.0 | /24 | R-HQ2 | fa0/0 | 192.168.119.1 |
| | | | PC-HQ2 | NIC | 192.168.119.10 |
| LAN-BRANCH1 | 192.168.9.0 | /24 | R-BRANCH1 | fa0/0 | 192.168.9.1 |
| | | | PC-B1 | NIC | 192.168.9.10 |
| LAN-BRANCH2 | 192.168.29.0 | /24 | R-BRANCH2 | fa0/0 | 192.168.29.1 |
| | | | PC-B2 | NIC | 192.168.29.10 |
| LAN-SERVIDORES | 172.16.9.0 | /24 | R-DATACENTER | fa0/0 | 172.16.9.1 |
| | | | SERVER | NIC | 172.16.9.10 |
| LAN-ADMIN | 172.20.9.0 | /24 | R-DATACENTER | fa0/1 | 172.20.9.1 |
| | | | PC-ADM | NIC | 172.20.9.10 |

### 1.2 Router IDs OSPF

| Router | Router ID | Rol |
|---|---|---|
| R-HQ1 | 1.1.1.1 | Backbone |
| R-HQ2 | 2.2.2.2 | Backbone |
| R-EDGE | 3.3.3.3 | Salida / Edge |
| R-BRANCH2 | 4.4.4.4 | Sucursal OSPF |
| R-DATACENTER | 5.5.5.5 | Servicios |

### 1.3 Mecanismo de integracion RIPv2 <-> OSPF

- Punto de union: R-HQ1.
- Mecanismo: redistribucion mutua (OSPF -> RIP y RIP -> OSPF) configurada unicamente en R-HQ1.
- Justificacion: el enunciado prohibe resolver la integracion con rutas estaticas individuales; la redistribucion mutua concentra el borde en un unico router, permite que las rutas aparezcan como `R` en el dominio de sucursal y como `O E2` en el dominio OSPF, y mantiene la coherencia administrativa.

---

## 2. Construccion de la topologia base

Esta tarea corresponde al **Integrante 1**. El resultado es el archivo `BASE.pkt` que servira como punto de partida para ambos integrantes.

### 2.1 Dispositivos a colocar

| Cantidad | Modelo | Nombres |
|---|---|---|
| 7 | Router 2911 | R-EDGE, R-HQ1, R-HQ2, R-BRANCH1, R-BRANCH2, R-DATACENTER, ISP |
| 6 | Switch 2960 | SW-HQ1, SW-HQ2, SW-BRANCH1, SW-BRANCH2, SW-SERVIDORES, SW-ADMIN |
| 5 | PC-PT | PC-HQ1, PC-HQ2, PC-B1, PC-B2, PC-ADM |
| 1 | Server-PT | SERVER |

### 2.2 Modulos a agregar

- En R-HQ1 y R-DATACENTER: agregar un modulo HWIC-1GE-SFP para habilitar una cuarta interfaz Gigabit (soporte del enlace redundante).
- En cada router: verificar que al menos exista una interfaz FastEthernet o Gigabit para la LAN; si el modelo 2911 no la tiene libre, agregar un NIM-ES2-4.

### 2.3 Pasos de cableado

Usar cable de cobre directo (icono de rayo automatico).

1. R-EDGE g0/0 <-> ISP g0/0
2. R-EDGE g0/1 <-> R-HQ1 g0/0
3. R-HQ1 g0/1 <-> R-HQ2 g0/0
4. R-HQ1 g0/2 <-> R-BRANCH1 g0/0
5. R-HQ2 g0/1 <-> R-BRANCH2 g0/0
6. R-HQ2 g0/2 <-> R-DATACENTER g0/0
7. R-HQ1 g0/3 <-> R-DATACENTER g0/1 (enlace redundante; agregar nota grafica "Enlace redundante" en color rojo)
8. R-HQ1 fa0/0 <-> SW-HQ1 fa0/1 <-> PC-HQ1 fa0
9. R-HQ2 fa0/0 <-> SW-HQ2 fa0/1 <-> PC-HQ2 fa0
10. R-BRANCH1 fa0/0 <-> SW-BRANCH1 fa0/1 <-> PC-B1 fa0
11. R-BRANCH2 fa0/0 <-> SW-BRANCH2 fa0/1 <-> PC-B2 fa0
12. R-DATACENTER fa0/0 <-> SW-SERVIDORES fa0/1 <-> SERVER fa0
13. R-DATACENTER fa0/1 <-> SW-ADMIN fa0/1 <-> PC-ADM fa0

### 2.4 Entregable del Paso 2

- Guardar como `ProyectoIntegrador_202300659_202300673_BASE.pkt`.
- Subir al drive compartido antes de iniciar la Fase I.
- No configurar IPs ni protocolos en esta etapa.

---

## 3. Responsabilidades del Integrante 1

### 3.1 Alcance

- Dispositivos: R-EDGE, R-HQ1, R-BRANCH1, ISP, SW-HQ1, SW-BRANCH1, PC-HQ1, PC-B1.
- Protocolos: estaticas, ruta por defecto, ruta flotante, RIPv2, OSPF (lado edge), redistribucion RIPv2 <-> OSPF.

### 3.2 Fase I - Direccionamiento

- Configurar hostname en R-EDGE, R-HQ1 y R-BRANCH1.
- Configurar cada interfaz WAN y LAN con los comandos:

```
interface g0/0
 description Enlace a <vecino>
 ip address <ip> <mascara>
 no shutdown
```

- Configurar IP, mascara y gateway en PC-HQ1 y PC-B1.
- Verificar conectividad con `ping` a vecinos directos.

**Capturas obligatorias:**

- `01_show_ip_int_brief_R-EDGE.png`
- `02_show_ip_int_brief_R-HQ1.png`
- `03_show_ip_int_brief_R-BRANCH1.png`

### 3.3 Fase II - Enrutamiento estatico

En R-EDGE:

```
ip route 0.0.0.0 0.0.0.0 200.10.9.2
```

En R-HQ1 (ruta estatica flotante hacia LAN-SERVIDORES por el enlace redundante, con AD 130):

```
ip route 172.16.9.0 255.255.255.0 10.9.40.2 130
```

- Verificar en `show ip route` que la ruta flotante NO aparezca mientras OSPF entrega la ruta.

(La captura `04_show_ip_route_R-HQ1_antes_falla.png` la toma el Integrante 2 — ver seccion 4.)

### 3.4 Fase III - RIPv2 en R-BRANCH1

```
router rip
 version 2
 no auto-summary
 network 192.168.9.0
 network 10.0.0.0
```

- Verificar rutas con codigo `R` en `show ip route`.

**Capturas obligatorias:**

- `05_show_ip_route_R-BRANCH1.png`
- `06_show_ip_protocols_R-BRANCH1.png`

### 3.5 Fase IV - OSPF en el lado edge

En R-EDGE:

```
router ospf 10
 router-id 3.3.3.3
 network 10.9.2.0 0.0.0.3 area 0
 default-information originate
```

En R-HQ1:

```
router ospf 10
 router-id 1.1.1.1
 network 10.9.1.0 0.0.0.3 area 0
 network 10.9.2.0 0.0.0.3 area 0
 network 10.9.40.0 0.0.0.3 area 0
 network 192.168.109.0 0.0.0.255 area 0
```

**Captura obligatoria:**

- `07_show_ip_ospf_neighbor_R-HQ1.png`

### 3.6 Fase V - Redistribucion mutua en R-HQ1

```
router rip
 redistribute ospf 10 metric 3
!
router ospf 10
 redistribute rip subnets metric 20 metric-type 2
```

- Verificar que R-BRANCH1 reciba las LAN del nucleo como `R` y que los routers OSPF vean la LAN de BRANCH1 como `O E2`.

(La captura `08_show_ip_route_R-BRANCH1_tras_redistribucion.png` la toma el Integrante 2 — ver seccion 4.)

### 3.7 Fase VII - IA aplicada (mitad del Integrante 1)

**Salidas a recopilar** (exportar como `.txt` desde el CLI):

- `show ip route` en R-HQ1 y R-BRANCH1.
- `show ip protocols` en R-HQ1 y R-BRANCH1.
- `show running-config | section router` en R-HQ1 y R-BRANCH1.

**Prompt exacto para la IA:**

```
Actua como ingeniero de redes senior. A continuacion te comparto salidas reales
de Cisco IOS tomadas de dos routers de una red empresarial que combina RIPv2 y
OSPF con redistribucion mutua en R-HQ1.

1. Identifica todas las rutas RIP, OSPF y estaticas visibles.
2. Determina que ruta esta instalada hacia la red 172.16.9.0/24 y explica por
   que, mencionando distancia administrativa y metrica.
3. Verifica si la redistribucion RIP <-> OSPF esta correctamente configurada en
   R-HQ1. Indica si falta algun parametro.
4. Confirma si `no auto-summary` esta activo en R-BRANCH1 y explica por que es
   necesario con la red 10.0.0.0.
5. Propone comandos adicionales de Cisco IOS para confirmar el diagnostico.

Salidas:
<pegar aqui el contenido de los .txt>
```

- Guardar la respuesta de la IA como `ia_respuesta_int1.txt`.
- Redactar un parrafo indicando si la respuesta fue correcta, incompleta o incorrecta y en que puntos.

### 3.8 Preguntas tecnicas de defensa a cargo del Integrante 1

- Pregunta 2: metrica de RIP y limite.
- Pregunta 5: por que una ruta estatica puede ser preferida a una ruta RIP.
- Pregunta 6: conversion de ruta estatica en flotante.
- Pregunta 9: funcion de `no auto-summary` en RIPv2.
- Pregunta 11: salidas de IOS mas utiles para diagnosticar una falla de enrutamiento.

### 3.9 Secciones del informe a cargo del Integrante 1

- Enunciado adaptado al grupo con tabla de direccionamiento (seccion 1.1).
- Narrativa tecnica de Fases II, III y V.
- Analisis de la respuesta de IA (su mitad).
- Pie de figura de las capturas 01, 02, 03, 05, 06, 07.

---

## 4. Responsabilidades del Integrante 2

### 4.1 Alcance

- Dispositivos: R-HQ2, R-BRANCH2, R-DATACENTER, SW-HQ2, SW-BRANCH2, SW-SERVIDORES, SW-ADMIN, PC-HQ2, PC-B2, SERVER, PC-ADM.
- Protocolos: OSPF (nucleo y data center), costos OSPF, prueba de falla y reconvergencia.

### 4.2 Fase I - Direccionamiento

- Configurar hostname en R-HQ2, R-BRANCH2 y R-DATACENTER.
- Configurar cada interfaz WAN y LAN con los comandos estandar (ver seccion 3.2).
- Incluir el extremo del enlace redundante en R-DATACENTER (`10.9.40.2`).
- Configurar IP, mascara y gateway en PC-HQ2, PC-B2, SERVER y PC-ADM.
- Verificar conectividad con `ping` a vecinos directos.

**Capturas obligatorias:**

- `10_show_ip_int_brief_R-HQ2.png`
- `11_show_ip_int_brief_R-BRANCH2.png`
- `12_show_ip_int_brief_R-DATACENTER.png`

### 4.3 Fase IV - OSPF en el nucleo

En R-HQ2:

```
router ospf 10
 router-id 2.2.2.2
 network 10.9.1.0 0.0.0.3 area 0
 network 10.9.23.0 0.0.0.3 area 0
 network 10.9.30.0 0.0.0.3 area 0
 network 192.168.119.0 0.0.0.255 area 0
```

En R-BRANCH2:

```
router ospf 10
 router-id 4.4.4.4
 network 10.9.23.0 0.0.0.3 area 0
 network 192.168.29.0 0.0.0.255 area 0
```

En R-DATACENTER:

```
router ospf 10
 router-id 5.5.5.5
 network 10.9.30.0 0.0.0.3 area 0
 network 10.9.40.0 0.0.0.3 area 0
 network 172.16.9.0 0.0.0.255 area 0
 network 172.20.9.0 0.0.0.255 area 0
```

**Capturas obligatorias:**

- `13_show_ip_ospf_neighbor_R-HQ2.png`
- `14_show_ip_ospf_neighbor_R-DATACENTER.png`
- `15_show_ip_route_R-HQ2.png`

### 4.4 Fase VI - Costos OSPF y prueba de falla

Configuracion de costos en R-DATACENTER:

```
interface g0/0
 ip ospf cost 10
!
interface g0/1
 ip ospf cost 50
```

- Verificar con `show ip ospf interface` en R-DATACENTER.
- Ejecutar `tracert 192.168.109.10` desde PC-ADM; debe pasar por R-HQ2.

**Captura obligatoria:**

- `16_tracert_PC-ADM_a_PC-HQ1_antes_falla.png`

Prueba de falla controlada:

```
interface g0/2
 shutdown
```
(aplicar en R-HQ2 sobre la interfaz hacia R-DATACENTER)

- Ejecutar nuevamente `tracert 192.168.109.10` desde PC-ADM; debe pasar por R-HQ1.
- Con el enlace principal apagado, tomar en R-HQ1 la captura de la ruta flotante activa.

**Capturas obligatorias:**

- `17_tracert_PC-ADM_a_PC-HQ1_tras_falla.png`
- `18_show_ip_route_R-DATACENTER_tras_falla.png`
- `09_show_ip_route_R-HQ1_tras_falla.png` (en R-HQ1; debe mostrar la ruta a 172.16.9.0/24 con next-hop 10.9.40.2 y AD 130)

Restauracion:

```
interface g0/2
 no shutdown
```

**Captura obligatoria:**

- `19_show_ip_route_R-DATACENTER_recuperado.png`

### 4.5 Capturas de ruta flotante y redistribucion

Con la red de servidores (172.16.9.0/24) ya anunciada por OSPF en R-DATACENTER:

- En R-HQ1, verificar con `show ip route` que la red 172.16.9.0/24 aparece como `O` (OSPF) y la ruta estatica flotante queda inactiva. Captura: `04_show_ip_route_R-HQ1_antes_falla.png`.
- En R-BRANCH1, verificar con `show ip route` que las LAN del nucleo llegan como `R` por la redistribucion. Captura: `08_show_ip_route_R-BRANCH1_tras_redistribucion.png`.

### 4.6 Fase VII - IA aplicada (mitad del Integrante 2)

**Salidas a recopilar** (exportar como `.txt` desde el CLI):

- `show ip route` en R-HQ2 y R-DATACENTER.
- `show ip ospf neighbor` en R-HQ2 y R-DATACENTER.
- `show ip ospf interface brief` en R-DATACENTER.
- `show ip protocols` en R-HQ2 y R-DATACENTER.

**Prompt exacto para la IA:**

```
Actua como ingeniero de redes senior. A continuacion te comparto salidas reales
de Cisco IOS tomadas de dos routers del nucleo OSPF de una red empresarial. El
Data Center tiene dos caminos OSPF: uno principal por R-HQ2 (costo 10) y uno
redundante por R-HQ1 (costo 50). Durante la prueba se simulo una falla en el
enlace principal.

1. Identifica las adyacencias OSPF activas y en que area operan.
2. Determina que ruta esta instalada hacia la red 192.168.109.0/24 y explica
   por que, en terminos de costo OSPF.
3. Diagnostica si la red mantiene conectividad al Data Center durante la falla
   del enlace principal, usando como evidencia los costos y las interfaces
   visibles.
4. Explica la relacion entre `ip ospf cost` y la seleccion de ruta.
5. Propone tres comandos Cisco IOS para verificar la convergencia tras la falla.

Salidas:
<pegar aqui el contenido de los .txt>
```

- Guardar la respuesta de la IA como `ia_respuesta_int2.txt`.
- Redactar un parrafo indicando si la respuesta fue correcta, incompleta o incorrecta y en que puntos.

### 4.7 Preguntas tecnicas de defensa a cargo del Integrante 2

- Pregunta 1: por que OSPF es mas adecuado para el nucleo que RIP.
- Pregunta 3: que representa el costo en OSPF.
- Pregunta 4: diferencia entre metrica y distancia administrativa.
- Pregunta 7: comportamiento cuando un vecino OSPF deja de estar disponible.
- Pregunta 8: verificacion de una adyacencia OSPF.
- Pregunta 10: si una IA puede reemplazar OSPF.
- Pregunta 12: por que una recomendacion de IA debe validarse antes de aplicarse.

### 4.8 Secciones del informe a cargo del Integrante 2

- Portada e indice.
- Resumen ejecutivo.
- Metodologia y descripcion de la topologia (seccion 2).
- Narrativa tecnica de Fases I, IV y VI.
- Analisis de la respuesta de IA (su mitad).
- Tabla preparatoria de troubleshooting oculto (seccion 5).
- Conclusiones generales.
- Pie de figura de las capturas 04, 08, 09, 10 a 19.

---

## 5. Tabla preparatoria de troubleshooting oculto

Esta tabla se construye antes de la defensa para responder rapido al cambio no anunciado del docente.

| Posible cambio del docente | Comando de deteccion |
|---|---|
| Eliminar un `network` de RIP | `show ip protocols` en R-BRANCH1 |
| Modificar una wildcard OSPF | `show running-config | section ospf` |
| Apagar una interfaz | `show ip interface brief` |
| Cambiar gateway o mascara en un PC | `ipconfig` en el PC afectado |
| Modificar una ruta estatica | `show ip route static` |
| Eliminar `no auto-summary` | `show ip protocols` + verificar sumarizacion |
| Cambiar una IP WAN | `show interfaces` y `ping` al vecino |
| Alterar un costo OSPF | `show ip ospf interface` |

---

## 6. Inventario de capturas (nombres exactos)

| # | Nombre de archivo | Responsable |
|---|---|---|
| 01 | 01_show_ip_int_brief_R-EDGE.png | Integrante 1 |
| 02 | 02_show_ip_int_brief_R-HQ1.png | Integrante 1 |
| 03 | 03_show_ip_int_brief_R-BRANCH1.png | Integrante 1 |
| 04 | 04_show_ip_route_R-HQ1_antes_falla.png | Integrante 2 |
| 05 | 05_show_ip_route_R-BRANCH1.png | Integrante 1 |
| 06 | 06_show_ip_protocols_R-BRANCH1.png | Integrante 1 |
| 07 | 07_show_ip_ospf_neighbor_R-HQ1.png | Integrante 1 |
| 08 | 08_show_ip_route_R-BRANCH1_tras_redistribucion.png | Integrante 2 |
| 09 | 09_show_ip_route_R-HQ1_tras_falla.png | Integrante 2 |
| 10 | 10_show_ip_int_brief_R-HQ2.png | Integrante 2 |
| 11 | 11_show_ip_int_brief_R-BRANCH2.png | Integrante 2 |
| 12 | 12_show_ip_int_brief_R-DATACENTER.png | Integrante 2 |
| 13 | 13_show_ip_ospf_neighbor_R-HQ2.png | Integrante 2 |
| 14 | 14_show_ip_ospf_neighbor_R-DATACENTER.png | Integrante 2 |
| 15 | 15_show_ip_route_R-HQ2.png | Integrante 2 |
| 16 | 16_tracert_PC-ADM_a_PC-HQ1_antes_falla.png | Integrante 2 |
| 17 | 17_tracert_PC-ADM_a_PC-HQ1_tras_falla.png | Integrante 2 |
| 18 | 18_show_ip_route_R-DATACENTER_tras_falla.png | Integrante 2 |
| 19 | 19_show_ip_route_R-DATACENTER_recuperado.png | Integrante 2 |

---

## 7. Verificacion de cumplimiento del enunciado

| Requisito | Responsable | Fase |
|---|---|---|
| Diseno de topologia y orden documental | Integrante 1 (topologia) / Integrante 2 (documental) | Seccion 2 / Seccion 4.7 |
| Direccionamiento IPv4 | Ambos (cada quien su lado) | Fase I |
| Enrutamiento estatico y ruta por defecto | Integrante 1 | Fase II |
| Ruta estatica flotante | Integrante 1 | Fase II / Fase VI |
| RIPv2 | Integrante 1 | Fase III |
| OSPF y adyacencias | Integrante 2 | Fase IV |
| Redundancia y prueba de falla | Integrante 2 | Fase VI |
| Troubleshooting oculto | Integrante 2 (preparacion) / Ambos (defensa) | Seccion 5 |
| Caso de IA aplicada | Ambos (una mitad cada uno) | Fase VII |
| Informe y defensa tecnica | Ambos (secciones separadas) | Secciones 3.10 y 4.7 |
