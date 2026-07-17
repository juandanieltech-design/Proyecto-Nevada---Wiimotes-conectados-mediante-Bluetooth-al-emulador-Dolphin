# Proyecto Nevada

## Wiimotes conectados mediante Bluetooth al emulador Dolphin

Documentación técnica de la conexión de mandos originales de Nintendo Wii a Dolphin Emulator en Linux mediante Bluetooth, incluyendo resolución de problemas, configuración, análisis de hardware y pruebas en entornos reales.

---

## Índice

- [Introducción](#introducción)
- [Objetivo principal](#objetivo-principal)
- [Objetivos específicos](#objetivos-específicos)
- [Filosofía del proyecto](#filosofía-del-proyecto)
- [Arquitectura del sistema](#arquitectura-del-sistema)
- [Hardware utilizado](#hardware-utilizado)
- [Herramientas de diagnóstico utilizadas](#herramientas-de-diagnóstico-utilizadas)
- [Funcionamiento real del Wiimote](#funcionamiento-real-del-wiimote)
- [Conceptos fundamentales](#conceptos-fundamentales)
  - [Wiimote Real](#wiimote-real)
  - [Emulated Wii Remote](#emulated-wii-remote)
  - [Bluetooth Passthrough](#bluetooth-passthrough)
  - [BlueZ](#bluez)
  - [hid_wiimote](#hid_wiimote)
  - [xwiimote](#xwiimote)
  - [Sensor Bar](#sensor-bar)
- [Desarrollo histórico de la investigación (Parte 2/3)](#desarrollo-histórico-de-la-investigación-parte-23)
  - [Fase 1 – Definición del problema](#fase-1--definición-del-problema)
  - [Fase 2 – Primer contacto con el Wiimote en Linux](#fase-2--primer-contacto-con-el-wiimote-en-linux)
  - [Fase 3 – Investigación en RetroArch](#fase-3--investigación-en-retroarch)
  - [Fase 4 – Comprendiendo la Sensor Bar](#fase-4--comprendiendo-la-sensor-bar)
  - [Fase 5 – Migración a Dolphin Emulator](#fase-5--migración-a-dolphin-emulator)
- [Desarrollo histórico de la investigación (Parte 3/3)](#desarrollo-histórico-de-la-investigación-parte-33)
  - [Fase 4 – Migración a Dolphin y análisis del hardware Bluetooth](#fase-4--migración-a-dolphin-y-análisis-del-hardware-bluetooth)
  - [Fase 5 – Primer éxito con Real Wii Remote](#fase-5--primer-éxito-con-real-wii-remote)
- [Desarrollo histórico de la investigación (Parte 4/4 – Final)](#desarrollo-histórico-de-la-investigación-parte-44--final)
  - [Fase 6 – Pruebas temporales en Windows y descubrimiento de la configuración correcta](#fase-6--pruebas-temporales-en-windows-y-descubrimiento-de-la-configuración-correcta)
  - [Fase 7 – Regreso a Linux y optimización final](#fase-7--regreso-a-linux-y-optimización-final)

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

```
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
```

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

### Comprender el funcionamiento interno del Wiimote

Investigar cómo funcionan:

- Bluetooth.
- Acelerómetro.
- Cámara infrarroja.
- Sensor Bar.
- Nunchuk.
- Wii MotionPlus.

### Analizar la comunicación entre componentes

Comprender la interacción entre:

- Sistema operativo Linux.
- Stack Bluetooth BlueZ.
- Adaptadores Bluetooth.
- Drivers del kernel.
- Dolphin Emulator.
- Nintendo Wii Remote.

### Documentar problemas reales

Registrar:

- Errores de conexión.
- Problemas de permisos.
- Limitaciones de RetroArch.
- Diferencias entre Real Wii Remote y Emulated Wii Remote.
- Problemas encontrados con el puntero.

### Crear una documentación reproducible

El proyecto busca que otra persona pueda entender:

- Qué se hizo.
- Por qué se hizo.
- Qué resultado tuvo.
- Qué alternativas existen.

---

## Filosofía del proyecto

Proyecto Nevada no busca demostrar únicamente que:

> *"Un Wiimote puede conectarse a Dolphin".*

Busca comprender:

> *"Por qué funciona, por qué falla y qué componentes intervienen".*

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

```
Nintendo Wii Remote
        |
    Bluetooth
        |
Adaptador Bluetooth
        |
      Linux
        |
  Dolphin Emulator
        |
   ┌────┴────┐
Sensor Bar  Nunchuk
   |
Cámara infrarroja del Wiimote
```

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

`0bda:a729`

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
```

Permite administrar:

- Adaptadores Bluetooth.
- Dispositivos detectados.
- Emparejamientos.
- Estados de conexión.

**`list`**

Muestra los adaptadores Bluetooth disponibles:

```bash
list
```

Ejemplo obtenido:

```
Controller 8A:88:4B:A2:CB:C0
Controller 24:B2:B9:1A:5E:B6
```

**`devices`**

Lista dispositivos detectados:

```bash
devices
```

**`paired-devices`**

Muestra dispositivos emparejados:

```bash
paired-devices
```

**`info`**

Información detallada de un dispositivo:

```bash
info MAC_DEL_DISPOSITIVO
```

Permite comprobar:

- Nombre.
- Conexión.
- Estado de confianza.
- Servicios disponibles.

### lsusb

Utilizado para identificar hardware USB.

Comando:

```bash
lsusb
```

Resultado relevante:

```
0bda:a729 Realtek Semiconductor Corp. Bluetooth 5.3 Radio
```

### btmgmt

Herramienta de bajo nivel para Bluetooth.

Comando utilizado:

```bash
sudo btmgmt info
```

Permitió confirmar la existencia de dos controladores Bluetooth:

- hci0
- hci1

### Comprobación de módulos Bluetooth

Comando:

```bash
lsmod | grep bluetooth
```

Resultado:

```
bluetooth
btrtl
btusb
rfcomm
```

Confirmó que Linux tenía cargado el stack Bluetooth necesario.

### Driver hid_wiimote

Linux dispone de un driver específico: `hid_wiimote`.

Permite reconocer el Wiimote como dispositivo HID.

Carga manual:

```bash
sudo modprobe hid_wiimote
```

Comprobación:

```bash
lsmod | grep hid_wiimote
```

---

## Funcionamiento real del Wiimote

### Primera conclusión técnica

Antes de comenzar las pruebas con Dolphin apareció una conclusión fundamental:

**El Wiimote no es simplemente un mando Bluetooth.**

Está formado por varios sistemas independientes:

```
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
```

Comprender esta separación sería clave para todo el desarrollo posterior de Proyecto Nevada.

---

## Conceptos fundamentales

### Wiimote Real

Control físico original de Nintendo Wii (o third-party compatible). Se conecta por Bluetooth al PC. Ofrece la experiencia auténtica con sensor de movimiento, apuntado y botones físicos. Requiere emparejamiento y configuración adecuada.

### Emulated Wii Remote

Control remoto de Wii simulado por software. No requiere hardware físico, se maneja con teclado, mouse o joystick. Útil para probar juegos sin tener un Wiimote real, pero con precisión y respuesta limitadas.

### Bluetooth Passthrough

Modo especial en emuladores (como Dolphin) que da acceso directo al adaptador Bluetooth del sistema, saltándose el stack Bluetooth del SO. Mejora la latencia y compatibilidad con Wiimotes reales, especialmente con MotionPlus y periféricos originales, pero impide usar otros dispositivos Bluetooth al mismo tiempo.

### BlueZ

Stack Bluetooth oficial para Linux. Es el encargado de gestionar la detección, emparejamiento y conexión de dispositivos Bluetooth en el sistema. Compatible con Wiimotes, aunque a veces requiere configuraciones extras para funcionar con MotionPlus o múltiples mandos.

### hid_wiimote

Controlador (driver) del núcleo Linux que permite que el sistema operativo reconozca el Wiimote como un dispositivo de entrada estándar (HID). Una vez cargado, el Wiimote puede usarse como joystick genérico en juegos y emuladores sin necesidad de software adicional.

### xwiimote

Biblioteca y conjunto de utilidades en espacio de usuario que ofrece una interfaz más avanzada para manejar Wiimotes en Linux. Permite leer datos de sensores (acelerómetro, giroscopio), manejar extensiones (Nunchuk, Classic Controller) y usar el infrarrojo, todo sin depender de emuladores específicos.

### Sensor Bar

Barra con LEDs infrarrojos que se coloca encima o debajo de la pantalla. El Wiimote usa su cámara frontal para detectar estos puntos de luz y calcular hacia dónde apunta. En PC se puede usar la barra original de Nintendo (conectada por USB o inalámbrica) o barras genéricas de terceros. También se pueden emular con dos velas o LEDs, pero con menor precisión.

---

## Desarrollo histórico de la investigación (Parte 2/3)

### Fase 1 – Definición del problema

**Objetivo**

Conseguir utilizar un Nintendo Wii Remote original RVL-036 en Linux para jugar títulos de Nintendo Wii desde un ordenador utilizando Dolphin Emulator.

**Situación inicial**

El punto de partida parecía sencillo:

> "Conectar el Wiimote por Bluetooth y utilizarlo como cualquier otro mando inalámbrico".

La primera hipótesis asumía que el proceso sería similar al de:

- Un teclado Bluetooth.
- Un mouse inalámbrico.
- Un gamepad moderno.

Sin embargo, las primeras pruebas demostraron rápidamente que esta idea era incorrecta.

El Wiimote utiliza un sistema de comunicación diferente al de un dispositivo Bluetooth convencional.

El problema real no era solamente:

> "¿Puede Linux detectar el mando?"

El problema verdadero era:

> "¿Puede Dolphin comunicarse con todas las funciones del Wiimote para reproducir una experiencia Wii completa?"

**Resultado**

La investigación permitió redefinir correctamente el problema.

El funcionamiento dependía de la interacción entre:

- Wiimote.
- Bluetooth.
- Adaptador utilizado.
- Sistema operativo.
- Drivers.
- Dolphin.
- Configuración seleccionada.

**Lecciones aprendidas**

- El Wiimote no es un dispositivo Bluetooth común.
- Detectar el dispositivo no significa poder utilizarlo correctamente.
- La forma de conexión depende del software que gestione el mando.
- Una experiencia Wii completa requiere mucho más que botones funcionando.

### Fase 2 – Primer contacto con el Wiimote en Linux

**Objetivo**

Conseguir que Linux detectara correctamente el Nintendo Wii Remote.

**Primeras pruebas**

Durante esta etapa se realizaron las primeras conexiones utilizando Bluetooth del sistema.

El Wiimote utilizado fue:

- Nintendo Wii Remote
- Modelo: RVL-036
- Color: Negro

**Problema inicial: solicitud de PIN**

Durante el proceso de emparejamiento Linux solicitó un código PIN.

Este comportamiento generó una de las primeras confusiones del proyecto.

Muchos dispositivos Bluetooth tradicionales utilizan códigos como:

- 0000
- 1234
- 1111

Sin embargo, el Wiimote no funciona de esta manera.

**Investigación del comportamiento Bluetooth**

El Wiimote no está diseñado para emparejarse como:

- Auriculares.
- Teléfonos.
- Teclados.
- Ratones.

Dependiendo del método utilizado:

- BlueZ.
- Dolphin.
- Bluetooth Passthrough.

El proceso puede ser diferente.

**Resultado**

Linux consiguió detectar el Wiimote.

Sin embargo, apareció una diferencia fundamental:

```
Dispositivo detectado
        ≠
Dispositivo funcional dentro de Dolphin
```

El mando podía existir dentro del sistema operativo, pero todavía no era capaz de ofrecer una experiencia Wii completa.

**Lecciones aprendidas**

- La conexión Bluetooth era solamente la primera capa del problema.
- El Wiimote necesita un método específico de comunicación.
- El emparejamiento tradicional no siempre es el camino correcto.

### Fase 3 – Investigación en RetroArch

**Objetivo**

Antes de utilizar Dolphin Standalone, se investigó si RetroArch podía proporcionar una solución completa.

**¿Por qué se intentó primero con RetroArch?**

La decisión inicial tenía motivos prácticos.

RetroArch ofrecía ventajas importantes:

*Integración con Steam*

La versión utilizada estaba integrada dentro del ecosistema de Steam.

Esto permitía mantener una biblioteca unificada.

*Guardados en la nube*

Una de las razones principales para intentar esta solución era conservar:

- Save states.
- Archivos .sav.
- Configuraciones.
- Organización de juegos.

Steam Cloud permitía mantener los datos sincronizados.

*Ventajas de una plataforma única*

Utilizar RetroArch significaba:

- Una sola interfaz.
- Una sola biblioteca.
- Una sola configuración general.
- Todos los sistemas emulados en un mismo lugar.

Desde el punto de vista de administración, era una solución muy atractiva.

**Primer resultado positivo**

Durante las primeras pruebas ocurrió algo importante:

Steam detectó correctamente el Wiimote.

Como consecuencia:

RetroArch también pudo utilizarlo como dispositivo de entrada.

Esto confirmó que:

- Linux veía el mando.
- Steam veía el mando.
- RetroArch recibía información del mando.

**Problema encontrado: botones invertidos**

Aunque el mando era detectado, apareció el primer inconveniente:

Los botones:

- 1
- 2

aparecían invertidos.

Esto indicaba que RetroArch estaba tratando el Wiimote como un dispositivo de entrada genérico.

No estaba interpretándolo como un Wiimote completo.

**Investigación del core Dolphin en RetroArch**

La siguiente prueba consistió en utilizar el núcleo Dolphin dentro de RetroArch.

Se analizaron las configuraciones disponibles.

Las opciones incluían:

- Wiimote.
- Sideways Wiimote.
- Wiimote + Nunchuk.
- Classic Controller.
- Classic Controller Pro.

A primera vista parecía suficiente.

Sin embargo, apareció una limitación crítica.

**Limitación del Dolphin Core en RetroArch**

Las configuraciones disponibles no proporcionaban una implementación completa del Wiimote.

Faltaban elementos fundamentales:

*Puntero infrarrojo*

No existía una configuración equivalente al sistema real de apuntado Wii.

*Sensores de movimiento*

No existía acceso completo a:

- Acelerómetro.
- Movimiento real.
- Control gestual.

*Configuración MotionPlus*

Las opciones disponibles eran demasiado básicas.

El core permitía seleccionar perfiles de control, pero no ofrecía la configuración avanzada disponible en Dolphin Standalone.

**Consecuencia**

Juegos como:

- Super Mario Galaxy.
- Zelda.
- Juegos dependientes del puntero.

No podían reproducir correctamente la experiencia original.

El mando podía existir.

Los botones podían funcionar.

Pero faltaba la parte esencial:

```
Wiimote completo
=
Botones
+
Movimiento
+
Puntero
+
Accesorios
```

**Resultado de RetroArch**

RetroArch funcionaba correctamente como plataforma general.

Pero para el objetivo específico del proyecto:

> Utilizar un Wiimote físico original con todas sus funciones.

No era suficiente.

**Conclusión de la fase**

La investigación cambió la dirección del proyecto.

RetroArch seguía teniendo ventajas:

- Steam Cloud.
- Biblioteca unificada.
- Organización.

Pero la prioridad pasó a ser:

> Conseguir la experiencia Wii más completa posible.

Por esta razón se decidió investigar Dolphin Standalone.

### Fase 4 – Comprendiendo la Sensor Bar

**Objetivo**

Comprender cómo funciona realmente el sistema de apuntado de Nintendo Wii.

**La idea equivocada inicial**

Uno de los errores más comunes es pensar:

> "La Sensor Bar detecta dónde está el Wiimote".

Esto es incorrecto.

**Funcionamiento real**

La comunicación funciona así:

```
Sensor Bar
      |
Emite luz infrarroja
      |
Cámara IR del Wiimote
      |
Calcula posición
      |
Envía información por Bluetooth
      |
Consola Wii / Dolphin
```

**La Sensor Bar no es un sensor**

La barra:

NO:

- Detecta movimiento.
- Lee posición.
- Se comunica por Bluetooth.
- Envía datos al ordenador.

Únicamente:

- Genera dos puntos de luz infrarroja.

**El verdadero sensor está en el Wiimote**

El Wiimote posee:

- Cámara infrarroja.
- Procesamiento interno.
- Capacidad para calcular orientación.

La cámara observa los dos puntos generados por la barra.

Según la posición relativa:

- Calcula dirección.
- Calcula distancia aproximada.
- Permite apuntar.

**Simulación del sistema**

Debido a que la barra solamente genera luz, puede ser reemplazada por:

- Dos LEDs infrarrojos.
- Soluciones caseras.
- Incluso dos velas.

Las velas funcionan porque producen dos fuentes luminosas que la cámara del Wiimote puede interpretar.

No porque tengan comunicación electrónica.

**Influencia en Proyecto Nevada**

Este descubrimiento fue fundamental.

Durante el proyecto ya se disponía de:

- Sensor Bar original Wii U

Por lo tanto:

La parte física del apuntado ya estaba disponible.

El problema real era:

```
Wiimote
+
Bluetooth
+
Dolphin
+
Configuración correcta
```

### Fase 5 – Migración a Dolphin Emulator

**Objetivo**

Investigar el soporte nativo de Dolphin para Nintendo Wii.

Después de las limitaciones encontradas en RetroArch, Dolphin Standalone se convirtió en la plataforma principal.

**Dos caminos investigados**

Dolphin ofrece diferentes maneras de utilizar un Wiimote.

Durante Proyecto Nevada se analizaron:

**Emulated Wii Remote**

*Concepto inicial*

Al principio parecía significar:

> "No usar un Wiimote real".

Esta interpretación resultó ser incorrecta.

*Funcionamiento real*

Emulated Wii Remote significa que:

Dolphin procesa las entradas internamente.

El dispositivo físico puede ser:

- Teclado.
- Mouse.
- Gamepad.
- Wiimote original.

*Ventajas*

Permite:

- Configurar botones.
- Ajustar sensibilidad.
- Configurar puntero.
- Modificar comportamiento.
- Adaptar diferentes dispositivos.

**Real Wii Remote**

En este modo Dolphin intenta comunicarse directamente con el Wiimote como una consola Wii.

La idea es reproducir:

```
Wii original
      |
Bluetooth
      |
Wiimote
```

*Ventajas*

- Mayor cercanía al hardware original.
- Mejor soporte para accesorios oficiales.
- Comunicación directa.

*Problemas*

Depende mucho de:

- Adaptador Bluetooth.
- Drivers.
- Sistema operativo.
- Permisos.
- Compatibilidad del hardware.

**Primera investigación con Dolphin**

Inicialmente se intentó utilizar:

- Real Wii Remote

Sin embargo, el Wiimote no era detectado correctamente.

Aunque:

- Linux veía el mando.
- Bluetooth funcionaba.

Dolphin no conseguía establecer la comunicación esperada.

**Nueva hipótesis**

El problema podía estar relacionado con:

- Bluetooth interno del portátil.
- Compatibilidad del adaptador.
- Comunicación directa con Dolphin.

Esto llevó a investigar hardware Bluetooth dedicado.

---

## Desarrollo histórico de la investigación (Parte 3/3)

### Fase 4 – Migración a Dolphin y análisis del hardware Bluetooth

**Objetivo**

Después de comprobar las limitaciones del núcleo Dolphin dentro de RetroArch, la investigación se trasladó a Dolphin Emulator Standalone.

La nueva meta era conseguir una experiencia más cercana al funcionamiento de una Nintendo Wii real utilizando hardware original:

- Nintendo Wii Remote.
- Nunchuk.
- Sensor Bar original.
- Adaptador Bluetooth.
- Dolphin Emulator.

En esta fase se analizaron principalmente dos métodos de funcionamiento:

- Emulated Wii Remote utilizando un Wiimote físico.
- Real Wii Remote mediante comunicación directa con Dolphin.

**Primer camino: Emulated Wii Remote**

*Hipótesis inicial*

Debido a que Dolphin permite una gran cantidad de configuraciones internas, se planteó la posibilidad de utilizar el Wiimote físico como entrada para un mando emulado.

La idea era:

```
Nintendo Wii Remote
        |
     Bluetooth
        |
       Linux
        |
      Dolphin
        |
Emulated Wii Remote
```

Este método parecía atractivo porque permitía mantener:

- El hardware original del Wiimote.
- Los sensores físicos del mando.
- La posibilidad de modificar la configuración dentro de Dolphin.

*Problema encontrado inicialmente*

Durante las primeras pruebas apareció un problema importante.

Aunque Linux podía detectar el mando, la experiencia todavía estaba incompleta.

El sistema no lograba reproducir correctamente todos los elementos necesarios para una experiencia Wii:

- Movimiento.
- Puntero infrarrojo.
- Lectura completa del mando.
- Integración con los juegos que requieren funciones especiales.

En este momento la investigación tomó una dirección equivocada.

La hipótesis inicial fue:

> El problema puede estar relacionado con Linux o con el adaptador Bluetooth.

Posteriormente se descubriría que el problema real era la configuración utilizada dentro de Dolphin.

**Investigación del adaptador Bluetooth**

*Motivo del cambio de estrategia*

Al encontrar dificultades utilizando Real Wii Remote y Emulated Wii Remote, apareció una nueva posibilidad:

> Quizás el Bluetooth integrado del portátil no era suficientemente compatible con Dolphin.

Aunque el equipo podía:

- Detectar dispositivos Bluetooth.
- Conectarse con otros periféricos.
- Reconocer el Wiimote.

Eso no significaba necesariamente que pudiera comunicarse correctamente con Dolphin.

Por este motivo se decidió adquirir un adaptador Bluetooth externo dedicado.

**Hardware utilizado**

*Equipo principal Linux*

El ordenador utilizado durante Proyecto Nevada es:

Lenovo LOQ 15IAX9

Componentes relevantes:

*Procesador*

12th Gen Intel(R) Core(TM) i5-12450HX

Características:

- 8 núcleos.
- 12 hilos.
- Frecuencia máxima aproximada: 4.4 GHz.

*GPU*

Sistema híbrido:

GPU integrada:

- Intel UHD Graphics (Alder Lake)

GPU dedicada:

- NVIDIA GeForce RTX 3050 Laptop GPU
- 6GB VRAM

*Bluetooth interno del portátil*

El equipo cuenta con:

Realtek RTL8852BE PCIe 802.11ax Wireless Network Controller

Este dispositivo integra:

- WiFi 6.
- Bluetooth.

Linux detectó el controlador:

```
Controller 24:B2:B9:1A:5E:B6
```

*Adaptador Bluetooth externo*

Durante la investigación se añadió:

Realtek Semiconductor Corp.
Bluetooth 5.3 Radio

Identificación USB:

`0bda:a729`

Información obtenida mediante:

```bash
lsusb
```

Resultado:

```
Bus 001 Device 008:
ID 0bda:a729 Realtek Semiconductor Corp. Bluetooth 5.3 Radio
```

**Verificación del hardware Bluetooth**

Para analizar los adaptadores disponibles se utilizaron diferentes herramientas Linux.

*Listar controladores Bluetooth*

Comando:

```bash
bluetoothctl list
```

Resultado obtenido:

```
Controller 8A:88:4B:A2:CB:C0
Controller 24:B2:B9:1A:5E:B6
```

Esto confirmó la existencia de dos controladores Bluetooth independientes.

*Información avanzada del controlador*

Comando:

```bash
sudo btmgmt info
```

Permitió comprobar:

- Versión Bluetooth.
- Fabricante.
- Capacidades soportadas.
- Estado del controlador.

*Estado del módulo Bluetooth Linux*

Comando:

```bash
lsmod | grep bluetooth
```

Resultado:

```
bluetooth 1130496
```

Esto confirmó que el stack Bluetooth del kernel estaba activo.

**Investigación del Bluetooth Passthrough**

Durante esta fase también se investigó Bluetooth Passthrough.

*Objetivo*

Comprobar si entregar el adaptador Bluetooth directamente a Dolphin podía solucionar los problemas encontrados.

La idea era permitir que Dolphin tuviera una comunicación más parecida a una consola Wii original.

Arquitectura:

```
Dolphin
   |
Bluetooth Passthrough
   |
Adaptador Bluetooth
   |
Wiimote
```

*Resultado del análisis*

Aunque Bluetooth Passthrough tiene ventajas:

- Menor intermediación.
- Comunicación más cercana al hardware real.
- Mejor compatibilidad con algunos accesorios.

También presenta requisitos importantes:

- Adaptador Bluetooth compatible.
- Permisos especiales.
- Configuración más compleja.
- Dependencia del hardware.

Durante Proyecto Nevada esta solución no terminó siendo la elegida.

La investigación demostró que el problema no era únicamente el adaptador Bluetooth.

**Descubrimiento importante**

El adaptador Bluetooth influye, pero no es el único elemento responsable.

El funcionamiento del Wiimote depende de la interacción entre:

```
Wiimote
   +
Bluetooth
   +
Sistema operativo
   +
Drivers
   +
Dolphin
   +
Configuración del emulador
```

Cambiar hardware podía mejorar la situación, pero no solucionaba automáticamente la configuración.

### Fase 5 – Primer éxito con Real Wii Remote

**Objetivo**

Después de adquirir el adaptador Bluetooth externo, se decidió probar nuevamente:

```
Dolphin
     |
Real Wii Remote
     |
Adaptador Bluetooth dedicado
     |
Nintendo Wii Remote
```

La hipótesis era:

> Un adaptador Bluetooth dedicado podría permitir una comunicación más cercana a una Wii real.

**Preparación del entorno**

*Desactivación del Bluetooth interno*

Para evitar conflictos entre controladores Bluetooth se tomó una decisión importante:

> Desactivar temporalmente el Bluetooth integrado del portátil.

La configuración quedó:

```
Bluetooth interno
        X

Bluetooth USB Realtek
        |
     Dolphin
        |
    Wiimote
```

El objetivo era obligar al sistema a utilizar únicamente el adaptador externo.

**Primer resultado positivo**

*Problema anterior*

Antes del adaptador dedicado:

- Dolphin no detectaba correctamente los Wiimotes en modo Real Wii Remote.
- Linux sí podía ver el dispositivo.
- La comunicación completa no se establecía.

*Prueba con New Super Mario Bros Wii*

Durante el inicio del juego se realizó:

- Botón SYNC del Wiimote.
- Detección desde Dolphin.

Resultado:

Por primera vez:

> Dolphin reconoció correctamente un Wiimote físico en modo Real Wii Remote.

*Primera prueba real de movimiento*

El resultado más importante ocurrió al comprobar los movimientos del personaje.

Se verificó:

- Movimiento normal.
- Botones.
- Nunchuk.
- Acelerómetro.

La prueba definitiva fue:

**Power-Up Helicóptero**

El giro del Wiimote funcionó correctamente.

Esto confirmó:

- El acelerómetro estaba funcionando.
- Dolphin recibía datos del movimiento.
- El Wiimote físico estaba siendo utilizado realmente.

Por primera vez Proyecto Nevada dejó de ser una investigación de conexión Bluetooth y pasó a ser una experiencia jugable.

*Prueba con Super Mario Galaxy*

Después del éxito con New Super Mario Bros Wii, se seleccionó un juego más exigente.

Motivos:

Super Mario Galaxy requiere:

- Wiimote.
- Nunchuk.
- Movimiento.
- Puntero infrarrojo.
- Interacción con estrellas y objetos mediante IR.

*Resultado*

El mando funcionaba parcialmente.

Funcionaba:

- ✅ Wiimote.
- ✅ Botones.
- ✅ Nunchuk.
- ✅ Movimiento.

No funcionaba:

- ❌ Puntero infrarrojo.

**Investigación de la Sensor Bar**

Durante esta etapa se comprobó físicamente la barra sensora.

Hardware utilizado:

- Sensor Bar original Nintendo Wii U.

Se verificó:

- Alimentación correcta.
- Funcionamiento con Wii U real.

La consola Wii U reconocía la barra correctamente.

Sin embargo, Dolphin continuaba sin recibir el puntero.

**Descubrimiento fundamental**

La investigación confirmó algo clave:

> La Sensor Bar no envía información.

No existe una comunicación:

```
Sensor Bar
      |
  Bluetooth
      |
   Wiimote
```

El funcionamiento real es:

```
Sensor Bar
      |
Luz infrarroja
      |
Cámara del Wiimote
      |
  Bluetooth
      |
   Dolphin
```

La barra solamente crea dos puntos de referencia.

El Wiimote calcula la posición utilizando su cámara infrarroja.

**Conclusión de la fase**

Esta etapa consiguió avances enormes:

- ✅ Wiimote original conectado.
- ✅ Real Wii Remote funcionando.
- ✅ Movimiento funcionando.
- ✅ Nunchuk funcionando.

Pero apareció una nueva limitación:

- ❌ El puntero infrarrojo todavía no estaba solucionado.

El problema no era la barra.

El problema era cómo Dolphin estaba interpretando la entrada del Wiimote.

**Lecciones aprendidas**

- El Bluetooth del Wiimote no funciona como un mando convencional.
- Un adaptador Bluetooth dedicado puede mejorar la compatibilidad.
- Movimiento y puntero son sistemas separados.
- Tener Sensor Bar no garantiza tener puntero funcional.
- La conexión del mando es solamente una parte de la experiencia Wii.

---

## Desarrollo histórico de la investigación (Parte 4/4 – Final)

### Fase 6 – Pruebas temporales en Windows y descubrimiento de la configuración correcta

**Objetivo**

Después de los problemas encontrados en Linux utilizando principalmente Real Wii Remote, se decidió realizar una prueba temporal utilizando Windows.

La hipótesis en ese momento era:

> Quizás el problema estaba relacionado con Linux, sus permisos, sus drivers Bluetooth o la forma en la que Dolphin interactuaba con el sistema.

El objetivo no era abandonar Linux, sino utilizar Windows como entorno de comparación para determinar si el problema provenía del sistema operativo o de la configuración utilizada.

**Motivo del cambio temporal de plataforma**

Durante las pruebas en Linux se habían encontrado varios obstáculos:

- Configuración de permisos Bluetooth.
- Diferencias entre BlueZ y Dolphin.
- Problemas iniciales con Real Wii Remote.
- Dificultades para conseguir una experiencia completa con movimiento y puntero.

Además, Windows ofrecía una instalación más directa de Dolphin:

- Menos configuración inicial.
- Drivers más simples.
- Menor interacción manual con Bluetooth.

Por este motivo se decidió comprobar si el mismo hardware funcionaba de forma diferente fuera de Linux.

**Hardware utilizado en Windows**

*Adaptador Bluetooth*

Se utilizó el mismo adaptador comprado durante Proyecto Nevada:

Realtek Semiconductor Corp.
Bluetooth 5.3 Radio

USB ID: `0bda:a729`

La intención era mantener constante el hardware y cambiar únicamente:

```
Linux
   |
Windows
```

para observar diferencias reales.

**Primera prueba: Real Wii Remote en Windows**

La primera prueba fue repetir exactamente el procedimiento utilizado anteriormente:

```
Dolphin
   ↓
Real Wii Remote
   ↓
Wiimote físico
```

Sin embargo, ocurrió algo inesperado.

El Wiimote tampoco funcionó inmediatamente en Windows.

El mando seguía sin ofrecer automáticamente:

- Comunicación completa.
- Puntero.
- Experiencia equivalente a Wii real.

**Descubrimiento importante**

Este resultado modificó una hipótesis inicial.

Hasta ese momento parecía posible que:

> Linux fuera el principal responsable del problema.

Pero las pruebas demostraron algo diferente.

El comportamiento era prácticamente el mismo:

*Linux:*

- Detectaba el hardware.
- Requería configuración.
- No ofrecía experiencia completa automáticamente.

*Windows:*

- Detectaba el hardware.
- También necesitaba configuración.
- Real Wii Remote tampoco funcionaba inmediatamente.

La diferencia no estaba en el sistema operativo.

El problema estaba en comprender correctamente Dolphin.

**Descubrimiento de Emulated Wii Remote con hardware real**

Durante la investigación apareció una configuración de Dolphin que cambiaría completamente el rumbo del proyecto.

Se descubrió que:

> Emulated Wii Remote no significa necesariamente teclado, mouse o gamepad convencional.

Ese era uno de los mayores malentendidos iniciales.

*Nueva interpretación de Emulated Wii Remote*

Dolphin permite utilizar un Wiimote físico dentro del sistema de entrada emulado.

La diferencia es:

**Real Wii Remote** — Dolphin intenta comunicarse directamente con el mando.

```
Wiimote
   ↓
Bluetooth
   ↓
Dolphin
```

**Emulated Wii Remote** — Dolphin recibe la información del dispositivo y la procesa internamente.

```
Wiimote físico
   ↓
Entrada del sistema
   ↓
Dolphin
   ↓
Wii Remote emulado
```

**Configuración encontrada**

La configuración utilizada incluía:

- Acelerómetro.
- Movimiento.
- Puntero.
- Nunchuk.

Por primera vez apareció una combinación funcional:

```
Wiimote físico
+
Emulated Wii Remote
+
Sensor Bar
+
Dolphin
```

**Prueba con Super Mario Galaxy**

Se utilizó nuevamente Super Mario Galaxy.

El objetivo era comprobar si esta nueva configuración podía solucionar las limitaciones encontradas anteriormente.

*Resultado obtenido*

La prueba fue satisfactoria.

Se consiguió:

- ✅ Wiimote físico funcionando.
- ✅ Nunchuk funcionando.
- ✅ Movimiento funcionando.
- ✅ Puntero infrarrojo funcionando.

**El descubrimiento del puntero**

Uno de los momentos más importantes ocurrió cuando el punto de referencia del puntero dejó de estar estático.

Anteriormente:

- El Wiimote podía moverse.
- El juego detectaba ciertos controles.
- Pero el puntero no respondía correctamente.

Con esta configuración:

El punto de referencia comenzó a seguir el movimiento del mando.

Esto confirmó:

- La cámara infrarroja del Wiimote estaba funcionando.
- La Sensor Bar estaba siendo detectada correctamente.
- Dolphin estaba interpretando la información del puntero.

**Problemas encontrados**

Aunque la solución era funcional, todavía existían detalles por mejorar.

El principal problema era: **puntero inestable**.

Síntomas:

- Movimiento irregular.
- Pequeñas desviaciones.
- Sensación diferente a una consola Wii real.

La experiencia era jugable, pero necesitaba ajustes.

**Conclusión Linux vs Windows**

Esta fase fue una de las más importantes de todo Proyecto Nevada.

La hipótesis inicial era:

> Windows puede solucionar los problemas que aparecen en Linux.

Las pruebas demostraron lo contrario.

La conclusión fue:

> Windows no hizo nada diferente a Linux.

El avance ocurrió porque se encontró la configuración correcta dentro de Dolphin: Wiimote físico + Emulated Wii Remote.

Esa misma configuración estaba disponible en Linux.

El cambio de sistema operativo solamente permitió descubrir una solución que ya existía.

**Lecciones aprendidas**

- Cambiar de sistema operativo no siempre resuelve un problema.
- Dolphin tiene diferentes formas de utilizar un Wiimote físico.
- Real Wii Remote no siempre es la opción más sencilla.
- Emulated Wii Remote puede funcionar con hardware original.
- La configuración interna de Dolphin tiene más importancia que el sistema operativo.
- Las hipótesis deben cambiar cuando las pruebas contradicen la idea inicial.

### Fase 7 – Regreso a Linux y optimización final

**Objetivo**

Después de descubrir que la solución estaba en la configuración de Dolphin y no en Windows, se decidió regresar al entorno Linux original.

La nueva hipótesis era:

> Si Emulated Wii Remote con hardware real funciona en Windows, también debería funcionar en Linux.

Los objetivos fueron:

- Confirmar la solución en Linux.
- Mejorar el rendimiento.
- Ajustar la estabilidad del puntero.

**Primera prueba de Super Mario Galaxy en Linux**

La configuración utilizada fue:

```
Linux
+
Dolphin
+
Wiimote físico
+
Emulated Wii Remote
+
Nunchuk
+
Sensor Bar original
```

Además, esta prueba se realizó en el portátil principal, con mayor capacidad de hardware.

**Problema encontrado: congelación durante Galaxy**

Durante la partida ocurrió un problema inesperado.

El juego funcionó correctamente hasta la sección donde:

- Rosalina termina su conversación inicial con Mario.
- Se utiliza el giro del Wiimote.
- Es necesario romper cristales cerca del anillo estelar.

En ese momento ocurrió:

- Ralentización severa.
- Pérdida de fluidez.
- Juego prácticamente congelado.

**Primera hipótesis**

La sospecha inicial fue:

> El equipo no tiene suficiente potencia para emular correctamente Wii.

Era una hipótesis razonable debido a:

- Gráficos avanzados de Galaxy.
- Efectos visuales.
- Carga de emulación.

Sin embargo, la investigación mostró otra causa.

**Investigación gráfica**

Se revisó la configuración gráfica de Dolphin.

El backend utilizado era: **OpenGL**.

Durante las pruebas se comprobó que Vulkan ofrecía mejor comportamiento en esta configuración.

**Solución encontrada**

Cambio realizado:

```
OpenGL
   ↓
Vulkan
```

*Resultado*

Después del cambio:

- ✅ La ralentización desapareció.
- ✅ Super Mario Galaxy continuó funcionando correctamente.
- ✅ La estabilidad aumentó considerablemente.

Esto confirmó que el problema no era falta de potencia. La causa era la API gráfica utilizada.

**Ajuste del puntero infrarrojo**

Aunque el juego funcionaba, todavía existía un problema: el puntero no tenía la estabilidad ideal.

*Investigación del comportamiento*

Se analizaron tres sistemas diferentes:

- **Cámara infrarroja** — responsable del apuntado real.
- **Acelerómetro** — responsable de inclinaciones, movimientos rápidos y gestos.
- **Configuración Dolphin** — responsable de combinar ambas entradas.

*Problema identificado*

Una dependencia demasiado alta del acelerómetro podía provocar:

- Movimiento artificial del puntero.
- Correcciones constantes.
- Inestabilidad.

El acelerómetro no debe reemplazar al sistema infrarrojo.

*Ajuste aplicado*

La dependencia del acelerómetro para el puntero fue reducida casi completamente:

- Dependencia acelerómetro: 0% - 1%

Objetivo: Puntero = Sensor infrarrojo (NO Puntero = Sensor infrarrojo + acelerómetro excesivo)

**Resultado final**

Después de estos ajustes:

- ✅ Dolphin funcionando en Linux.
- ✅ Super Mario Galaxy funcionando.
- ✅ Wiimote físico funcionando.
- ✅ Nunchuk funcionando.
- ✅ Movimiento funcionando.
- ✅ Sensor Bar funcionando.
- ✅ Puntero mejorado.
- ✅ Rendimiento estable con Vulkan.

**Estado actual del proyecto**

Proyecto Nevada consiguió demostrar que:

```
Nintendo Wii Remote original
+
Bluetooth
+
Dolphin Emulator
+
Linux
+
Sensor Bar
=
Experiencia Wii funcional
```

La solución final no fue:

- Comprar otro sistema operativo.
- Cambiar completamente de hardware.
- Abandonar Linux.

La solución fue comprender correctamente:

- Cómo funciona el Wiimote.
- Cómo Dolphin procesa entradas.
- Diferencias entre Real Wii Remote y Emulated Wii Remote.
- Separación entre movimiento y apuntado.
- Importancia del backend gráfico.

**Conclusión final del experimento**

Proyecto Nevada comenzó con una pregunta sencilla:

> ¿Es posible utilizar un Nintendo Wii Remote original en Linux moderno para jugar títulos de Wii mediante Dolphin?

La respuesta final es: **sí**.

Pero la investigación demostró que el reto real no era conectar el mando.

El verdadero reto era comprender todo el ecosistema:

```
Wiimote
   ↓
Bluetooth
   ↓
Linux
   ↓
Drivers
   ↓
Dolphin
   ↓
Configuración de entrada
   ↓
Sensor Bar
   ↓
Juego
```

**Resultados obtenidos**

*Conectividad*

- ✅ Wiimote original conectado mediante Bluetooth.
- ✅ Adaptador Bluetooth dedicado probado.
- ✅ Comunicación estable con Dolphin.
- ✅ Experiencia funcional en Linux.

*Controles*

- ✅ Botones.
- ✅ Nunchuk.
- ✅ Movimiento.
- ✅ Puntero infrarrojo.
- ✅ Sensor Bar.

**Juegos probados**

*New Super Mario Bros Wii*

Resultado: funcionamiento exitoso.

Comprobado:

- Movimiento del personaje.
- Uso del giro.
- Power-Up Helicóptero.

*Super Mario Galaxy*

Resultado: funcionamiento avanzado.

Comprobado:

- Wiimote.
- Nunchuk.
- Movimiento.
- Puntero.

Solucionado: OpenGL → Vulkan.

Pendiente: perfeccionar la precisión del puntero.

**Trabajo futuro**

*1. The Legend of Zelda: Skyward Sword*

Próxima prueba principal.

Objetivo: comprobar Wii MotionPlus, giroscopio, movimientos precisos de espada y orientación absoluta del mando.

Este juego será una prueba definitiva porque MotionPlus no es un accesorio opcional, sino una parte fundamental de la jugabilidad.

*2. MadWorld*

Segunda prueba importante.

Objetivo: evaluar juegos con gran cantidad de movimientos rápidos.

Se analizará:

- Respuesta del acelerómetro.
- Latencia.
- Reconocimiento de gestos.
- Precisión durante movimientos consecutivos.

*3. Optimización del puntero*

Pendiente: encontrar la configuración ideal para conseguir una sensación más cercana a Wii original.

**Reflexión final**

Proyecto Nevada no terminó siendo únicamente un proyecto de conectar un mando antiguo.

Terminó siendo una investigación sobre:

- Bluetooth.
- Linux.
- Emulación.
- Hardware real.
- Sensores.
- Arquitectura de entrada.

Cada problema encontrado aportó información:

- El Wiimote no era simplemente un mando Bluetooth.
- La Sensor Bar no era un sensor.
- Windows no era la solución.
- Real Wii Remote no era obligatoriamente la mejor opción.
- La configuración correcta era la clave.

El proyecto continuará con nuevas pruebas, especialmente con Wii MotionPlus, Zelda Skyward Sword y MadWorld.

Proyecto Nevada queda establecido como una base funcional para seguir explorando la experiencia completa de Nintendo Wii en hardware moderno.
