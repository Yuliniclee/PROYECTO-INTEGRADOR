# Arreglar el enlace R-EDGE ↔ R-HQ2 (y que no se vuelva a caer)

## OPCIÓN A — Arreglo rápido 

### A1. Volver a poner el transceptor (si se quitó)

- Doble clic en **R-HQ2** → pestaña **Physical**.
- Apaga el router con el botón de encendido del chasis.
- Arrastra **GLC-LH-SMD** al huequito del HWIC-1GE-SFP del slot que da **g0/2/0**.
- Enciende el router.
- Haz lo mismo en **R-EDGE** si su extremo también es SFP.

### A2. Volver a cablear

- Cable de **fibra** (el amarillo con forma de "~").
- Clic en R-EDGE → el puerto SFP (ej. **g0/2** o **g0/3/0**, el que tenga la IP 10.9.3.1).
- Clic en R-HQ2 → **GigabitEthernet0/2/0**.

### A3. Reaplicar IPs (por si se perdieron)

En **R-EDGE**:

```
enable
configure terminal
interface g0/2
 description Enlace a R-HQ2
 ip address 10.9.3.1 255.255.255.252
 no shutdown
exit
end
write memory
```

En **R-HQ2**:

```
enable
configure terminal
interface g0/2/0
 description Enlace a R-EDGE
 ip address 10.9.3.2 255.255.255.252
 no shutdown
exit
end
write memory
```

### A4. Guardar BIEN (los dos pasos, siempre)

- `write memory` en cada router (ya lo hiciste arriba).
- **Ctrl + S** en Packet Tracer para guardar el .pkt a disco.
- Si solo haces uno de los dos, al reabrir se pierde.

---

## Comandos para verificar que SÍ funcionó

En **R-HQ2**:

```
show ip interface brief
```
- `GigabitEthernet0/2/0  10.9.3.2  up  up`  (en Opción A/B)

```
show cdp neighbors
```
- Debe aparecer **R-EDGE** por Gig0/2/0.

```
ping 10.9.3.1
```
- Debe salir `!!!!!` → 100%.

```
show ip ospf neighbor
```
- Deben estar los **4 vecinos FULL**:
  - 1.1.1.1 (R-HQ1)
  - 3.3.3.3 (R-EDGE)
  - 4.4.4.4 (R-BRANCH2)
  - 5.5.5.5 (R-DATACENTER)

Si usaste la Opción C, en lugar de los 4 verás 3 vecinos (sin R-EDGE directo), y eso está bien.

---

## Regla de oro para no perder nada

Cada vez que termines un bloque de cambios:

1. `write memory` en el router que tocaste.
2. **Ctrl + S** en Packet Tracer.

Guarda así cada 5-10 minutos. La mayoría de las "cosas que se borran solas" son cambios que nunca llegaron a disco.
