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

---

# Fase 4 – Migración a Dolphin y análisis del hardware Bluetooth

## Objetivo

Después de las limitaciones encontradas durante las pruebas con RetroArch, el siguiente paso fue investigar Dolphin Emulator como plataforma principal.

El objetivo era conseguir una experiencia más cercana a una consola Wii real utilizando:

- Nintendo Wii Remote original.
- Sensor Bar original.
- Bluetooth del ordenador.
- Dolphin Emulator.

Durante esta fase se investigaron dos caminos diferentes:

- Emulated Wii Remote utilizando un Wiimote físico.
- Real Wii Remote utilizando comunicación directa con Dolphin.

---

# Primer intento: Emulated Wii Remote

## Objetivo

Utilizar un Nintendo Wii Remote físico conectado al sistema y permitir que Dolphin interpretara sus entradas.

Esta alternativa parecía una solución viable porque permitía:

- Mantener el Wiimote original.
- Configurar manualmente controles.
- Utilizar Dolphin como intermediario entre Linux y el mando.

## Problema encontrado

Durante las primeras pruebas se llegó a un punto donde parecía existir un bloqueo.

El Wiimote podía ser detectado por Linux, pero la experiencia todavía no era completa.

Algunos elementos fundamentales para una experiencia Wii real seguían sin estar resueltos:

- Movimiento.
- Puntero infrarrojo.
- Comunicación completa con Dolphin.

En este momento todavía no se había identificado que existían configuraciones adicionales relacionadas con permisos y acceso al dispositivo Bluetooth.

Como consecuencia, el problema parecía estar relacionado con la comunicación entre Linux y Dolphin.

---

# Investigación del adaptador Bluetooth

## Motivo del cambio de estrategia

Al encontrar limitaciones con la configuración inicial, surgió una nueva hipótesis:

El problema podía estar relacionado con el hardware Bluetooth utilizado.

Aunque el Bluetooth integrado del portátil permitía detectar dispositivos, era necesario comprobar si un adaptador dedicado ofrecía mayor compatibilidad con Dolphin y los Wiimote originales.

Por este motivo se adquirió un adaptador Bluetooth USB adicional.

---

# Hardware Bluetooth utilizado

## Bluetooth interno del equipo

El equipo utilizado durante Proyecto Nevada cuenta con un controlador inalámbrico:

```
Realtek RTL8852BE PCIe 802.11ax Wireless Network Controller
```

Este dispositivo integra conectividad inalámbrica y Bluetooth.

Durante las pruebas Linux detectó un controlador Bluetooth interno:

```
Controller 24:B2:B9:1A:5E:B6
```

Características detectadas:

- Bluetooth versión: 5.x
- Fabricante: Realtek
- Interfaz: PCIe
- Controlador Linux utilizado: Realtek Bluetooth stack mediante btusb

---

## Adaptador Bluetooth USB dedicado

Durante la investigación se incorporó un adaptador externo:

```
Realtek Semiconductor Corp. Bluetooth 5.3 Radio
ID USB: 0bda:a729
```

Características detectadas:

- Interfaz: USB
- Fabricante: Realtek
- Bluetooth: 5.3
- Identificador USB: 0bda:a729

Linux también detectó otro dispositivo Bluetooth Realtek:

```
Realtek Semiconductor Corp. Bluetooth Radio
ID USB: 0bda:4853
```

Esto permitió identificar la presencia de múltiples dispositivos Bluetooth Realtek conectados al sistema.

---

# Estado de los controladores Bluetooth

El sistema Linux detectó correctamente dos controladores Bluetooth independientes:

```
Controller 8A:88:4B:A2:CB:C0
Controller 24:B2:B9:1A:5E:B6
```

Mediante `btmgmt` se confirmó:

## Controlador hci1

```
version 10
manufacturer 93
```

Con soporte para:

- Bluetooth clásico BR/EDR.
- Bluetooth Low Energy.
- Secure Connections.
- Discoverable.
- Bondable.

## Controlador hci0

```
version 11
manufacturer 93
```

Con soporte adicional para:

- Bluetooth Low Energy avanzado.
- Wide Band Speech.
- CIS Central.
- CIS Peripheral.

---

# Investigación realizada

Con la incorporación del adaptador externo se comenzó a comparar el comportamiento entre:

