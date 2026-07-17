# Proyecto-Nevada---Wiimotes-conectados-mediante-Bluetooth-al-emulador-Dolphin
Technical documentation of connecting original Nintendo Wii Remotes to Dolphin Emulator on Linux using Bluetooth, including troubleshooting, configuration, and real hardware testing.
Indice:

# Proyecto Nevada
### Wiimotes conectados mediante Bluetooth al emulador Dolphin

> Technical documentation of connecting original Nintendo Wii Remotes to Dolphin Emulator on Linux using Bluetooth, including troubleshooting, configuration, and real hardware testing.

---

# Introducción

**Proyecto Nevada** nace con un objetivo muy concreto: documentar el proceso real de conectar un Nintendo Wii Remote original al emulador Dolphin en un sistema Linux moderno utilizando Bluetooth y hardware físico.

Aunque existen numerosos tutoriales en Internet, la mayoría están incompletos, utilizan versiones antiguas de Dolphin o simplemente omiten los problemas que aparecen durante la configuración.

Este repositorio no pretende ser únicamente un tutorial paso a paso.

Su propósito es servir como una **bitácora técnica de ingeniería**, registrando los problemas encontrados, las hipótesis planteadas, las pruebas realizadas y las soluciones verificadas durante el desarrollo del proyecto.

La filosofía de Proyecto Nevada es sencilla:

> *Si alguien vuelve a enfrentarse al mismo problema dentro de cinco años, debería poder encontrar aquí toda la información necesaria para comprender qué ocurre y cómo solucionarlo.*

---

# Objetivos

Proyecto Nevada persigue varios objetivos técnicos.

## Objetivo principal

Conseguir que un Nintendo Wii Remote original funcione correctamente en Dolphin Emulator bajo Linux utilizando Bluetooth y hardware físico, intentando reproducir la experiencia de una consola Wii real.

## Objetivos específicos

- Comprender el funcionamiento interno del Wiimote.
- Investigar cómo interactúan Linux, Bluetooth y Dolphin.
- Documentar todos los errores encontrados durante la configuración.
- Registrar las soluciones que realmente funcionan.
- Crear una guía reproducible basada en pruebas reales.
- Evaluar la compatibilidad con diferentes juegos de Nintendo Wii.
- Mantener un historial técnico del proyecto para futuras mejoras.

---

# Filosofía del proyecto

Este repositorio no busca demostrar únicamente que un Wiimote puede conectarse a Dolphin.

Busca comprender **por qué funciona**, **por qué falla** y **qué componentes participan durante todo el proceso**.

Cada error, cada prueba y cada solución forman parte de la documentación.

En lugar de eliminar los problemas encontrados durante la investigación, Proyecto Nevada los conserva como parte del conocimiento generado durante el desarrollo.

# Arquitectura del sistema

Antes de comenzar la configuración es importante comprender qué dispositivos intervienen durante la comunicación.

En una sesión normal participan los siguientes componentes:

- Sistema operativo Linux
- Adaptador Bluetooth
- BlueZ
- Dolphin Emulator
- Nintendo Wii Remote
- Sensor Bar
- Nunchuk (opcional)
- Wii MotionPlus (según el juego)

Cada uno cumple una función distinta.

Comprender esta arquitectura facilita enormemente la resolución de problemas.

# Día 1 - Primer contacto con el Wiimote

El objetivo inicial consistía simplemente en conseguir que Linux detectara correctamente un Nintendo Wii Remote original.

A primera vista parecía un proceso sencillo.

Sin embargo, durante las primeras pruebas aparecieron varios comportamientos inesperados.

## Primer problema

Durante el proceso de emparejamiento el sistema solicitó un código PIN.

Muchos usuarios intentan introducir códigos como:

0000

1234

1111

Sin embargo, en la mayoría de los casos el Wiimote no requiere un PIN tradicional.

Forzar un código puede impedir el emparejamiento correcto.

