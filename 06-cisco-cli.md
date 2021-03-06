# Comandos Cisco CLI

Aunque podéis encontrar en Internet infinidad de manuales mucho más completos que este, pretendo compilar aquí las acciones básicas con dispositivos Cisco de la manera más resumida posible, y ajustándose a lo que necesitamos en clase


* [1. Navegación entre los distintos modos](#1-navegación-entre-los-distintos-modos)
* [2. Comando show](#2-comando-show)
* [3. Cambiar el nombre al dispositivo](#3-cambiar-el-nombre-al-dispositivo)
* [4. Proteger con contraseña el modo EXEC privilegiado](#4-proteger-con-contraseña-el-modo-exec-privilegiado)
* [5. Proteger el acceso por consola](#5-proteger-el-acceso-por-consola)
* [6. Establecer un MOTD](#6-establecer-un-motd)
* [7. Encriptar contraseñas en el dispositivo](#7-encriptar-contraseñas-en-el-dispositivo)
* [8. Gestión de la configuración de Inicio](#8-gestión-de-la-configuración-de-inicio)
* [9. Configurar interfaz virtual de un switch (SVI)](#9-configurar-interfaz-virtual-de-un-switch-svi)
* [10. Configurar acceso vía telnet](#10-configurar-acceso-vía-telnet)
* [11. Configurar acceso SSH](#11-configurar-acceso-ssh)
* [12. Configurar dhcp en un router/switch](#12-configurar-dhcp-en-un-routerswitch)
* [14. Configuración de Switches](#14-configuración-de-switches)
    * [14.1. Tabla de direcciones MAC](#141-tabla-de-direcciones-mac)
    * [14.2. Configuración básica de puertos o interfaces](#142-configuración-básica-de-puertos-o-interfaces)
    * [14.3. Establecer seguridad de puertos](#143-establecer-seguridad-de-puertos)
    * [14.4. Configuración de STP](#144-configuración-de-STP)
* [15. Sobre VLANs](#15-Sobre-VLANs)
    * [15.1. Creación de VLANs](#151-Creación-de-VLANs)
    * [15.2. Diagnósticos en VLANs](#152-Diagnósticos-en-VLANs)
    * [15.3. VTP](#153-VTP)
    * [15.4. Enrutado entre VLANs usando un switch multicapa](#154-Enrutado-entre-VLANs-usando-un-switch-multicapa)

                                            
## 1. Navegación entre los distintos modos
Tienes que entender Cisco CLI como un sistema operativo en modo texto para dispositivos Cisco. Tiene infinidad de comandos, y como te puedes imaginar, unos son más delicados que otros en el sentido de que alteran partes comprometidas del sistema. Esto hace necesario plantear un sistema de privilegios.

En Cisco CLI se consigue por medio de los **modos de ejecución**. La siguiente figura muestra un diagrama de estados donde se aprecian los distintos modos, y el comando necesario para pasar de uno a otro:
![Modos en Cisco CLI](imagenes/cisco_cli.svg "Modos de ejecución")

Cuando te conectas a un dispositivo cisco, lo haces en modo **EXEC usuario**. Aquí tienes un conjunto reducido de comandos que **no requieren permisos especiales** (hacer un ping, mostrar alguna configuración básica, ...). 
Mediante el comando **`enable`**, pasas al modo **EXEC privilegiado**, que te permite ejecutar comandos más comprometidos: hacer copias de seguridad de la configuración, arrancar el asistente de configuración, .... Para volver al modo **EXEC usuario**, tienes que ejecutar **`disable`** 
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

Cuando escribamos un comando en este manual, siempre irá acompañado del promt para que sepamos en qué modo hay que ejecutarlo.

Los **comandos para navegar entre un modo u otro**, los tienes en el diagrama anterior.


## 2. Comando show
El comando `show` va acompañado de algún parámetro. Por ejemplo, `show interfaces` muestra la configuración de las interfaces. Sin embargo, estos parámetros que acompañan al comando varían dependiendo del modo (usuario, exec privilegiado, …) en el que nos encontremos.

Para saber qué cosas puedes mostrar en cada momento, usa el operador `?`:
```bash
show ?
```
Esto te devolverá la lista de cosas que puedes mostrar según el **modo** en el que te encuentres.

Algunos ejemplos de cosas a mostrar según el modo:

* En **EXEC de usuario**, mostrar información hardware y software del dispositivo (versión del ios, del procesador, características HW del dispositivo, …)
    ```bash
    asir1a> show version
    ```
* En **EXEC privilegiado**, mostrar configuración de ejecución en RAM (o sea, no guardado)
    ```bash
    asir1a# show running-config
    ```
* En **EXEC privilegiado**, mostrar configuración que se cargará al arrancar el dispositivo
    ```bash
    asir1a# show startup-config
    ```
* En **EXEC privilegiado**, información sobre las interfaces físicas y virtuales disponibles
    ```bash
    asir1a# show ip interface brief
    ```
* En **EXEC privilegiado**, mostrar información sobre las interfaces
    ```bash
    asir1a# show interfaces
    ```

## 3. Cambiar el nombre al dispositivo
Recuerda que debes hacer en modo de **configuración global** (Fíjate en el promt):
* Cambia el nombre del dispositivo
    ```bash
    asir1a(config)# hostname nombre_del_dispositivo
    ```
* Resetea el nombre del dispositivo:
    ```bash
    asir1a(config)# no hostname
    ```

## 4. Proteger con contraseña el modo EXEC privilegiado
Recuerda que en este modo había comandos más *delicados*. Es bueno protegerlo con contraseña para evitar accesos no autorizados.
Hay 2 maneras de establecer contraseña: 1. en texto plano (no seguro), 2. cifrada (seguro)
1. Forma no segura (en texto plano)
    ```bash
    asir1a(config)# enable password contraseña
    ```
2. Forma segura (cifrado)
    ```bash
    asir1a(config)# enable secret contraseña
    ```
El primero método almacena la contraseña en el fichero de configuración en texto plano, y la segunda forma, cifrada. Es evidente que el segundo método es más seguro y recomendable, pero es mi deber enseñarte los 2.

## 5. Proteger el acceso por consola
Hay varias maneras de acceder al dispositivo para configurarlo. Las más comunes son mediante **consola** (línea CTY), y a través de **servicios de red** (líneas VTY) como telnet y ssh. Estos tipos de acceso se conocen en cisco como **líneas (line)**

Acceder por **consola** consiste en conectarte al dispositivo usando un cable de consola, mediante el protocolo RS232. Tambíen es algo crítico que debería ser protegido.

Los comandos para conseguirlo son:
```bash
asir1a(config)# line console 0
asir1a(config-line)# password mi_contraseña
asir1a(config-line)# login
```
* el comando `line console 0` sirve para acceder al modo de configuración de línea de la consola. El cero se utiliza para representar la primera (y en la mayoría de los casos la única) interfaz de consola.
* El comando, `password mi_contraseña`, establece la contraseña.
* El comando `login` configura el dispositivo para que pida esa constraseña al conectar por consola. 
Con esto, la próxima vez que accedamos al dispositivo por consola, se nos pedirá esta contraseña.


## 6. Establecer un MOTD
MOTD significa Message Of The Day. Se trata de una especie de aviso que le sale al usuario cuando se conecta por consola o accede por SSH/Telnet al dispositivo.
Se configura así:
```bash
asir1a(config)# banner motd # mensaje #
```

## 7. Encriptar contraseñas en el dispositivo
El comando de configuración global `service password-encryption` le indica al dispositivo que cifre las contraseñas que se guardan en su archivo de configuración.
```bash
asir1a(config)# service password-encryption
```

## 8. Gestión de la configuración de Inicio
Recuerda que todos los cambios que hagamos, se hacen en memoria y por tanto, son volátiles. A no ser que los guardemos en la nvram del dispositivo, estos cambios se perderán en cuanto lo apaguemos. 
Podemos guardar la configuración actual como configuración de inicio con el siguiente comando:
```bash
asir1a# copy running-config startup-config
```

Podemos borrar la configuración de inicio de la siguiente manera:
```bash
asir1a# erase startup-config
```

Por último, podemos borrar también toda la información de configuración de VLANs que tengamos hecha mediante;
```bash
asir1a# delete vlan.dat
```

## 9. Configurar interfaz virtual de un switch (SVI)
Un switch opera a nivel 2, sin embargo, para propósitos de administración, incluye una **interfaz virtual (SVI)**, para poder administrarlo remotamente por red (vía telnet, ssh, web). Para activar dicha interfaz, ejecutamos los siguientes comandos:
1. Accedemos a la vlan 1, que es la de administración
    ```bash
    asir1a(config)# interface vlan 1
    ```
2. Esteblecemos una ip y una máscara para la interfaz seleccionada en el paso anterior
    ```bash
    asir1a(config-if)# ip address DIRECCION_IP MASCARA_DE_RED
    ```
3. Activamos la interfaz la interfaz
    ```bash
    asir1a(config-if)# no shutdown
    ```
Por ejemplo, para poner a un switch la ip 192.168.21.250:
```bash
asir1a(config)# configure vlan 1 
asir1a(config-if)# ip address 192.168.21.250 255.255.255.0
asir1a(config-if)# no shutdown
```


## 10. Configurar acceso vía telnet
Como hemos dicho antes, hay varias maneras de acceder al dispositivo para configurarlo. Las más comunes son mediante **consola** (línea CTY), y a través de **servicios de red** (líneas VTY) como telnet y ssh. Estos tipos de acceso se conocen en cisco como **líneas (line)**.

Las líneas **VTY** son las líneas de terminal virtual que se usan solamente para controlar las conexiones Telnet/SSH entrantes. Son virtuales ya que no hay hardware relacionado con ellas. 

Para configurar el acceso vía Telnet, recuerda que lo primero es tener configurada una ip para acceder al dispositivo. Si estás trabajando con un switch de nivel 2, necesitas configurar la interfaz svi (ver apartado 6), si por contra, estás con un router, necesitas poner ip a una interfaz física.

Normalmente, hay 5 líneas VTY numeradas de 0 a 4. Piensa en estas líneas como un pool de conexiones permitidas. De manera que, puedes configurar cada una de ellas, incluso con una contraseña distintas. Nosotros por simplicidad, dejaremos todas igual. 
En el siguiente ejemplo, configuramos de la línea 0 a 15.

```bash
asir1a(config)# line vty 0 15 
asir1a(config-line)# password contraseña
asir1a(config-line)# login
asir1a(config-line)# transport input telnet
``` 

Hecho esto, desde otro dispositivo con ip (un host, un switch, un router), podemos hacer telnet al dispositivo anteriormente configurado. Por ejemplo, si hemos configurado la 192.168.1.1:
```bash
PC>telnet 
Trying 192.168.1.1 ...Open
```

## 11. Configurar acceso SSH
Este acceso es más complicado que el acceso telnet. De nuevo, recuerda que lo primero es tener configurada una ip para acceder al dispositivo. Si estás trabajando con un switch de nivel 2, necesitas configurar la interfaz svi (ver apartado 12), si por contra, estás con un router, necesitas poner ip a una interfaz física

Necesitamos crear un usuario (Vamos a exigir autenticación con contraseña local):
```bash
asir1a(config)#username asir1a secret 12345
```
PAra generar las claves RSA (que haremos después), necesitamos que nuestro dispositivo tenga un nombre distinto del genérico (Switch o Router) con el que viene configurado por defecto.

Cambiamos el nombre del dispositivo:
```bash
asir1a(config)# hostname asir_sw
```

Poner a la máquina un nombre de dominio:
```bash
asir1a(config)#ip domain-name iescelia.org
```

Generar claves con encriptación rsa:
```bash
asir1a(config)#crypto key generate rsa
```

Configurar las líneas de acceso:
```bash
asir1a(config)# line vty 0 15
asir1a(config-line)# transport input ssh
asir1a(config-line)# login local
asir1a(config)# exit
```

Una vez hecho esto, desde cualquier pc o dispositivo podemos ejecutar:
```bash
PC>ssh -l asir1a 192.168.1.1
Open
Password: 

asir1c>
```

## 12. Configurar dhcp en un router/switch
Podemos configurar un servidor DHCP en un router, o en un switch que lo permita (en packet tracer, switch 2950-24 no lo permite, pero el 2950T sí). 
Partimos de que tenemos las interfaces del switch (ver apartado 12) o del router (apartado 15.d) configuradas. Vamos a hacerlo para la red 192.168.1.0/24.
En cisco, se establecen las reglas dhcp por ámbitos o pools. Un pool es una configuración dhcp específica y podemos tener varias simultáneas con distintas políticas: distintas redes, rangos de ips, dns, … 
En nuestro ejemplo, crearemos un pool llamado asirPool:
```bash
asir1a(config)#ip dhcp pool asirPool
```
Establecemos la red afectada y su máscara:
```bash
asir1a(dhcp-config)#network 192.168.1.0 255.255.255.0
```
Establecemos el gateway por defecto:
```bash
asir1a(dhcp-config)#default-router 192.168.1.1
```
Establecemos los servidores dns:
```bash
asir1a(dhcp-config)# dns-server 8.8.8.8
```
Excluimos ciertas ips:
```bash
asir1a(dhcp-config)#exit
asir1a(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.10
```
Establecer duración de la concesión (no funciona en packet tracer):
```bash
lease {días [horas] [minutos] infinite} 
```

Para desactivarlo:
```bash
asir1a(config)#no service dhcp
```

Para activarlo:
```bash
asir1a(config)# service dhcp
```

Ver lista de direcciones IP concedidas y sus MACs:
```bash
asir1a# show ip dhcp binding
```
Ver eventos dhcp ocurridos en el servidor:
```bash
asir1a# debug ip dhcp server events
```

## 14. Configuración de switches
En este apartado vamos a centrarnos en aspectos específicos de switches:

### 14.1. Tabla de direcciones MAC
Para ver la **Tabla de direcciones MAC** de un Switch:
```bash
asir1a# show mac-address-table
```

Si quieres borrar las direcciones aprendidas de manera dinámica:
```bash
asir1a# clear mac-address-table
```
### 14.2. Configuración básica de puertos o interfaces
Antes de configurar un puerto o rango de puertos, tenemos que seleccionarlos para entrar en su **modo de configuración específico**. Para ello, desde el modo de configuración global, la sintaxis del comando a ejecutar es:
```bash
asir1a(config)# interface [type] [module/number]
```
Donde:
*   **type**: FastEthernet, GigabitEthernet, ...
*   **module/number**: 0/1, 0/2, 1/1, ... 

Ejemplos:
```bash
asir1a(config)# interface FastEthernet 0/1
```
Podemos seleccionar **rangos de puertos**, con el comando:
```bash
asir1a(config)# interface range type module/primer número – último número
```
Ejemplo:
```bash
asir1a(config)# interface range FastEthernet 0/1-4, FastEthernet 0/10, FastEthernet 0/20-24
```

**Más cosas básicas a configurar:**
* **Añadir una descripción a la interfaz**:
    ```bash
    asir1a (config-if)# description ‘Interfaz para sala 1’
    ```
* **Especificar la velocidad de los puertos**:
    ```bash
    asir1a(config-if)# speed [10 | 100 | 1000 | auto]
    ```
    Por ejemplo, para poner un puerto a 100 Mbps:
    ```bash
    asir1a(config-if)# speed 100
    ```
* **Especificar el modo de comunicación de los puertos**:
    ```bash
    asir1a(config-if)# duplex [auto | full | half]
    ```
    Por ejemplo:
    ```bash
    asir1a(config-if)# duplex full
    ```


### 14.3. Establecer seguridad de puertos
En los apuntes tienes información más completa de todo esto. Lo que se muestra a continuación es básicamente una chuleta.
Recuerda que las fases por las que debes pasar para configurar la seguridad de los puertos son:
1. **Seleccionar el puerto/s a los que vamos a aplicar la configuración**. Ejemplo:
    ```bash
    asir1a(config)# interface FastEthernet 0/1
    ```
2. **Elegir el modo del puerto**: **`access`** o **`trunk`**. El modo por defecto, **`dynamic`**, no nos permite activar la seguridad:
    
    **Modo access** (conexión con dispositivos finales)
    ```bash
    asir1a(config-if)# switchport mode access 
    ```
    **Modo trunk** (conexión con dispositivos de interconexión)
    ```bash
    asir1a(config-if)# switchport mode trunk 
    ```
3. **Activar la seguridad en el puerto/s seleccionados**:
    ```bash
    asir1a(config-if)# switchport port-security
    ```
4. **Configurar aspectos específicos de la seguridad**:

    1. **Establecer el máximo de MACs asociadas a un puerto.**:
    Ej. Para que una interfaz tenga un máximo de 10 direcciones MAC asociadas:
        ```bash
        asir1a(config-if)# switchport port-security maximum 10
        ```
    2. **Asignar una MAC concreta a un puerto**:
        ```bash
        asir1a(config-if)# switchport port-security mac-address 000A.1A3A.A815
        ```
    3. **Configurar Sticky Secure MAC Addresses**:
        ```bash
        asir1a(config-if)# switchport port-security mac-address sticky
        ```
        Para desactivarlo:
        ```bash
        asir1a(config-if)# no switchport port-security mac-address sticky
        ```
    4. **Consultar el estado de la seguridad de un puerto**:
        ```bash
        asir1a# show port-security interface FastEthernet 0/1
        ```
        Otra manera de ver la configuración de un modo más general (no sólo de una interfaz) es:
        ```bash
        asir1a# show port-security interface address
        ```
5. **Acciones a tomar si se viola la política de seguridad**:
Tres modos de actuación:
    
    1. **`shutdown`**: opción por defecto. El puerto se desactiva, y hay que activarlo a mano
        ```bash
        asir1a (config-if) # switchport port-security violation shutdown
        ```
    2. **`restrict`**: el puerto sigue activo, pero rechaza los paquetes desde las direcciones MAC que violen la restricción. Se deja constancia en un servidor de syslog
        ```bash
        asir1a (config-if) # switchport port-security violation restrict
        ```
    3. **`protect`**: igual que `restrict`, pero no deja constancia de lo ocurrido.
        ```bash
        asir1a (config-if) # switchport port-security violation protect
        ```
### 14.4. Configuración de STP
El protocolo **STP** (Spanning Tree Protocol), aunque lo normal es que venga activado por defecto, puede desactivarse.

* Activar STP:
    ```bash
    asir1a# spanning-tree vlan 1
    ```
* Desactivar STP:
    ```bash
    asir1a# no spanning-tree vlan 1
    ```
* Para ver la configuración spanning tree de nuestro switch, utilizaremos:
    ```bash
    asir1a# show spanning-tree
    ``` 

## 15. Sobre VLANs
### 15.1. Creación de VLANs
Para **crear una VLAN o configurar una ya existente**, ejecutamos lo siguiente. Recuerda que una VLAN se identifica por un número y que el **1**, la vlan 1, ya está cogida porque es la vlan por defecto:
```bash
asir1a(config)# vlan número
```

Opcionalmente, puedo **poner un nombre a la VLAN** (recuerda que se identifican por un número). Para ello, dentro de la configuración específica de esa VLAN (es decir, ejecutando previamente `asir1a(config)# vlan número`), ejecuto:
```bash
asir1a(config-vlan) # name mi_vlan_de_prueba
```

Para **asignar puertos o interfaces a una vlan determinada**, dentro del modo de configuración
específico de cada interfaz, ejecutamos lo siguiente: (por ejemplo, para la interfaz FastEthernet
0/1, primero ejecuto `asir1a(config)# interface FastEthernet 0/1` y luego...):
* Opcionalmente, cambiamos el modo del puerto: **trunk** si voy a conectarlo a un switch/router, **access** si voy a conectarlo a un dispositivo final:
    ```bash
    asir1a(config-if) # switchport mode access
    ```
* Y luego metemos esa interfaz en la vlan que deseemos
    ```bash
    asir1a(config-if) # switchport access vlan numero
    ```

### 15.2. Diagnósticos en VLANs
Para obtener información de diagnóstico en una VLAN, básicamente emplearemos 2 comandos:
1. Para obtener información general sobre la base de datos de VLAN del dispositivo (lista de vlans creadas, a qué vlan está asociada cada interfaz, ...):
```bash 
asir1a# show vlan
```

2. Para obtener detalles relativos a una vlan de una interfaz (su modo, el protocolo de encapsulación, su estado, ...): (por ejemplo, para la interfaz FastEthernet0/1)
```bash 
asir1a# show interface FastEthernet0/1 switchport
```

### 15.3. VTP
VTP es un protocolo que nos permite administrar vlans entre varios switches de manera centralizada. Se organiza por **dominios**. Se crea un dominio al que se adhieren los switches que van a ser gestionados de manera centralizada.
En un dominio VTP, los switches pueden desempeñar distintos **roles**:
* **servidor**: puede crear y eliminar vlans.
* **cliente**: es informado de la creación y eliminación de vlans y replica los cambios (los crea/destruye en su base de datos según reciba mensajes para ello procedentes de un servidor vtp de su dominio)
* **switch transparente**: Vive al margen de lo que ocurra en su dominio VTP. Los mensajes de creación/eliminación no le afectan.

![Dominio VTP](imagenes/dominio_vtp.png "Dominio VTP")

#### Funcionamiento
1. Designamos cuál será el servidor vtp.
2. Con el servidor vtp creamos un **dominio vtp** (formado por nombre y contraseña)
3. Designamos que switches serán **clientes vtp**. 
4. Adherimos los clientes al dominio vtp.
5. Desde los servidores VTP se crean las vlan necesarias.


#### Comandos
1. **Configurar un servidor VTP**: le damos al switch el rol de servidor, creamos un dominio vtp y le ponemos una contraseña:
```bash
servidor-vtp(config)# vtp mode server
servidor-vtp(config)# vtp domain 1asir
servidor-vtp(config)# vtp password 123456
```

2. Configuramos un **cliente VTP**: le damos al switch el rol de cliente y lo adherimos a un dominio ya creado:
```bash
vtp-cliente-1(config)# vtp mode client
vtp-cliente-1(config)# vtp domain 1asir
vtp-cliente-1(config)# vtp password 123456
```

3. Configurar un switch **transparente**:
```bash
sw-transparente(config)# vtp mode transparent
```

4. Ver el **estado** de un dominio VTP:
```bash
vtp-cliente-1# show vtp status
```
Ejemplo de salida:
```
VTP Version                     : 2
Configuration Revision          : 4
Maximum VLANs supported locally : 255
Number of existing VLANs        : 7
VTP Operating Mode              : Client
VTP Domain Name                 : 1asir
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0x2C 0xB2 0x70 0x71 0x49 0x5B 0x98 0x34
Configuration last modified by 0.0.0.0 at 3-1-93 00:33:35
```

### 15.4. Enrutado entre VLANs usando un switch multicapa
Las VLANs en un switch (o en un dominio VTP), en principio, son divisiones lógicas aisladas. Podemos enrutar entre ellas de 2 maneras:
1. Usando un router con la técnica *Router on a stick*
2. Usando un **switch multicapa**.

En este apartado, nos centramos en la segunda solución: Switch multicapa:
![Enrutado switch multicapa](imagenes/enrutado_switch_multicapa.svg "Enrutado switch multicapa")

Pasos:
1. Creamos las 2 vlans
```bash
sw-multicapa(config)#vlan 10
sw-multicapa(config-vlan)#exit
sw-multicapa(config)#vlan 20
sw-multicapa(config-vlan)#exit
```

2. Ponemos las interfaces necesarias en modo de acceso y las asociamos a las VLAN:
```bash
sw-multicapa(config)#interface range GigabitEthernet 1/0/1-12
sw-multicapa(config-if-range)#switchport mode access
sw-multicapa(config-if-range)#switchport access vlan 10
sw-multicapa(config-if-range)# exit

sw-multicapa(config)#interface range GigabitEthernet 1/0/13-24
sw-multicapa(config-if-range)#switchport mode access
sw-multicapa(config-if-range)#switchport access vlan 20
```

3. Configuramos las **SVI** (Switch virtual interfaces) en el switch:
```bash
sw-multicapa(config)# interface vlan 10
sw-multicapa(config-if)#ip address 192.168.10.1 255.255.255.0
sw-multicapa(config-if)#no shutdown
sw-multicapa(config-if)#exit

sw-multicapa(config)# interface vlan 20
sw-multicapa(config-if)#ip address 192.168.20.1 255.255.255.0
sw-multicapa(config-if)#no shutdown
sw-multicapa(config-if)#exit
```

4. Activamos enrutado en el switch:
```bash
sw-multicapa(config)#ip routing
```