- Bluetooth interno del portátil.
- Adaptador Bluetooth USB dedicado.

El objetivo era determinar si las diferencias de hardware afectaban:

- Detección del Wiimote.
- Emparejamiento.
- Comunicación con Dolphin.
- Compatibilidad con Real Wii Remote.
- Bluetooth Passthrough.

---

# Descubrimiento importante

Durante esta etapa se comprobó que el adaptador Bluetooth era solamente una parte del problema.

Aunque el hardware tiene una influencia importante, la comunicación correcta del Wiimote depende de varios factores:

- Adaptador Bluetooth compatible.
- Controladores del sistema operativo.
- Permisos de acceso.
- Método utilizado por Dolphin.
- Configuración seleccionada dentro del emulador.

La investigación demostró que cambiar hardware podía mejorar la compatibilidad, pero no solucionaba automáticamente todos los problemas.

---

# Lecciones aprendidas

- Un adaptador Bluetooth compatible es necesario, pero no garantiza por sí solo el funcionamiento correcto.
- Los Wiimote originales requieren una comunicación diferente a la de un dispositivo Bluetooth convencional.
- Tener varios adaptadores Bluetooth conectados puede afectar qué controlador utiliza el sistema.
- Antes de cambiar hardware es necesario comprender la configuración del software.
- En proyectos de hardware real, los problemas normalmente aparecen por la interacción entre varios componentes, no por una única causa.

---

---

# Fase 5 – Primer éxito con Real Wii Remote y descubrimiento de la limitación del puntero

## Objetivo

Después de adquirir el adaptador Bluetooth dedicado, el siguiente objetivo fue comprobar si era posible utilizar el modo **Real Wii Remote** de Dolphin.

La hipótesis era que un adaptador Bluetooth dedicado podría ofrecer una comunicación más cercana al hardware original de una consola Wii.

---

# Preparación del entorno

## Desactivación del Bluetooth interno

Para evitar interferencias entre varios controladores Bluetooth, inicialmente se decidió desactivar el Bluetooth integrado del portátil.

El objetivo era utilizar únicamente el adaptador Bluetooth externo dedicado.

Esta decisión permitió controlar mejor qué hardware estaba utilizando Dolphin durante las pruebas.

La configuración buscaba reproducir un escenario más parecido al recomendado para Real Wii Remote:

```
Dolphin
   |
   |
Adaptador Bluetooth dedicado
   |
   |
Nintendo Wii Remote
```

---

# Primer resultado positivo

## Problema inicial

Antes de utilizar el adaptador dedicado, el modo Real Wii Remote de Dolphin no conseguía detectar correctamente los Wiimote disponibles.

Aunque los mandos podían ser reconocidos por Linux, Dolphin no lograba establecer la comunicación esperada en modo Real Wii Remote.

---

## Prueba con el adaptador Bluetooth dedicado

Después de configurar el nuevo adaptador, se realizó una nueva prueba.

Durante el inicio de **New Super Mario Bros. Wii**, se utilizó el botón de sincronización del Wiimote.

Esta vez ocurrió un cambio fundamental:

Dolphin consiguió detectar correctamente los mandos físicos.

No solamente se produjo una conexión Bluetooth.

El Wiimote comenzó a comportarse como un mando real de Wii.

---

# Primera prueba de jugabilidad real

## New Super Mario Bros. Wii

El resultado fue la primera gran victoria del proyecto.

El mando podía controlar el juego correctamente.

Se verificaron funciones como:

- Movimiento del personaje.
- Uso de botones principales.
- Detección del mando como Wiimote real.
- Movimiento mediante inclinación.

Uno de los momentos más importantes fue comprobar el movimiento de giro de Mario.

El control por movimiento funcionaba.

Especialmente, el giro asociado al Power-Up Helicóptero confirmó que:

- El acelerómetro estaba siendo leído correctamente.
- Dolphin recibía información del movimiento.
- El Wiimote físico estaba siendo utilizado de forma funcional.

Este fue el primer punto donde Proyecto Nevada pasó de una investigación de conexión Bluetooth a una experiencia real de juego.

---

# Prueba con Super Mario Galaxy

## Objetivo

Después del éxito con New Super Mario Bros. Wii, el siguiente paso lógico era probar un título que dependiera más intensamente de las capacidades del Wiimote.

