# Bluetooth - Proyecto Nevada

Documentación de comandos utilizados para diagnosticar, configurar y verificar la comunicación entre un Nintendo Wii Remote RVL-036 y Dolphin Emulator en Linux.

Este documento reúne las herramientas utilizadas durante la investigación de Proyecto Nevada para analizar:

- Stack Bluetooth BlueZ.
- Adaptadores Bluetooth.
- Comunicación con Nintendo Wii Remote.
- Permisos del sistema.
- Diagnóstico de problemas con Dolphin Emulator.

---

## Hardware Bluetooth utilizado

### Nintendo Wii Remote

Modelo:
Nintendo Wii Remote
RVL-036

Identificado en Linux como:
Nintendo RVL-CNT-01

---

### Bluetooth interno del portátil

Modelo:
Realtek RTL8852BE PCIe 802.11ax Wireless Network Controller

Características:

- WiFi 6.
- Bluetooth integrado.
- Adaptador interno del Lenovo LOQ 15IAX9.

Fue utilizado durante las primeras pruebas de conexión.

---

### Adaptador Bluetooth externo

Modelo detectado:
Realtek Semiconductor Corp. Bluetooth 5.3 Radio

Identificador USB:

`0bda:a729`

Este adaptador fue utilizado durante las pruebas con Dolphin Emulator y Real Wii Remote.

---

## 1. bluetoothctl

### Iniciar la herramienta Bluetooth de Linux

```bash
bluetoothctl
```

`bluetoothctl` es la herramienta principal del stack Bluetooth BlueZ.

Permite:

- Administrar adaptadores Bluetooth.
- Buscar dispositivos.
- Revisar conexiones.
- Consultar información del Wiimote.

## 2. Listar adaptadores Bluetooth

Dentro de `bluetoothctl`:

```bash
list
```

Ejemplo obtenido durante Proyecto Nevada:
Controller 8A:88:4B:A2:CB:C0
Controller 24:B2:B9:1A:5E:B6

Esto permitió confirmar la existencia de:

- Bluetooth interno del portátil.
- Adaptador Bluetooth USB externo.

## 3. Escanear dispositivos Bluetooth

Iniciar búsqueda:

```bash
scan on
```

Detener búsqueda:

```bash
scan off
```

Durante la investigación permitió detectar:
Nintendo RVL-CNT-01

## 4. Mostrar dispositivos detectados

Comando:

```bash
devices
```

Ejemplo:
Device XX:XX:XX:XX:XX:XX Nintendo RVL-CNT-01

## 5. Consultar información del Wiimote

Comando:

```bash
info MAC_DEL_DISPOSITIVO
```

Ejemplo:

```bash
info XX:XX:XX:XX:XX:XX
```

Información importante:

- Nombre del dispositivo.
- Estado de conexión.
- Estado de confianza.
- Servicios disponibles.

Ejemplo:
Name: Nintendo RVL-CNT-01
Paired: no
Bonded: no
Trusted: yes
Connected: yes

## 6. Conectar manualmente el Wiimote

Comando:

```bash
connect MAC_DEL_DISPOSITIVO
```

Ejemplo:

```bash
connect XX:XX:XX:XX:XX:XX
```

Fue utilizado para comprobar si Linux podía establecer comunicación con el mando.

## 7. Dispositivos emparejados

Comando:

```bash
paired-devices
```

Permite consultar dispositivos Bluetooth registrados en BlueZ.

Durante las pruebas se comprobó que el Wiimote no siempre requiere un emparejamiento tradicional como otros dispositivos Bluetooth.

## 8. Identificar hardware USB

Fuera de `bluetoothctl`:

```bash
lsusb
```

Utilizado para identificar el adaptador Bluetooth externo.

Resultado relevante:
Bus 001 Device 008:
ID 0bda:a729 Realtek Semiconductor Corp. Bluetooth 5.3 Radio

## 9. Información avanzada del controlador Bluetooth

Comando:

```bash
sudo btmgmt info
```

Permitió analizar:

- Controladores Bluetooth disponibles.
- Estado del hardware.
- Capacidades soportadas.

Ejemplo:
hci0
hci1

Esto confirmó la presencia de múltiples controladores Bluetooth.

## 10. Ver módulos Bluetooth cargados

Comando:

```bash
lsmod | grep bluetooth
```

Resultado esperado:
bluetooth
btrtl
btusb
rfcomm

Esto confirmó que Linux tenía activo el stack Bluetooth necesario.

## 11. Driver hid_wiimote

Linux dispone de un driver específico para Nintendo Wii Remote: `hid_wiimote`.

Carga manual:

```bash
sudo modprobe hid_wiimote
```

Comprobación:

```bash
lsmod | grep hid_wiimote
```

Este driver permite que Linux reconozca el Wiimote como dispositivo HID.

## 12. Diagnóstico del kernel

Consultar mensajes relacionados con Bluetooth:

```bash
dmesg | grep bluetooth
```

También puede utilizarse:

```bash
journalctl -k | grep bluetooth
```

Estas herramientas permiten revisar:

- Carga de drivers.
- Errores del adaptador.
- Problemas de comunicación.

## 13. Permisos para Dolphin Bluetooth Passthrough

Durante las pruebas con Bluetooth Passthrough apareció un problema de permisos.

Dolphin detectaba el adaptador Bluetooth:
0bda:a729 Realtek Semiconductor Corp. Bluetooth 5.3 Radio

pero no podía acceder directamente al dispositivo.

Error encontrado:
Access denied (insufficient permissions)
LIBUSB_ERROR_ACCESS

El problema no era una incompatibilidad del adaptador, sino una restricción de permisos del sistema.

### Crear regla udev para Dolphin

Crear archivo:

```bash
sudo nano /etc/udev/rules.d/51-dolphin-bluetooth.rules
```

Contenido:
SUBSYSTEM=="usb", ATTR{idVendor}=="0bda", ATTR{idProduct}=="a729", MODE="0666"

Recargar reglas:

```bash
sudo udevadm control --reload-rules
```

Aplicar cambios:

```bash
sudo udevadm trigger
```

Después de esto se debe reconectar el adaptador Bluetooth.

---

## Conclusiones

Las pruebas realizadas demostraron que:

- Linux detectaba correctamente el hardware Bluetooth.
- El Wiimote podía ser identificado por BlueZ.
- Los permisos del sistema podían impedir el acceso directo de Dolphin al adaptador.
- El adaptador Bluetooth no era la única variable del problema.
- La configuración interna de Dolphin tenía un impacto mayor que cambiar únicamente el hardware.

La comunicación correcta del Wiimote depende de:
Nintendo Wii Remote
|
Bluetooth
|
Linux
|
Drivers
|
Dolphin Emulator
|
Configuración correcta

Proyecto Nevada confirmó que conectar un Wiimote no depende solamente de detectar el dispositivo Bluetooth, sino de comprender toda la cadena de comunicación.

