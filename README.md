# Proyecto Nevada

## Wiimotes conectados mediante Bluetooth al emulador Dolphin

Documentación técnica de la conexión de mandos originales de Nintendo Wii a Dolphin Emulator en Linux mediante Bluetooth, incluyendo resolución de problemas, configuración, análisis de hardware y pruebas en entornos reales.

---

## Índice

- Introducción
- Objetivos
- Filosofía del proyecto
- Arquitectura del sistema
- Hardware utilizado
- Herramientas de diagnóstico
- Funcionamiento real del Wiimote
- Conceptos fundamentales
  - Wiimote Real
  - Emulated Wii Remote
  - Bluetooth Passthrough
  - BlueZ
  - hid_wiimote
  - xwiimote
  - Sensor Bar
  - MotionPlus

---

## Introducción

Proyecto Nevada nace con un objetivo concreto:

**Conseguir utilizar un Nintendo Wii Remote original en un sistema Linux moderno para jugar títulos de Nintendo Wii mediante Dolphin Emulator, utilizando hardware físico y manteniendo una experiencia cercana a una consola Wii real.**

Aunque existen numerosos tutoriales sobre cómo conectar un Wiimote al ordenador, muchos presentan problemas:

- Utilizan versiones antiguas de Dolphin.
- Asumen hardware Bluetooth específico.
- No explican diferencias entre métodos de conexión.
- No documentan problemas reales encontrados durante la configuración.
- Confunden el funcionamiento de la Sensor Bar.
- No explican por qué ciertas configuraciones funcionan y otras no.

Proyecto Nevada no busca ser únicamente una guía de instalación.

Su objetivo principal es documentar una investigación técnica:

- Qué se intentó.
- Qué hipótesis surgieron.
- Qué problemas aparecieron.
- Qué soluciones funcionaron.
- Qué conclusiones fueron descartadas.

La intención es que esta documentación pueda servir como referencia futura para comprender el comportamiento del Wiimote en sistemas Linux.

---

## Objetivo principal

Conseguir una experiencia funcional utilizando:

Nintendo Wii Remote original
+
Bluetooth
+
Linux
+
Dolphin Emulator
+
Sensor Bar
=
Experiencia Wii funcional


El objetivo no era solamente conseguir que Linux detectara un dispositivo Bluetooth.

El verdadero objetivo era:

- Jugar títulos de Nintendo Wii.
- Utilizar movimiento.
- Utilizar Nunchuk.
- Utilizar apuntado mediante infrarrojos.
- Evaluar compatibilidad con juegos que requieren características especiales.

---

## Objetivos específicos

Proyecto Nevada tiene varios objetivos técnicos:

**Comprender el funcionamiento interno del Wiimote**

Investigar cómo funcionan:

- Bluetooth.
- Acelerómetro.
- Cámara infrarroja.
- Sensor Bar.
- Nunchuk.
- Wii MotionPlus.

**Analizar la comunicación entre componentes**

Comprender la interacción entre:

- Sistema operativo Linux.
- Stack Bluetooth BlueZ.
- Adaptadores Bluetooth.
- Drivers del kernel.
- Dolphin Emulator.
- Nintendo Wii Remote.

**Documentar problemas reales**

Registrar:

- Errores de conexión.
- Problemas de permisos.
- Limitaciones de RetroArch.
- Diferencias entre Real Wii Remote y Emulated Wii Remote.
- Problemas encontrados con el puntero.

**Crear una documentación reproducible**

El proyecto busca que otra persona pueda entender:

- Qué se hizo.
- Por qué se hizo.
- Qué resultado tuvo.
- Qué alternativas existen.

---

## Filosofía del proyecto

Proyecto Nevada no busca demostrar únicamente que:

*"Un Wiimote puede conectarse a Dolphin".*

Busca comprender:

*"Por qué funciona, por qué falla y qué componentes intervienen".*

Cada error encontrado forma parte del conocimiento generado.

En lugar de ocultar los problemas, la documentación conserva:

- Intentos fallidos.
- Cambios de estrategia.
- Hipótesis descartadas.
- Soluciones encontradas.