Este comportamiento depende del método de conexión utilizado y del software que gestione Bluetooth.

Conclusión:

Si Dolphin va a controlar directamente el Wiimote, el procedimiento puede ser diferente al de un dispositivo Bluetooth convencional.

## ¿Por qué Bluetooth es tan importante?

Todo el proyecto depende del adaptador Bluetooth.

No todos los adaptadores presentan el mismo comportamiento.

La calidad del chipset puede determinar si Dolphin consigue comunicarse correctamente con el Wiimote.

Durante esta investigación se trabajó con un adaptador Bluetooth Realtek.

Antes de continuar es recomendable verificar que Linux detecta correctamente el dispositivo mediante:

bluetoothctl: Herramienta de línea de comandos para gestionar Bluetooth en Linux. Al ejecutarlo solo, abre un modo interactivo donde se pueden escribir el resto de comandos sin repetir "bluetoothctl".

list: Muestra los adaptadores Bluetooth disponibles en el equipo (hardware), como el Bluetooth interno o un dongle USB, con su dirección MAC y nombre.

devices: Lista todos los dispositivos Bluetooth que el sistema ha detectado alguna vez, estén o no emparejados actualmente (caché de dispositivos conocidos).

paired-devices: Muestra únicamente los dispositivos que están emparejados con la computadora, es decir, aquellos con los que ya se intercambiaron claves de seguridad.

info <MAC>: Muestra información detallada de un dispositivo específico usando su dirección MAC: nombre, estado de emparejamiento, conexión, servicios soportados (UUIDs), y si es confiable o está bloqueado.

# Conceptos sobre Wiimote en Linux / Retroarch / Dolphin

## Wiimote Emulado (Emulated Wii Remote)

En Dolphin, **Emulated Wii Remote** no significa necesariamente utilizar un teclado o un mando convencional.

Se trata de un modo de funcionamiento en el que **Dolphin interpreta y procesa todas las entradas del control**, independientemente del dispositivo utilizado.

Esto permite utilizar:

- Un teclado y ratón.
- Un gamepad convencional.
- Un Nintendo Wii Remote físico conectado por Bluetooth.
- Otros dispositivos compatibles.

Cuando se utiliza un **Wiimote físico** en este modo, Dolphin recibe los datos enviados por el sistema operativo (Linux, Windows, etc.) y los traduce internamente al comportamiento esperado por la consola Wii.

Este modo ofrece una gran flexibilidad, ya que permite reasignar botones, ajustar sensores, modificar la sensibilidad del puntero e incluso combinar diferentes dispositivos de entrada.

**Durante Proyecto Nevada, esta fue la solución finalmente adoptada**, ya que ofrecía una excelente compatibilidad con el hardware disponible y evitaba algunos de los problemas encontrados con Bluetooth Passthrough.

> **Nota importante**
>
> Muchas personas creen que "Emulated Wii Remote" significa "no utilizar un Wiimote físico". Esto es incorrecto.
>
> En Dolphin, un Nintendo Wii Remote original puede utilizarse tanto en modo **Emulated Wii Remote** como en modo **Real Wii Remote**, dependiendo de cómo se quiera gestionar la comunicación con el mando.
>
> ## Wiimote Real (Real Wii Remote)

En este modo, Dolphin intenta comunicarse directamente con el Nintendo Wii Remote como si fuera una consola Wii real.

El emulador deja de interpretar las entradas y delega gran parte de la comunicación al propio mando mediante Bluetooth.

Este modo suele ofrecer una experiencia muy cercana al hardware original, especialmente con accesorios oficiales como MotionPlus.

Sin embargo, también depende en mayor medida del adaptador Bluetooth, de los controladores del sistema operativo y de la compatibilidad del hardware utilizado.

En algunos equipos puede requerir configuraciones adicionales o presentar problemas de detección y permisos.