Se seleccionó:

**Super Mario Galaxy**

debido a sus requisitos de:

- Puntero infrarrojo.
- Movimiento.
- Nunchuk.
- Acciones específicas mediante apuntado.

---

# Problema encontrado

Aunque Dolphin detectaba correctamente:

- Wiimote.
- Nunchuk.
- Movimiento básico.

La configuración Real Wii Remote todavía no ofrecía el puntero.

El mando funcionaba parcialmente, pero faltaba una de las funciones esenciales de la experiencia Wii:

```
Movimiento:
✅ Funcionando

Botones:
✅ Funcionando

Nunchuk:
✅ Funcionando

Puntero infrarrojo:
❌ No disponible
```

---

# Investigación de la Sensor Bar

Durante esta etapa se comprobó físicamente el funcionamiento de la barra sensora.

Proyecto Nevada contaba con una Sensor Bar original de Wii U.

La barra recibía alimentación correctamente.

Incluso se realizó una comprobación adicional encendiendo la Wii U para verificar que la consola original reconocía correctamente el accesorio.

La Sensor Bar funcionaba.

Sin embargo, esto no solucionaba el problema.

---

# Descubrimiento importante

La investigación permitió comprender una diferencia fundamental:

La Sensor Bar no transmite información al ordenador.

La barra únicamente genera puntos de luz infrarroja.

El Wiimote utiliza su cámara infrarroja para detectar esos puntos y calcular el apuntado.

Por lo tanto:

Tener una Sensor Bar encendida no garantiza que Dolphin pueda utilizar el puntero.

El problema no estaba en la barra.

El problema estaba en la comunicación completa entre:

- Wiimote.
- Dolphin.
- Sistema de entrada.
- Configuración Real Wii Remote.

---

# Resultado de la fase

Esta fase fue un punto de inflexión para Proyecto Nevada.

Se consiguió por primera vez:

✅ Conectar Wiimotes originales mediante Dolphin.  
✅ Utilizar modo Real Wii Remote.  
✅ Controlar juegos reales.  
✅ Usar movimiento mediante acelerómetro.  
✅ Utilizar Nunchuk.  

Pero también apareció una nueva limitación:

❌ El puntero infrarrojo todavía no funcionaba.

La investigación continuaría enfocándose en comprender la diferencia entre conexión del mando y emulación completa de la experiencia Wii.

---

# Lecciones aprendidas

- Un adaptador Bluetooth dedicado puede cambiar completamente la compatibilidad con Real Wii Remote.
- La conexión Bluetooth por sí sola no garantiza una experiencia Wii completa.
- El acelerómetro y el puntero infrarrojo son sistemas independientes.
- Una Sensor Bar funcionando correctamente no significa automáticamente que Dolphin recibirá el puntero.
- La experiencia completa de Wii requiere resolver tres elementos diferentes:
  - Comunicación Bluetooth.
  - Sensores de movimiento.
  - Entrada infrarroja.

---
---

# Fase 6 – Pruebas en Windows y descubrimiento de la configuración correcta

## Objetivo

Después de los problemas encontrados durante la investigación en Linux, se decidió realizar una prueba temporal utilizando Windows.

En ese momento surgió una nueva hipótesis:

> El problema podía estar relacionado con Linux, sus permisos, sus controladores Bluetooth o la forma en que Dolphin interactuaba con el sistema.

El objetivo era comprobar si un entorno diferente permitía obtener una configuración funcional con menos obstáculos.

---

# Cambio temporal de plataforma

## Motivo

Durante las pruebas en Linux aparecieron varios desafíos:

- Configuración de permisos para Bluetooth.
- Diferencias entre BlueZ y Dolphin.
- Problemas iniciales utilizando Real Wii Remote.
- Dificultad para obtener una experiencia completa con movimiento y puntero.

Además, Windows ofrecía una instalación más directa de Dolphin y una configuración inicial aparentemente más sencilla.

Por este motivo se decidió utilizar Windows como entorno de comparación.

---

# Hardware utilizado

## Sistema Windows

**Equipo:**

[Completar especificaciones del equipo Windows]

**Sistema operativo:**

Windows [versión]

## Adaptador Bluetooth

Se utilizó el mismo adaptador Bluetooth dedicado adquirido durante Proyecto Nevada.

