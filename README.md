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

Fase 1 – Definición del problema
Objetivo

Conseguir utilizar un Nintendo Wii Remote original en Linux para jugar títulos de Nintendo Wii desde el PC.

Hipótesis inicial

Al principio se asumió que el proceso sería similar al de cualquier otro dispositivo Bluetooth.

Resultado

La investigación demostró rápidamente que el Wiimote utiliza un comportamiento Bluetooth particular y que no puede tratarse exactamente igual que un teclado, un ratón o un mando convencional.

Lecciones aprendidas
El Wiimote no es un dispositivo Bluetooth "común".
El método de emparejamiento depende del software que vaya a utilizar el mando.
Fase 2 – Primer contacto con el Wiimote
Objetivo

Conseguir que Linux detectara correctamente el mando.

Qué se intentó
Emparejar el Wiimote desde Linux.
Permitir que el sistema operativo lo reconociera.
Resolver la solicitud del código PIN.
Resultado

El Wiimote consiguió ser detectado por el sistema operativo.

Sin embargo, la detección del dispositivo no garantizaba que pudiera utilizarse correctamente desde un emulador.

Lecciones aprendidas
Detectar el Wiimote no significa que esté listo para jugar.
El código PIN puede aparecer dependiendo del método utilizado.
No todos los métodos de emparejamiento producen el mismo resultado.
Fase 3 – Investigación en RetroArch
Objetivo

Utilizar el Wiimote dentro de RetroArch.

Qué se intentó

La intención inicial era mantener toda la experiencia dentro de RetroArch para aprovechar el ecosistema ya configurado.

Se realizaron diferentes pruebas para conseguir que RetroArch reconociera el mando.

Resultado

Aunque el Wiimote podía conectarse al sistema operativo, RetroArch no llegó a ofrecer una experiencia funcional con el hardware disponible.

Esto llevó a replantear la estrategia del proyecto.

Lecciones aprendidas
Un dispositivo Bluetooth correctamente conectado no implica compatibilidad con todos los emuladores.
Cada emulador implementa el soporte para Wiimote de manera diferente.
Fue necesario buscar una alternativa especializada.
Fase 4 – Migración a Dolphin
Objetivo

Investigar el soporte nativo que ofrece Dolphin para Nintendo Wii.

Qué se descubrió

Durante esta etapa apareció uno de los conceptos más importantes del proyecto:

Emulated Wii Remote.
Real Wii Remote.

Inicialmente ambos conceptos parecían describir tipos distintos de mandos.

La investigación demostró que en realidad representan dos formas diferentes en las que Dolphin procesa la comunicación con el control.

Lecciones aprendidas
Un Wiimote físico también puede utilizarse como Emulated Wii Remote.
La diferencia no está en el hardware, sino en cómo Dolphin gestiona la entrada.
Fase 5 – Investigación del Bluetooth
Objetivo

Comprender por qué algunos métodos funcionaban y otros no.

Qué se investigó
BlueZ.
Adaptadores Bluetooth.
Drivers del kernel.
Bibliotecas especializadas.

También se analizaron las diferencias entre distintos chipsets Bluetooth.

Resultado

Se comprobó que el adaptador Bluetooth tiene un impacto directo sobre la compatibilidad con Dolphin.

No todos los adaptadores ofrecen el mismo comportamiento.

Lecciones aprendidas
El hardware Bluetooth importa.
La calidad del chipset puede modificar completamente la experiencia.
Fase 6 – Bluetooth Passthrough
Objetivo

Evaluar si Bluetooth Passthrough ofrecía mejores resultados.

Qué ocurrió

Se investigó el funcionamiento interno de Bluetooth Passthrough.

Aunque este modo intenta reproducir el comportamiento de una consola Wii real, también introduce nuevos requisitos relacionados con el adaptador Bluetooth y los permisos del sistema.

Resultado

En el hardware utilizado durante Proyecto Nevada, esta no terminó siendo la mejor solución.

Finalmente se optó por utilizar Emulated Wii Remote con un Wiimote físico.

Lecciones aprendidas

Bluetooth Passthrough no es necesariamente la mejor opción.

La solución depende del hardware disponible.

Fase 7 – Sensor Bar
Objetivo

Comprender el funcionamiento del apuntado.

Qué se descubrió

Uno de los mayores malentendidos consiste en pensar que la Sensor Bar envía información al ordenador.

En realidad sucede lo contrario.

La Sensor Bar únicamente emite luz infrarroja.

Es el Wiimote quien detecta dicha luz utilizando su cámara infrarroja integrada.

Lecciones aprendidas

La Sensor Bar no transmite datos.

Toda la inteligencia del sistema de apuntado se encuentra dentro del propio Wiimote.

Fase 8 – Primera prueba exitosa
Objetivo

Conseguir controlar un juego real.

Resultado

Por primera vez el Wiimote consiguió utilizarse correctamente en Dolphin.

El sistema reconocía:

botones;
Nunchuk;
puntero (con la barra sensora).

La experiencia se acercaba significativamente a la de una consola Wii real.

Lecciones aprendidas

El verdadero objetivo nunca fue conectar el mando.

El objetivo era jugar.

Fase 9 – MotionPlus
Objetivo

Comprobar la compatibilidad con juegos que utilizan MotionPlus.

Qué ocurrió

Durante las pruebas se comprobó que algunos títulos, como The Legend of Zelda: Skyward Sword, requieren MotionPlus para poder jugar correctamente.

No se trataba de un error de Dolphin ni del proceso de conexión, sino de un requisito propio del juego.

Lecciones aprendidas

No todos los juegos utilizan el mismo conjunto de funciones del Wiimote.

Es necesario verificar los requisitos específicos de cada título antes de asumir que existe un fallo en la configuración.