Durante Proyecto Nevada también se investigó este modo de funcionamiento para comprender sus ventajas, limitaciones y diferencias frente al Wiimote Emulado.

## Wiimote Real
Control físico original de Nintendo Wii (o third-party compatible). Se conecta por Bluetooth al PC. Ofrece la experiencia auténtica con sensor de movimiento, apuntado y botones físicos. Requiere emparejamiento y configuración adecuada.

## Bluetooth Passthrough
Modo especial en emuladores (como Dolphin) que da acceso directo al adaptador Bluetooth del sistema, saltándose el stack Bluetooth del SO. Mejora la latencia y compatibilidad con Wiimotes reales, especialmente con MotionPlus y periféricos originales, pero impide usar otros dispositivos Bluetooth al mismo tiempo.

## BlueZ
Stack Bluetooth oficial para Linux. Es el encargado de gestionar la detección, emparejamiento y conexión de dispositivos Bluetooth en el sistema. Compatible con Wiimotes, aunque a veces requiere configuraciones extras para funcionar con MotionPlus o múltiples mandos.

## hid_wiimote
Controlador (driver) del núcleo Linux que permite que el sistema operativo reconozca el Wiimote como un dispositivo de entrada estándar (HID). Una vez cargado, el Wiimote puede usarse como joystick genérico en juegos y emuladores sin necesidad de software adicional.

## xwiimote
Biblioteca y conjunto de utilidades en espacio de usuario que ofrece una interfaz más avanzada para manejar Wiimotes en Linux. Permite leer datos de sensores (acelerómetro, giroscopio), manejar extensiones (Nunchuk, Classic Controller) y usar el infrarrojo, todo sin depender de emuladores específicos.

## Sensor Bar
Barra con LEDs infrarrojos que se coloca encima o debajo de la pantalla. El Wiimote usa su cámara frontal para detectar estos puntos de luz y calcular hacia dónde apunta. En PC se puede usar la barra original de Nintendo (conectada por USB o inalámbrica) o barras genéricas de terceros. También se pueden emular con dos velas o LEDs, pero con menor precisión.

## MotionPlus
Accesorio (o integrado en mandos posteriores) que añade un giroscopio de 3 ejes al Wiimote, permitiendo detectar rotaciones y movimientos angulares con mucha mayor precisión. Fundamental para juegos que requieren movimientos exactos (Wii Sports Resort, Zelda Skyward Sword). En Linux requiere soporte del driver (hid_wiimote) o de bibliotecas como xwiimote para leer sus datos correctamente.


# Desarrollo del proyecto

La siguiente sección documenta cronológicamente el proceso de investigación realizado durante Proyecto Nevada.

No pretende ser un tutorial tradicional, sino una bitácora técnica donde se registran los problemas encontrados, las hipótesis planteadas y las conclusiones obtenidas en cada etapa.

---

## Fase 1 – Definición del problema

### Objetivo

Conseguir utilizar un Nintendo Wii Remote original en Linux para jugar títulos de Nintendo Wii desde un ordenador.

### Situación inicial

El punto de partida parecía sencillo: conectar el Wiimote por Bluetooth y utilizarlo como cualquier otro dispositivo inalámbrico.

Sin embargo, muy pronto quedó claro que el Wiimote posee un funcionamiento diferente al de un teclado, un ratón o un mando Bluetooth convencional.

Además, el objetivo del proyecto no consistía únicamente en conectar el mando, sino en conseguir una experiencia de juego lo más cercana posible a una consola Wii real.

### Hipótesis inicial

Inicialmente se asumió que bastaría con:

- Emparejar el Wiimote desde Linux.
- Abrir un emulador compatible.
- Comenzar a jugar.

La investigación demostraría posteriormente que el proceso era considerablemente más complejo.

### Resultado

Esta primera fase permitió definir correctamente el problema.

El verdadero reto no consistía en conectar un dispositivo Bluetooth, sino en comprender cómo interactúan:

- Linux.
- BlueZ.
- El adaptador Bluetooth.
- Dolphin.
- El propio Wiimote.

### Lecciones aprendidas

- El Wiimote no es un dispositivo Bluetooth convencional.
- El método de emparejamiento depende del software que vaya a utilizar posteriormente el mando.
- Conectar el mando no significa que pueda utilizarse inmediatamente en un emulador.

---

## Fase 2 – Primer contacto con el Wiimote

### Objetivo

Conseguir que Linux detectara correctamente un Nintendo Wii Remote original.

### Qué se intentó

Durante esta etapa comenzaron las primeras pruebas reales con el mando.

El sistema operativo detectaba el dispositivo y era posible iniciar el proceso de emparejamiento.

Sin embargo, aparecieron varios comportamientos inesperados.

### Primer problema: solicitud de PIN

Uno de los primeros obstáculos fue que Linux solicitaba un código PIN durante el emparejamiento.

Muchos tutoriales recomiendan introducir valores como:

- 0000
- 1111
- 1234

Durante la investigación se comprobó que esta recomendación no siempre resulta correcta.

El Wiimote no utiliza necesariamente un proceso de autenticación idéntico al de otros dispositivos Bluetooth.

Dependiendo del método de conexión y del software utilizado, el PIN puede incluso impedir el emparejamiento esperado.

### Resultado

Finalmente Linux consiguió detectar correctamente el mando.

Sin embargo, todavía no era posible jugar.

La investigación permitió comprender una diferencia fundamental:

**Bluetooth conectado no significa Wiimote funcionando.**

Un dispositivo puede aparecer correctamente conectado en el sistema operativo y aun así no ser utilizable por un emulador.

### Lecciones aprendidas

- Detectar el Wiimote no garantiza que el emulador pueda utilizarlo.
- La solicitud del PIN depende del método de conexión.
- El objetivo todavía estaba lejos de cumplirse.

---

## Fase 3 – Investigación en RetroArch

### Objetivo

Determinar si RetroArch podía ofrecer una experiencia completa para jugar títulos de Nintendo Wii utilizando un Nintendo Wii Remote original.

### ¿Por qué comenzar con RetroArch?

Antes de utilizar Dolphin Standalone, la intención era mantener toda la biblioteca de emulación dentro de un único entorno.

RetroArch ofrecía varias ventajas importantes:

- Integración con Steam.
- Sincronización de partidas mediante Steam Cloud (save states y archivos `.sav`).
- Una única interfaz para múltiples consolas y emuladores.
- Organización centralizada de toda la colección de juegos.
- Configuración unificada para distintos sistemas.

Desde un punto de vista práctico, mantener toda la emulación en RetroArch simplificaba considerablemente la administración de la biblioteca.

### Primeras pruebas

Las primeras pruebas fueron prometedoras.

Steam detectó correctamente el Nintendo Wii Remote como un dispositivo de entrada.

Como consecuencia, RetroArch también era capaz de reconocer el mando.

Sin embargo, aparecieron inmediatamente las primeras diferencias respecto al comportamiento esperado de una consola Wii real.

### Primer problema detectado

Los botones **1** y **2** aparecían invertidos respecto a su funcionamiento habitual en Wii.

Aunque este problema podía corregirse mediante remapeos, evidenciaba que el Wiimote estaba siendo tratado como un dispositivo de entrada genérico y no como un mando de Wii con soporte específico.

### Investigación del núcleo Dolphin para RetroArch

La siguiente etapa consistió en analizar las posibilidades de configuración del núcleo Dolphin integrado en RetroArch.

Se comprobó que las opciones disponibles eran considerablemente más limitadas que las presentes en Dolphin Standalone.

Entre las configuraciones disponibles se encontraban:

- Wiimote.
- Wiimote horizontal (Sideways Wiimote).
- Wiimote + Nunchuk.
- Classic Controller.
- Classic Controller Pro.