```
Realtek Semiconductor Corp.
Bluetooth 5.3 Radio
USB ID: 0bda:a729
```

El objetivo era comprobar si el mismo hardware tenía un comportamiento diferente al utilizar otro sistema operativo.

---

# Primer resultado: Real Wii Remote tampoco funcionó inmediatamente

La primera prueba consistió en repetir la estrategia utilizada anteriormente:

```
Dolphin → Real Wii Remote
```

Sin embargo, el resultado fue inesperado.

El Wiimote físico tampoco era detectado correctamente como Real Wii Remote de manera inmediata.

Esto fue un descubrimiento importante.

La hipótesis inicial de que "Linux era el problema principal" comenzó a perder fuerza.

El comportamiento era similar:

- El hardware Bluetooth funcionaba.
- El Wiimote podía comunicarse.
- Pero la experiencia completa no aparecía automáticamente.

---

# Descubrimiento de Emulated Wii Remote con hardware real

Durante esta etapa se encontró una configuración de Dolphin que utilizaba:

```
Emulated Wii Remote
```

con una configuración predeterminada que incluía funciones esenciales:

- Acelerómetro.
- Movimiento.
- Puntero.
- Configuración compatible con juegos de Wii.

Inicialmente existía una idea equivocada:

> Emulated Wii Remote significa utilizar teclado, mouse o un mando convencional.

Las pruebas demostraron que esto no era correcto.

Un Nintendo Wii Remote físico también puede utilizarse dentro de este modo.

La diferencia no está en el mando utilizado, sino en la forma en que Dolphin procesa la información.

---

# Prueba con Super Mario Galaxy

## Resultado

Con esta configuración fue posible ejecutar Super Mario Galaxy utilizando el Wiimote físico.

Se consiguió:

- Movimiento mediante el Wiimote.
- Uso del Nunchuk.
- Uso del puntero infrarrojo.
- Control suficiente para jugar.

Además ocurrió un avance importante:

El punto de referencia del puntero, que anteriormente permanecía estático, comenzó finalmente a responder al movimiento del mando.

Esto confirmó que:

- La cámara infrarroja del Wiimote estaba funcionando.
- La Sensor Bar estaba siendo utilizada.
- Dolphin estaba recibiendo la información necesaria.

---

# Limitaciones encontradas

Aunque la solución era funcional, todavía existían problemas.

El principal inconveniente fue el comportamiento del puntero.

Problemas observados:

- Puntero inestable.
- Movimiento irregular.
- Necesidad de ajustes adicionales.

La experiencia era jugable, pero todavía estaba lejos de la precisión de una consola Wii original.

---

# Conclusión de la comparación Linux vs Windows

Esta fase permitió corregir una hipótesis inicial.

Durante la investigación se pensó que Linux podía ser la causa principal de los problemas.

Sin embargo, las pruebas demostraron que:

**Windows no proporcionó una solución diferente a Linux.**

La configuración que permitió utilizar el Wiimote correctamente mediante:

```
Wiimote físico + Emulated Wii Remote
```

también estaba disponible en Linux.

El avance no ocurrió por cambiar de sistema operativo.

El avance ocurrió al descubrir la configuración correcta dentro de Dolphin.

---

# Lecciones aprendidas

- Cambiar de sistema operativo no siempre resuelve un problema de configuración.
- Dolphin ofrece múltiples formas de utilizar un Wiimote físico.
- Real Wii Remote no es obligatoriamente la mejor solución.
- Emulated Wii Remote puede utilizar hardware real.
- La configuración del emulador puede ser más importante que el sistema operativo.
- Las hipótesis deben modificarse cuando las pruebas muestran nuevos resultados.

---

---

# Fase 7 – Regreso a Linux y configuración final de rendimiento y puntero

## Objetivo

Después de las pruebas realizadas en Windows, se decidió regresar al entorno Linux original para comprobar si la solución encontrada también podía aplicarse correctamente.

La hipótesis final era:

> El problema no estaba relacionado con Linux, sino con la configuración utilizada dentro de Dolphin.

Esta fase tenía dos objetivos principales:

- Evaluar el rendimiento de Dolphin en el portátil Linux.
- Ajustar la precisión del puntero del Wiimote.

---

# Primera prueba de Super Mario Galaxy en Linux

## Contexto

