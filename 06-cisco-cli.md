# Comandos Cisco CLI

Aunque podéis encontrar en Internet infinidad de manuales mucho más completos que este, pretendo compilar aquí las acciones básicas con dispositivos Cisco de la manera más resumida posible, y ajustándose a lo que necesitamos en clase

## 1. Navegación entre los distintos modos
Tienes que entender Cisco CLI como un sistema operativo en modo texto para dispositivos Cisco. Tiene infinidad de comandos, y como te puedes imaginar, unos son más delicados que otros en el sentido de que alteran partes comprometidas del sistema. Esto hace necesario plantear un sistema de privilegios.

En Cisco CLI se consigue por medio de los **modos de ejecución**. La siguiente figura muestra un diagrama de estados donde se aprecian los distintos modos, y el comando necesario para pasar de uno a otro:
![Modos en Cisco CLI](imagenes/cisco_cli.svg "Modos de ejecución")

Cuando te conectas a un dispositivo cisco, lo haces en modo **EXEC usuario**. Aquí tienes un conjunto reducido de comandos que **no requieren permisos especiales** (hacer un ping, mostrar alguna configuración básica, ...). 
Mediante el comando **enable**, pasas al modo **EXEC privilegiado**, que te permite ejecutar comandos más comprometidos: hacer copias de seguridad de la configuración, arrancar el asistente de configuración, .... Para volver al modo **EXEC usuario**, tienes que ejecutar **disable** 
En resumen, creo que la figura anterior es bastante explicativa. De todos modos:
* **EXEC usuario**: comandos sin privilegios
* **EXEC privilegiado**: conjunto de comandos de gestión (copias de seguridad de la configuración, visualización de parámetros del dispositivo, ...) que requieren privilegios.
* **Configuración Global**: comandos de configuración global del dispositivo. Ej. cambiar la hora, el nombre del dispositivo, el motd...
* **Configuración específica**: Para configurar elementos individuales: una interfaz de red, una línea vty, un puerto de un switch, ...

Otra cosa importante, es que según el modo en el que estés, cambia el promt del sistema (ese texto que aparece en la consola a la espera de comandos)

Imagina que el nombre de nuestro dispositivo es *asir1a*. En este caso tendremos de promt:
* **asir1a>** en el modo EXEC de usuario
* **asir1a#** en el modo EXEC privilegiado
* **asir1a(config)#** en el modo de configuración global
* **asir1a(config-x)#** en el modo de configuración específico, siendo x el elemento a configurar (una interfaz, un puerto, ...)

Los **comandos para navegar entre un modo u otro**, los tienes en el diagrama anterior.