A primera vista parecía suficiente para la mayoría de juegos.

Sin embargo, todas estas configuraciones compartían una limitación muy importante.

### La limitación crítica

Ninguna de las configuraciones disponibles permitía utilizar correctamente:

- el puntero infrarrojo (IR Pointer);
- los sensores de movimiento del Wiimote;
- la emulación completa del comportamiento del mando original.

Como consecuencia, juegos que dependen de estas funciones, como **Super Mario Galaxy**, no podían jugarse de forma adecuada.

RetroArch permitía seleccionar diferentes perfiles de mando, pero no ofrecía el nivel de integración que sí proporciona Dolphin Standalone para el control por movimiento.

### Resultado

Las pruebas demostraron que RetroArch era perfectamente válido para utilizar el núcleo Dolphin en situaciones sencillas.

Sin embargo, cuando el objetivo era reproducir la experiencia completa de una Nintendo Wii utilizando un Wiimote original, sus opciones resultaban insuficientes.

Esta conclusión llevó a replantear completamente la estrategia del proyecto y centrar la investigación en Dolphin Standalone.

### Lecciones aprendidas

- Que un mando sea reconocido por Steam no implica que todas sus funciones estén disponibles en un emulador.
- RetroArch detectaba correctamente el Wiimote, pero no ofrecía una implementación completa de sus características.
- El soporte para sensores de movimiento y puntero infrarrojo era el principal obstáculo.
- Mantener toda la biblioteca en RetroArch seguía siendo una idea atractiva por su integración con Steam Cloud y su interfaz unificada, pero la prioridad pasó a ser conseguir la máxima compatibilidad con los juegos de Wii.
- Para una experiencia auténtica con un Nintendo Wii Remote original, Dolphin Standalone ofrecía herramientas mucho más avanzadas.

---

## Fase 3 – Investigación en RetroArch

### Objetivo

Determinar si RetroArch podía ofrecer una experiencia completa para jugar títulos de Nintendo Wii utilizando un Nintendo Wii Remote original.

### ¿Por qué comenzar con RetroArch?

Antes de utilizar Dolphin Standalone, la intención era mantener toda la biblioteca de emulación dentro de un único entorno.

RetroArch ofrecía varias ventajas importantes:

- Integración con Steam.
- Sincronización de partidas mediante Steam Cloud (save states y archivos `.sav`).
- Una única interfaz para múltiples consolas y emuladores.
- Organización centralizada de toda la colección de juegos.
- Configuración unificada para distintos sistemas.

Desde un punto de vista práctico, mantener toda la emulación en RetroArch simplificaba considerablemente la administración de la biblioteca.

### Primeras pruebas

Las primeras pruebas fueron prometedoras.

Steam detectó correctamente el Nintendo Wii Remote como un dispositivo de entrada.

Como consecuencia, RetroArch también era capaz de reconocer el mando.

Sin embargo, aparecieron inmediatamente las primeras diferencias respecto al comportamiento esperado de una consola Wii real.

### Primer problema detectado

Los botones **1** y **2** aparecían invertidos respecto a su funcionamiento habitual en Wii.

Aunque este problema podía corregirse mediante remapeos, evidenciaba que el Wiimote estaba siendo tratado como un dispositivo de entrada genérico y no como un mando de Wii con soporte específico.

### Investigación del núcleo Dolphin para RetroArch

La siguiente etapa consistió en analizar las posibilidades de configuración del núcleo Dolphin integrado en RetroArch.

Se comprobó que las opciones disponibles eran considerablemente más limitadas que las presentes en Dolphin Standalone.

Entre las configuraciones disponibles se encontraban:

- Wiimote.
- Wiimote horizontal (Sideways Wiimote).
- Wiimote + Nunchuk.
- Classic Controller.
- Classic Controller Pro.

