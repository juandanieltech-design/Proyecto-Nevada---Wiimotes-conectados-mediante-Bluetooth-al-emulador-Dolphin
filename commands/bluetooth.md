# Bluetooth - Proyecto Nevada

Documentación de comandos utilizados para diagnosticar, configurar y verificar la comunicación entre un Nintendo Wii Remote RVL-036 y Dolphin Emulator en Linux.

Este documento reúne las herramientas utilizadas durante la investigación de Proyecto Nevada para analizar:

- Stack Bluetooth BlueZ.
- Adaptadores Bluetooth.
- Comunicación con Nintendo Wii Remote.
- Permisos del sistema.
- Diagnóstico de problemas con Dolphin Emulator.

---

# Hardware Bluetooth utilizado

## Nintendo Wii Remote

Modelo:
Nintendo Wii Remote
RVL-036

Identificado en Linux como:
Nintendo RVL-CNT-01


---

## Bluetooth interno del portátil

Modelo:

Realtek RTL8852BE PCIe 802.11ax Wireless Network Controller


Características:

- WiFi 6.
- Bluetooth integrado.
- Adaptador interno del Lenovo LOQ 15IAX9.

Fue utilizado durante las primeras pruebas de conexión.

---

## Adaptador Bluetooth externo

Modelo detectado:
Realtek Semiconductor Corp. Bluetooth 5.3 Radio


Identificador USB:
0bda:a729


Este adaptador fue utilizado durante las pruebas con Dolphin Emulator y Real Wii Remote.

---

# 1. bluetoothctl

## Iniciar la herramienta Bluetooth de Linux

```bash
bluetoothctl

bluetoothctl es la herramienta principal del stack Bluetooth BlueZ.

Permite:

Administrar adaptadores Bluetooth.
Buscar dispositivos.
Revisar conexiones.
Consultar información del Wiimote.


