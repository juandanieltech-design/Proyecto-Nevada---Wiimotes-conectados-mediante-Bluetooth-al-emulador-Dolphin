## Bluetooth interno del portátil

El equipo principal cuenta con un adaptador Bluetooth integrado mediante:

**Realtek RTL8852BE PCIe 802.11ax Wireless Network Controller**

Características:

- WiFi 6.
- Bluetooth integrado.
- Controlador interno del portátil.

Durante las primeras pruebas de Proyecto Nevada se utilizó este adaptador para investigar la conexión del Nintendo Wii Remote en Linux.

Sin embargo, durante las pruebas con Dolphin Emulator utilizando el modo Real Wii Remote, se decidió desactivar temporalmente el Bluetooth interno del equipo.

### Motivo de la desactivación

La desactivación no se realizó porque el Bluetooth interno fuera incompatible o defectuoso.

El objetivo fue evitar posibles conflictos entre:

- Bluetooth interno del portátil.
- Adaptador Bluetooth USB externo.
- Dolphin Emulator.
- Comunicación directa con el Wiimote.

La configuración experimental quedó:

Bluetooth interno
X

Bluetooth USB Realtek
|
Dolphin
|
Nintendo Wii Remote


Esta separación permitió realizar pruebas controladas utilizando únicamente el adaptador externo.

### Resultado de las pruebas

Posteriormente se comprobó que:

- El Bluetooth interno funcionaba correctamente.
- No era obligatorio utilizar un adaptador externo en Linux.
- El problema principal no estaba relacionado únicamente con el hardware Bluetooth.
- La configuración correcta de Dolphin tenía mayor impacto que cambiar de adaptador.

Después de las pruebas, el Bluetooth interno fue reactivado en Linux.

En Windows también se mantuvo como parte del proceso de comparación experimental, permitiendo comprobar diferencias entre sistemas operativos y configuraciones.