A primera vista parecía suficiente para la mayoría de juegos.

Sin embargo, todas estas configuraciones compartían una limitación muy importante.

### La limitación crítica

Ninguna de las configuraciones disponibles permitía utilizar correctamente:

- el puntero infrarrojo (IR Pointer);
- los sensores de movimiento del Wiimote;
- la emulación completa del comportamiento del mando original.

Como consecuencia, juegos que dependen de estas funciones, como **Super Mario Galaxy**, no podían jugarse de forma adecuada.

RetroArch permitía seleccionar diferentes perfiles de mando, pero no ofrecía el nivel de integración que sí proporciona Dolphin Standalone para el control por movimiento.

### Resultado

Las pruebas demostraron que RetroArch era perfectamente válido para utilizar el núcleo Dolphin en situaciones sencillas.

Sin embargo, cuando el objetivo era reproducir la experiencia completa de una Nintendo Wii utilizando un Wiimote original, sus opciones resultaban insuficientes.

Esta conclusión llevó a replantear completamente la estrategia del proyecto y centrar la investigación en Dolphin Standalone.

### Lecciones aprendidas

- Que un mando sea reconocido por Steam no implica que todas sus funciones estén disponibles en un emulador.
- RetroArch detectaba correctamente el Wiimote, pero no ofrecía una implementación completa de sus características.
- El soporte para sensores de movimiento y puntero infrarrojo era el principal obstáculo.
- Mantener toda la biblioteca en RetroArch seguía siendo una idea atractiva por su integración con Steam Cloud y su interfaz unificada, pero la prioridad pasó a ser conseguir la máxima compatibilidad con los juegos de Wii.
- Para una experiencia auténtica con un Nintendo Wii Remote original, Dolphin Standalone ofrecía herramientas mucho más avanzadas.

# Comprendiendo la Sensor Bar

Uno de los conceptos más importantes aprendidos durante Proyecto Nevada fue el funcionamiento real de la Sensor Bar.

Al principio es fácil asumir que la barra sensora detecta la posición del Wiimote o que se comunica con él mediante Bluetooth.

Ninguna de estas afirmaciones es correcta.

## ¿Qué hace realmente la Sensor Bar?

La Sensor Bar no transmite datos.

No contiene sensores de movimiento.

No conoce la posición del mando.

Su única función consiste en emitir dos grupos de luz infrarroja situados en los extremos de la barra.

## ¿Quién detecta realmente el movimiento?

El verdadero sensor se encuentra dentro del propio Nintendo Wii Remote.

En la parte frontal del mando existe una cámara infrarroja capaz de detectar estos dos puntos luminosos.

A partir de su posición relativa, el Wiimote calcula hacia dónde está apuntando.

Posteriormente transmite esa información a la consola Wii o al emulador mediante Bluetooth.

En otras palabras:

La Sensor Bar únicamente emite luz.

Toda la inteligencia del sistema de apuntado se encuentra dentro del Wiimote.

## Una consecuencia interesante

Como la Sensor Bar únicamente emite luz infrarroja, puede sustituirse por cualquier fuente que genere dos puntos infrarrojos suficientemente visibles.

Durante años la comunidad ha demostrado que incluso dos velas pueden utilizarse para apuntar con el Wiimote.

Las velas no reemplazan la barra porque tengan electrónica.

La reemplazan porque producen dos puntos de luz que la cámara infrarroja del Wiimote puede detectar.

## Influencia en Proyecto Nevada

Comprender este funcionamiento cambió completamente la dirección de la investigación.

Durante el proyecto se disponía de una Sensor Bar original procedente de una Wii U.

Esto significaba que el sistema de apuntado ya estaba resuelto.

El verdadero reto pasaba a ser conseguir que el Wiimote pudiera comunicarse correctamente con Dolphin mediante Bluetooth, aprovechando la barra sensora original para proporcionar el puntero infrarrojo.