La filosofía del proyecto puede resumirse así:

> Si alguien vuelve a enfrentarse al mismo problema dentro de varios años, debería poder entender qué ocurrió y por qué.

---

## Arquitectura del sistema

Antes de analizar la configuración es necesario comprender qué elementos participan en una sesión normal.

Nintendo Wii Remote
|
|
Bluetooth
|
|
Adaptador Bluetooth
|
|
Linux
|
|
Dolphin Emulator
|

| |
Sensor Bar Nunchuk
|
|
Cámara infrarroja del Wiimote



Cada componente cumple una función diferente.

### Componentes del sistema

**Sistema operativo Linux**

Responsable de:

- Gestionar Bluetooth.
- Cargar drivers.
- Administrar permisos.
- Comunicar dispositivos HID.
- Ejecutar Dolphin.

**Adaptador Bluetooth**

Elemento fundamental del proyecto.

El Wiimote no funciona como un mando Bluetooth moderno convencional.

La compatibilidad depende mucho del chipset utilizado.

Durante Proyecto Nevada se probaron:

- Bluetooth interno del portátil.
- Adaptador Bluetooth USB externo Realtek.

**Dolphin Emulator**

Dolphin es el encargado de interpretar la comunicación del Wiimote.

Dispone de varios modos:

- Emulated Wii Remote.
- Real Wii Remote.
- Bluetooth Passthrough.

La elección del modo cambia completamente la forma en que se procesa la información.

**Nintendo Wii Remote**

Modelo utilizado durante Proyecto Nevada:

- Nintendo Wii Remote
- Modelo: RVL-036
- Color: Negro
- Fabricante: Nintendo

Funciones utilizadas:

- Botones físicos.
- Acelerómetro.
- Cámara infrarroja.
- Comunicación Bluetooth.
- Nunchuk.

Accesorio pendiente de pruebas:

- Wii MotionPlus

**Sensor Bar Wii U**

Durante el proyecto se utilizó una barra sensora original compatible con Wii U.

Importante:

> La barra sensora no transmite información.
> 
> Su única función es generar puntos de luz infrarroja.

---

## Hardware utilizado

### Equipo principal Linux

**Modelo:**

Lenovo LOQ 15IAX9

**Procesador:**

Intel Core i5-12450HX
12th Gen Intel

**GPU:**

Intel UHD Graphics
+
NVIDIA GeForce RTX 3050 Laptop GPU 6GB

**Sistema operativo:**

Linux Ubuntu

### Bluetooth interno

El equipo utiliza:

Realtek RTL8852BE PCIe
802.11ax Wireless Network Controller

Este controlador incluye:

- WiFi.
- Bluetooth integrado.

Durante las primeras pruebas fue utilizado como solución inicial.

### Adaptador Bluetooth externo

Debido a problemas iniciales con Real Wii Remote, se incorporó un adaptador externo.

**Modelo detectado:**

Realtek Semiconductor Corp.
Bluetooth 5.3 Radio

**Identificador USB:**

0bda:a729

**Características:**

- Interfaz USB.
- Bluetooth 5.3.
- Chipset Realtek.

Este adaptador permitió realizar pruebas específicas con Real Wii Remote.

---

## Herramientas de diagnóstico utilizadas

Proyecto Nevada utilizó herramientas estándar de Linux para analizar el comportamiento del hardware.

### bluetoothctl

Herramienta principal de BlueZ.

Ejecutar:

```bash
bluetoothctl

Permite administrar:

Adaptadores Bluetooth.

Dispositivos detectados.

Emparejamientos.

Estados de conexión.

list
Muestra los adaptadores Bluetooth disponibles:

bash
list
Ejemplo obtenido:

text
Controller 8A:88:4B:A2:CB:C0
Controller 24:B2:B9:1A:5E:B6
devices
Lista dispositivos detectados:

bash
devices
paired-devices
Muestra dispositivos emparejados:

bash
paired-devices
info
Información detallada de un dispositivo:

bash
info MAC_DEL_DISPOSITIVO
Permite comprobar:

Nombre.

Conexión.

Estado de confianza.

Servicios disponibles.

lsusb
Utilizado para identificar hardware USB.

##Comando:

bash
lsusb
Resultado relevante:

text
0bda:a729 Realtek Semiconductor Corp. Bluetooth 5.3 Radio
btmgmt
Herramienta de bajo nivel para Bluetooth.

Comando utilizado:

bash
sudo btmgmt info
Permitió confirmar la existencia de dos controladores Bluetooth:

hci0

hci1

#Comprobación de módulos Bluetooth
##Comando:

bash
lsmod | grep bluetooth
Resultado:

text
bluetooth
btrtl
btusb
rfcomm
Confirmó que Linux tenía cargado el stack Bluetooth necesario.

Driver hid_wiimote
Linux dispone de un driver específico:

hid_wiimote

Permite reconocer el Wiimote como dispositivo HID.

Carga manual:

bash
sudo modprobe hid_wiimote
Comprobación:

bash
lsmod | grep hid_wiimote
Funcionamiento real del Wiimote
Primera conclusión técnica
Antes de comenzar las pruebas con Dolphin apareció una conclusión fundamental:

El Wiimote no es simplemente un mando Bluetooth.

Está formado por varios sistemas independientes:

text
Bluetooth
    |
Comunicación del mando

Acelerómetro
    |
Movimiento

Cámara IR
    |
Puntero

MotionPlus
    |
Rotación precisa
Comprender esta separación sería clave para todo el desarrollo posterior de Proyecto Nevada.

##Conceptos fundamentales
Wiimote Real
Control físico original de Nintendo Wii (o third-party compatible). Se conecta por Bluetooth al PC. Ofrece la experiencia auténtica con sensor de movimiento, apuntado y botones físicos. Requiere emparejamiento y configuración adecuada.

##Emulated Wii Remote
Control remoto de Wii simulado por software. No requiere hardware físico, se maneja con teclado, mouse o joystick. Útil para probar juegos sin tener un Wiimote real, pero con precisión y respuesta limitadas.

##Bluetooth Passthrough
Modo especial en emuladores (como Dolphin) que da acceso directo al adaptador Bluetooth del sistema, saltándose el stack Bluetooth del SO. Mejora la latencia y compatibilidad con Wiimotes reales, especialmente con MotionPlus y periféricos originales, pero impide usar otros dispositivos Bluetooth al mismo tiempo.

##BlueZ
Stack Bluetooth oficial para Linux. Es el encargado de gestionar la detección, emparejamiento y conexión de dispositivos Bluetooth en el sistema. Compatible con Wiimotes, aunque a veces requiere configuraciones extras para funcionar con MotionPlus o múltiples mandos.

##hid_wiimote
Controlador (driver) del núcleo Linux que permite que el sistema operativo reconozca el Wiimote como un dispositivo de entrada estándar (HID). Una vez cargado, el Wiimote puede usarse como joystick genérico en juegos y emuladores sin necesidad de software adicional.

##xwiimote
Biblioteca y conjunto de utilidades en espacio de usuario que ofrece una interfaz más avanzada para manejar Wiimotes en Linux. Permite leer datos de sensores (acelerómetro, giroscopio), manejar extensiones (Nunchuk, Classic Controller) y usar el infrarrojo, todo sin depender de emuladores específicos.

##Sensor Bar
Barra con LEDs infrarrojos que se coloca encima o debajo de la pantalla. El Wiimote usa su cámara frontal para detectar estos puntos de luz y calcular hacia dónde apunta. En PC se puede usar la barra original de Nintendo (conectada por USB o inalámbrica) o barras genéricas de terceros. También se pueden emular con dos velas o LEDs, pero con menor precisión.

##MotionPlus
Accesorio (o integrado en mandos posteriores) que añade un giroscopio de 3 ejes al Wiimote, permitiendo detectar rotaciones y movimientos angulares con mucha mayor precisión. Fundamental para juegos que requieren movimientos exactos (Wii Sports Resort, Zelda Skyward Sword). En Linux requiere soporte del driver (hid_wiimote) o de bibliotecas como xwiimote para leer sus datos correctamente.