A diferencia de las primeras pruebas, esta vez se utilizó el portátil principal con mayor capacidad de hardware.

La configuración ya incluía:

- Wiimote físico.
- Sensor Bar original.
- Nunchuk.
- Dolphin Emulator.
- Configuración Emulated Wii Remote con hardware real.

La expectativa era comprobar si la experiencia obtenida en Windows podía reproducirse en Linux.

---

# Problema encontrado: congelación durante Super Mario Galaxy

Durante la partida ocurrió un problema inesperado.

La prueba avanzó correctamente hasta la zona donde Rosalina termina su primera interacción con Mario.

Posteriormente, durante una sección donde era necesario utilizar el giro del Wiimote para romper cristales cerca del anillo estelar, ocurrió una degradación del rendimiento.

Síntomas observados:

- Ralentización fuerte.
- Juego prácticamente congelado.
- Caída de rendimiento durante una escena específica.

Inicialmente parecía que podía tratarse de falta de potencia del equipo.

Sin embargo, la investigación demostró que esa hipótesis era incorrecta.

---

# Investigación del problema gráfico

## Hipótesis inicial

La primera sospecha fue:

> El hardware del portátil no tiene suficiente potencia para emular correctamente Wii.

Esta hipótesis parecía razonable debido a la carga gráfica de Super Mario Galaxy.

Sin embargo, al analizar la configuración se encontró que Dolphin estaba utilizando:

```
OpenGL
```

como backend gráfico.

---

# Solución encontrada: cambio a Vulkan

Se realizó el cambio del backend gráfico:

```
OpenGL → Vulkan
```

Después del cambio:

- La ralentización desapareció.
- El juego continuó funcionando correctamente.
- La experiencia fue mucho más estable.

Esto confirmó que el problema no era la potencia del equipo.

La causa estaba relacionada con la API gráfica utilizada por Dolphin.

---

# Ajuste del puntero infrarrojo

## Problema encontrado

Aunque el juego funcionaba, el puntero todavía presentaba cierta inestabilidad.

El movimiento del cursor no era completamente natural y podía presentar pequeñas desviaciones.

---

# Investigación del comportamiento del puntero

Durante las pruebas se analizó la relación entre:

- Cámara infrarroja del Wiimote.
- Sensor Bar.
- Acelerómetro.
- Configuración de Dolphin.

Se descubrió que una dependencia excesiva del acelerómetro podía introducir movimientos no deseados.

El acelerómetro sirve para detectar inclinaciones y movimientos del mando, pero no debe utilizarse como sustituto del sistema infrarrojo para apuntar.

---

# Configuración aplicada

Para mejorar la estabilidad:

- La dependencia del acelerómetro para el puntero fue reducida prácticamente a cero.
- Se estableció un valor aproximado de:

```
Dependencia del acelerómetro:
0% - 1%
```

El objetivo era permitir que:

```
Puntero = Sensor infrarrojo
```

en lugar de:

```
Puntero = Sensor infrarrojo + correcciones constantes del acelerómetro
```

---

# Resultado final de la fase

Después de estos ajustes se consiguió una mejora significativa:

✅ Super Mario Galaxy funcionando en Linux.  
✅ Wiimote físico funcionando.  
✅ Movimiento funcionando.  
✅ Nunchuk funcionando.  
✅ Puntero mejorado.  
✅ Rendimiento estable utilizando Vulkan.

---

# Conclusión de Proyecto Nevada hasta esta etapa

Las pruebas realizadas demostraron que el problema inicial no era Linux.

El verdadero desafío estaba en comprender la interacción entre:

- Hardware Bluetooth.
- Drivers del sistema.
- Configuración de Dolphin.
- Método de entrada utilizado.
- Backend gráfico.
- Sensores del Wiimote.

La solución final no fue cambiar de sistema operativo.

La solución fue comprender correctamente cada componente del sistema.

---

# Lecciones aprendidas

- Un problema de rendimiento no siempre significa falta de potencia.
- La API gráfica utilizada por el emulador puede cambiar completamente la experiencia.
- Vulkan ofrece mejores resultados en esta configuración que OpenGL.
- El puntero del Wiimote depende principalmente del sistema infrarrojo.
- El acelerómetro debe utilizarse para movimiento, no como sustituto del puntero.
- Una configuración correcta puede ser más importante que cambiar de hardware.

---
