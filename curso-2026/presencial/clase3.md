# Clase 3

## Temas:
- 8. Gestión de redes
    - IP estática
    - forward
    - fijar nombres de hosts
- 9. SSH
- 10. Gestión de dispositivos de almacenamiento

# Gestión de redes

Una red de computadoras es un conjunto de equipos y software conectados entre sí por medio de dispositivos físicos que envían y reciben impulsos eléctricos, ondas electromagnéticas o cualquier otro medio para el transporte de datos, con la finalidad de compartir información, recursos y ofrecer servicios. 

Sin una forma de encaminar el tráfico de datos de un lugar a otro, la comunicación en red sería imposible, por lo cual, es necesaria la implementación de ciertos protocolos o reglas. 

El Protocolo de Control de Transmisión (TCP, Transmission Control Protocol) y el Protocolo de Internet (IP, Internet Protocol), o TCP/IP para abreviar se han convertido en los protocolos predominantes de Internet.

Algunos conceptos de redes:

- TCP es un protocolo que se encarga de una entrega fiable de la información de una red a otra.

- IP: es responsable de entregar nuestros paquetes a los equipos correctos. Nos ayuda a encaminar la información. 

- Los dispositivos que pueden conectarse a una red tienen un identificador único llamado dirección MAC. 
	- Las direcciones MAC son permanentes y están codificadas en un dispositivo. 
	- Además para poder conectarse a la red el dispositivo debe disponer de una dirección IP.

- Los datos se envían a través de una red por medio de paquetes. Cuando movemos datos a través de la red, los dividimos en paquetes. Cuando un paquete llega a su destino, se volverá a ordenar.

- Servidores y clientes. Un servidor es algo que proporciona datos a algo que los solicita, el cliente. 

## Dirección IP

Según [wikipedia](https://es.wikipedia.org/wiki/Direcci%C3%B3n_IP):

La dirección IP es un conjunto de números que identifica, de manera lógica y jerárquica, a una interfaz en la red de un dispositivo (computadora, laptop, etc) que utilice el protocolo IP (Internet Protocol) o que corresponde al nivel de red del modelo TCP/IP.

La dirección IP puede cambiar a menudo debido a cambios en la red, o porque el dispositivo encargado dentro de la red de asignar las direcciones IP decida asignar otra IP (por ejemplo, con el protocolo DHCP). A esta forma de asignación de dirección IP se le denomina también dirección IP dinámica. Los sitios de Internet que por su naturaleza necesitan estar permanentemente conectados, generalmente tienen la necesidad de una dirección IP fija (IP fija o IP estática). Esta no cambia con el tiempo.

Los dispositivos se conectan entre sí mediante sus respectivas direcciones IP. Sin embargo, para las personas es más fácil recordar un nombre de dominio que los números de la dirección IP. Los servidores de nombres de dominio (DNS), "traducen" el nombre de dominio en una dirección IP. Si la dirección IP dinámica cambia, es suficiente actualizar la información en el servidor DNS. El resto de las personas seguirán accediendo al dispositivo por el nombre de dominio.

Actualmente, se usan 2 versiones de direcciones IP: IPv4 e IPv6 (pensada para reemplazar a IPv4). Nosotros nos vamos a centrar en IPv4.

### IPv4:

Las direcciones IPv4 se expresan mediante un número binario de 32 bits permitiendo un espacio de direcciones de hasta 4.294.967.296 (2^32) direcciones posibles. 

Una dirección IP está compuesta por 4 octetos y cada octeto se describe normalmente en números decimales. 
- Ocho bits de datos, o un solo octeto, pueden representar todos los números decimales del 0 al 255 (0-255.0-255.0-255.0-255). 
	- La dirección 12.34.56.78 es una IP válida. 
	- La dirección 123.456.789.100 tiene números más grandes que los que se podrían representar mediante 8 bits.

```
D        0.       0.       0.       .0
B 00000000.00000000.00000000.00000000
B 11111111.11111111.11111111.11111111 -> 32 bits
D     255.     255.     255.     255 
```

Las direcciones inicial y final dentro de una red representan la dirección de la red (la que la identifica) y la de broadcast (envía información a todos los nodos), respectivamente y no pueden asignarse a ningún equipo en particular.

Las direcciones IP se pueden dividir en dos secciones, la ID de red y la ID de host. 

Siguiendo esta idea inicialmente las redes se dividieron en 4 clases según el número de octetos que definia la red (classfull). 

- Las direcciones de clase A son aquellas en las que se utiliza el primer octeto para la ID de red, y los tres últimos se utilizan para la ID de host. 

- Las direcciones de clase B son las que utilizan los dos primeros octetos para la ID de red, y los dos segundos se utilizan para la ID de host. 

- Las direcciones de clase C son aquellas donde se utilizan los primeros tres octetos para la ID de red y solo se utiliza el octeto final para la ID de host. 

Pero, como dice [wikipedia](https://es.wikipedia.org/wiki/Direcci%C3%B3n_IP#Direcciones_IP):

> El diseño de redes de clases (classful) sirvió durante la expansión de internet, sin embargo no era escalable y frente a una gran expansión de las redes en la década de los noventa, el sistema de espacio de direcciones de clases fue reemplazado por una arquitectura de redes sin clases (Classless Inter-Domain Routing - CIDR) en el año 1993. 
> CIDR está basada en redes de longitud de máscara de subred variable (variable-length subnet masking VLSM), lo que permite asignar redes de longitud de prefijo arbitrario. Permitiendo por tanto una distribución de direcciones más fina y granulada, calculando las direcciones necesarias y "desperdiciando" las mínimas posibles.

La máscara de red permite distinguir dentro de la dirección IP, los bits que identifican a la red y los bits que identifican al host. 

La máscara se forma poniendo en 1 los bits que identifican la red y en 0 los bits que identifican al host. De esta forma tenemos 2 maneras de identificar a la máscara de red: 

- Indicando el número de "1" que forman a la misma. Ej: Si en una red sólo el último octeto de bits representa a los hosts entonces la máscara está comprendida por los otros 3 octetos, es decir 3*8 = 24

- Indicando el valor decimal correspondiente a considerar los bits de la máscara de red. Para el caso anterior, si los 3 primeros octetos comprenden la máscara de red entonces los valores decimales serán 255.255.255.0.

El espacio de direcciones de una red puede ser subdividido a su vez creando subredes autónomas separadas. Por ejemplo para agrupar todos los hosts dentro de un área determinada. En este caso crearíamos una subred que englobara las direcciones IP de estos.

Ejemplo:

En una red 192.168.0.0/24 tenemos: 256 hosts (2^8) - 2 (una para identificar la red y otra para broadcast)
	- Hosts: 192.168.0.1 (router), 192.168.0.2, 192.168.0.3, ..., 192.168.0.254

Si tenemos el mismo espacio de direcciones (192.168.0.0/24) podemos dividir la red en subredes más pequeñas. Supongamos que necesitamos armar redes de oficinas en una empresa y queremos que cada una tenga su propia subred de, al menos, 40 IPs cada una:
	- Podemos dividir la red en subredes /26 o 255.255.255.192:
		- 192.168.0.0 – 192.168.0.63: la primera IP es la dirección de red, y la última es la dirección de broadcast. Las IP que están en el medio son para los hosts (62)
		- 192.168.0.64 – 192.168.0.127
		- 192.168.0.128 – 192.168.0.191
		- 192.168.0.192 – 192.168.0.255

## Redes en Linux

### Localización de archivos de configuración de red

- /etc/hosts: Este es un archivo de texto que asocia direcciones IP con nombres de dominio, una linea por dirección IP. Por cada host, hay una linea con: `IP hostname [alias...]`

- /etc/resolv.conf: "resolver" es un set de rutinas que proveen acceso al sistema de dominio de nombres (DNS, Internet Domain System). El archivo de configuración resolv.conf contiene información que es leida por esta rutina la primera vez que es invocada por el proceso 

- /etc/nsswitch.conf: El archivo de configuración Name Service Switch (NSS) es usado por librerias y otras aplicaciones para determinar fuentes desde donde obtener información de servicios y en que orden. Cada servicio es identificado por el nombre de la base de datos. En la línea host: files dns quiere decir que ante una pregunta de un nombre de dominio primero la buscará en files (en /etc/hosts) y luego si no la encuentra irá al servidor DNS configurado.

- /etc/network/interfaces: permite definir los nombres lógicos de las interfaces de redes, a los que se les asociará una configuración determinada. 

- /etc/netplan/* Ubuntu: Archivos yaml con info de como manejar las redes /etc/netplan/

### Comandos de redes con iproute2:

#### Listado de interfaces de red:

Para listar las interfaces de red que tenemos en el equipo:

```
ip link 

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:39:f8:a8 brd ff:ff:ff:ff:ff:ff
```

Para ver la información de nuestras interfaces de red podemos usar cualquiera de los siguientes comandos
```
ip address
ip addr
ip a

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:39:f8:a8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.18/24 brd 192.168.0.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 2800:810:4fd:86c6::4/128 scope global dynamic noprefixroute 
       valid_lft 2931sec preferred_lft 1131sec
    inet6 2800:810:4fd:86c6:a00:27ff:fe39:f8a8/64 scope global dynamic mngtmpaddr noprefixroute 
       valid_lft 2087931sec preferred_lft 2087931sec
    inet6 fe80::a00:27ff:fe39:f8a8/64 scope link 
       valid_lft forever preferred_lft forever

```
- lo ...: interfaz de loopback: Esta dirección se suele utilizar cuando una transmisión de datos tiene como destino el propio host. También se suele usar en tareas de diagnóstico de conectividad y validez del protocolo de comunicación.
- enp0s3 ...: interfaz de red física (ethernet). 
	- state UP
	- link/ether 08:00:27:39:f8:a8 brd ff:ff:ff:ff:ff:ff: MAC del dispositivo
	- inet 192.168.0.18/24 brd 192.168.0.255 scope global dynamic enp0s3: 
		- inet: IPv4
		- 192.168.0.18/24: Dirección IP / máscara de red
		- brd 192.168.0.255: Broadcast
		- dynamic: IP dinamica, es decir, que la IP es suminstrada por un servidor DHCP que asigna IPs a los hosts de la red
	- inet6: IPv6
		- IP global
		- IP global temporal (privacidad)
		- IP red local

Para ver información de una interfaz especifica podemos ejecutar:

```
ip a show enp0s3

2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:39:f8:a8 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.18/24 brd 192.168.0.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 2800:810:4fd:86c6::4/128 scope global dynamic noprefixroute
       valid_lft 2770sec preferred_lft 970sec
    inet6 2800:810:4fd:86c6:a00:27ff:fe39:f8a8/64 scope global dynamic mngtmpaddr noprefixroute
       valid_lft 2087927sec preferred_lft 2087927sec
    inet6 fe80::a00:27ff:fe39:f8a8/64 scope link
       valid_lft forever preferred_lft forever
```

#### Analizar la información de nuestra tabla de enrutamiento:
```
ip route # tambien 'ip ro' o 'ip r' 

default via 192.168.0.1 dev enp0s3 proto static
192.168.0.0/24 dev enp0s3 proto kernel scope link src 192.168.0.18
```

#### Activar/desactivar una interfaz de red

```
ip link set enp0s3 up
ip link set enp0s3 down
```

#### Configurar una dirección IP en una interfaz

```
ip addr add 192.168.0.19/24 dev enp0s3 brd +
```

#### Eliminar una dirección IP de una interfaz.

```
ip addr del 192.168.0.19 dev enp0s3 
```

#### Agregar una puerta de enlace por defecto

```
ip route add default via 192.168.0.1 
```

Todas las configuraciones realizadas con estos comandos son temporarios, es decir, al reiniciar la máquina toda la configuración se pierde. Para que la configuración sea persistente es necesario escribir los cambios en archivos de configuración.

### Configuración persistente de interfaces de red

En el caso de Debian y derivados esta tarea se lleva a cabo a través del archivo /etc/network/interfaces.

```bash
# The primary network interface
allow-hotplug enp0s3 
iface enp0s3 inet static
	address 192.168.0.100
	network 192.168.0.0
	netmask 255.255.255.0
	gateway 192.168.0.1
```
- Líneas que empiezan con #: representan comentarios
- `allow-hotplug enp0s3`: Las interfaces que aparecen con allow-hotplug se activan cuando se producen eventos hotplug (en caliente), como la detección de la placa de red por parte del kernel, la conexión del cable de red, etc
- Líneas iface: sirven para definir nombres lógicos de interfaces de red junto con su configuración particular
- address IP: indica la IP asignada al dispositivo
- network IP: indica la IP de la red
- netmask máscara: indica la máscara de la red
- gateway IP: indica la puerta de enlace

Con estas modificaciones ponemos la interfaz de red con IPs estática. En este punto es necesario reiniciar el servidor para que tome la configuración.

Si la interface aparece apagada (down) la iniciamos con:

```bash
ip link set dev enp0s3 up
```

### Configuración IP estática en Ubuntu (a partir de la versión 18.04):

Para configurar la red en Ubuntu debemos modificar los archivos /etc/netplan/*.

Por ejemplo:

```bash
vi /etc/netplan/00-installer-config.yaml

network:
  ethernets:
    enp2s0:
      dhcp4: no
      addresses: [<IP estatica>/<Mascara de red>]
      gateway4: <IP Gateway>
      nameservers:
              addresses: [8.8.8.8,8.8.4.4]
    enp3s0:
      dhcp4: true
  version: 2
```
Luego de editar el archivo ejecutamos:

```bash
netplan apply
```

#### Configuración modo router:

`vi /etc/sysctl.conf`
Descomentamos `net.ipv4.ip_forward=1` y `net.ipv6.conf.all.forwarding=1` para permitir el trafico IPv4 y 6. Luego, ejecutamos:
`iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE`
Lo anterior no es persistente, ya que al reiniciar el dispositivo se pierde la configuración.

Lo que hay que hacer es instalar iptables-persistent y agregar la configuración al archivo rules.v4 en /etc/iptables/rules.v4:
`apt-get install iptables-persistent`
Contenido de /etc/iptables/rules.v4:
```bash
*nat
...
:OUTPUT ACCEPT [0:0]
-A POSTROUTING -o enp0s3 -j MASQUERADE
...
```

# SSH

## Instalar OpenSSH (servidor y cliente)

```
sudo apt update && sudo apt install openssh-server openssh-client
```

## Conectar con el servidor ssh
```
ssh -p 22 username@server_ip        # => Ex: ssh -p 2267 john@192.168.0.100
ssh -p 22 -l username server_ip
ssh -v -p 22 username@server_ip     # => verbose
```

## ssh-key

```
ssh-keygen -t ecdsa -b 521
# Seleccionar archivo donde se guarda (por defecto ~/.ssh/id-<algoritmo>)
# Poner contraseña (2 veces)
ssh-copy-id -i ~/.ssh/file-key-ecdsa user@host
```
Con esto tenemos un método de conexion más seguro. Sin embargo, cada vez que queramos conectarnos al servidor al que pasamos la clave publica vamos a tener que ingresar la contraseña. Para evitar esto podemos:
- Dejar la contraseña en blanco cuando creamos las claves, pero esto no es una buena práctica ya que si alguien tiene acceso fisico a la PC donde están almacenadas ambas claves puede ingresar al servidor. Sin embargo, puede ser útil entre maquinas que se manejan en una red privada (por ejemplo, los nodos de un cluster HPC)
- Usar ssh-agent y ssh-add:
    - Primero ejecutamos: `eval $(ssh-agent)`
    - Segundo: `ssh-add`. Aca nos pide la contraseña que usamos al crear las claves y ya podemos acceder a los servidores que tienen la clave pública sin contraseña mientras la terminal permanezca abierta.

## Seguridad del demonio SSHd

Para cambiar la configuración por defecto se modifica el archivo /etc/ssh/sshd_config y se reinicia el servidor

a) Cambiar puerto de escucha
Port 2278
 
b) Deshabilitar login como root
PermitRootLogin no
 
c) Limitar el acceso de usuarios por SSH
AllowUsers stud u1 u2 john
 
d) Filtrar accesso a nivel del firewall (iptables)
 
e) Activar autenticación por clave pública (Public Key Authentication) y deshabilitar la autenticación por clave
 
f) Usar sólo la version 2 del protocolo
 
g) Otras configuraciones:
ClientAliveInterval 300 
ClientAliveCountMax 0
MaxAuthTries 2
MaxStartUps 3
LoginGraceTime 20

Para obtener ayuda:
```
man sshd_config
``` 

# Gestión de dispositivos de almacenamiento

Para llevar a cabo esta parte del curso vamos a crear 3 discos de 500MB (dependiendo el espacio de la computadora) en nuestra máquina virtual. 

En VirtualBox seleccionamos nuestra máquina virtual (apagada) y después vamos a la configuración (ícono del engranaje),
	- vamos a almacenamiento y sobre el menú "controlador: SATA" seleccionamos agregar disco duro. 
	- Se nos abre una nueva ventana, seleccionamos crear y se vuelve a abrir otra ventana donde vamos a crear el disco. 
		- Seleccionamos la prime opción "VDI ..." y siguiente, tamaño fijo y siguiente, ponemos el nombre y el tamaño, crear. 
		- Esto genera un disco que va a figurar en "not attached", lo seleccionamos y clickeamos seleccionar. 

Repetimos la operación para generar los otros discos y finalmente seleccionamos aceptar para guardar los cambios.

En un sistema Linux hay solamente una estructura de directorios sin importar de cuántos dispositivos de almacenamiento físico dispongamos. Pero para poder utilizarlos, es necesario que primero asociemos a cada uno con un directorio del sistema. Este directorio recibe el nombre de punto de montaje.

## Definiciones:
Una partición es una división lógica que se hace a un dispositivo de almacenamiento con el propósito de definir un espacio determinado para un uso en particular. 

Un sistema de archivos puede definirse como la forma en que se estructurarán los contenidos (archivos y directorios) de dicha partición.

En cualquier sistema Linux el espacio utilizado por ciertos directorios (/home y /var, por nombrar dos ejemplos) puede llegar a aumentar o variar constantemente. El crear una partición dedicada para cada uno de ellos presenta dos ventajas al menos:

- Limitar el espacio que pueden ocupar sus contenidos (de manera que su crecimiento no perjudique el de otros directorios)
- Impedir que una partición llena afecte críticamente el funcionamiento de todo el sistema.

## En cuanto a los sistemas de archivos (es lo que uno hace cuando formatea el disco), los más utilizados hoy en día son ext4 y xfs.

### FAT32 - VFAT:
- Es antiguo y sencillo, por eso es el más compatible con cualquier sistema operativo o dispositivo hardware. Su principal inconveniente es que no puede almacenar archivos de más de 4 GB y las particiones no pueden ser mayores de 2 TB.

### NTFS
- Es el sucesor de FAT32, desarrollado por Microsoft. Elimina el máximo de 4GB en ficheros y 2TB de partición. Incluye más funcionalidades: permisos, cifrado, copias de seguridad, cuotas, etc.. Es menos compatible que FAT32

### EXT4:
- Es la última versión del sistema ext usado en Linux.
- Tamaño máximo de sistema de archivos (generalmente se extiende a toda la partición): 1 EiB = 1024 PiB (1 PiB = 1024 TiB, 1 TiB = 1024 GiB)
- Tamaño máximo de archivos: 16 TiB
- Cantidad máxima de subdirectorios: 64.000
- Journaling: permite la recuperación de archivos que estaban siendo escritos en el momento de un fallo del sistema. Si bien esta característica no elimina totalmente las posibilidades de encontrar archivos corruptos, sí contribuye a la confiabilidad del sistema de archivos y a la rapidez del chequeo de los mismos.
- Permite aumentar o disminuir el tamaño del sistema de archivos.

### XFS:

- Tamaño máximo de sistema de archivos (generalmente se extiende a toda la partición): 8 EiB en sistemas operativos de 64 bits y de 16 TiB en 32 bits.
- Tamaño máximo de archivos: 9 EiB
- Journaling
- Es posible aumentar el tamaño del sistema de archivos pero no disminuirlo

### BTRFS:
- Es uno de los más recientes y su uso crece cada vez más debido a las ventajas que ofrece, entre ellas:
	- Multi-devices: Se puede crear un único sistema de ficheros en varias particiones `mkfs.btrfs /dev/device1 /dev/device2 /dev/device3`
	- Compression: Se puede montar con la opción -o compress y los datos se guardan comprimidos de forma transparente al usuario
	- Subvolumnes: Se pueden crear subvolúmenes y snapshots (instantaneas) `btrfs subvolume create /mnt/punto-montaje/subvolumen1`

### SWAP:
- Es el sistema de ficheros para la partición de intercambio de Linux. Se utiliza como memoria alternativa a la RAM y es invisible para el usuario.

### Crear particiones y sistemas de archivos

La herramienta tradicional para administrar particiones basadas en MBR (presente por defecto en computadoras fabricadas hasta 2009 aproximadamente) es fdisk, mientras que desde 2010 en adelante (particiones basadas en GPT) se comenzó a utilizar también otra utilidad llamada gdisk. 

El estándar GPT permite crear hasta 128 particiones primarias en un mismo disco, y permite alcanzar tamaños de particiones no soportados por MBR (el cual soporta hasta 2 TB y hasta 4 particiones primarias – si se necesitan más particiones se puede reemplazar una de las primarias por una extendida que pueda dividirse en múltiples particiones lógicas).

#### fdisk 

Con la opción `fdisk -l` podemos listar los distintos discos presentes en la máquina y las particiones que puedan llegar a tener cada uno

```
fdisk -l #  Lista los discos y sus particiones

Disk /dev/sda: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: D2AA813E-4E02-4A05-8579-9EAA76E808B8

Device       Start      End  Sectors Size Type
/dev/sda1     2048     4095     2048   1M BIOS boot
/dev/sda2     4096  2101247  2097152   1G Linux filesystem
/dev/sda3  2101248 20969471 18868224   9G Linux filesystem


Disk /dev/sdb: 2 GiB, 2147483648 bytes, 4194304 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdc: 2 GiB, 2147483648 bytes, 4194304 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdd: 2 GiB, 2147483648 bytes, 4194304 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 8,102 GiB, 9659482112 bytes, 18866176 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

Los discos en linux se van enumerando de forma alfabetica como sda, sdb, sdc, etc. En este caso podemos ver que el sda tiene 3 particiones, una donde se encuentra el GRUB o gestor de arranque, otra donde se encuentra el kernel de linux (como se vió durante la instalación) y por último la partición que contiene al sistema. En el caso de sdb, sdc y sdd se puede ver que no tienen ninguna partición.

Con el comando fdisk más el identificador del dispositivo, por ejemplo `fdisk /dev/sdb`, comenzamos la edición del disco sdb:

```
fdisk /dev/sdb

Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x77e015af.

Command (m for help):

```
Algunas opciones más comunes son:
- m: mostrar el menú de ayuda
- p: mostrar la tabla de particiones del dispositivo
- l: mostrar los tipos de particiones soportadas por fdisk
- n: agregar una nueva partición
- d: borrar una partición existente
- w: guardar cambios en la tabla de particiones
- q: salir sin guardar cambios.
- g: crea una tabla de particiones GPT vacia
- o: crea una tabla de particiones DOS vacia

Como se puede ver en la captura, nos dice que el disco en cuestión no tiene tabla de partición.

Con la "m" vemos las opciones que presenta fdisk.

```
Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x39d24173.

Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table


Command (m for help): 
```

Si queremos crear una nueva partición podemos usar la "n".

```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p):
```

Con la letra "p" podemos elegir entre crear una partición primaria o una extendida con la "e". Recordemos qué con MBR podemos crear solo 4 particiones primarias. Si se quiere crear más es necesario crear una partición extendida, sobre la que se puede crear particiones logicas. Si solo damos enter se crea una partición primaria. 

Luego fdisk nos pide que seleccionemos un numero de partición (del 1 al 4, por defecto 1 - si no hay particiones -). 

A continuación seleccionamos el primer sector desde el cual vamos a crear la partición y el sector final o el tamaño ingresando un numero seguido de una letra. Si damos enter sin indicar valores, vamos a usar todo el disco. 

Por defecto fdisk etiqueta la partición como tipo "linux", esto no quiere decir que ya tenga formato, eso se hace después. De esta forma ya hemos creado la partición y podemos verla con la letra "p"

```
Using default response p.
Partition number (1-4, default 1): 
First sector (2048-4194303, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-4194303, default 4194303): 

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): p
Disk /dev/sdb: 2 GiB, 2147483648 bytes, 4194304 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x39d24173

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 4194303 4192256   2G 83 Linux

Command (m for help):  
```

Si quisieramos cambiar el tipo de partición podemos usar la "t" y luego con la "L" podemos listar los tipos:

```
Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs        
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT            
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor      
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor      
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary  
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS    
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE 
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep        
1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT            
Hex code (type L to list all codes):     
```
Si queremos crear una partición para Windows podemos usar la opción 7 (HPFS/NTFS/exFAT).

Si queremos crear una partición etx4 podemos usar la opción 83.

Para finalizar y escribir los cambios en el disco usamos la opción "w".

#### gdisk

El comando fdisk estaba pensado para particiones MBR, con la evolución de la BIOS a la UEFI aparecieron las tablas GPT. El comando gdisk es muy parecido a fdisk pero para manejar especificamente particiones GPT.

Para iniciar su uso tipeamos gdisk y el disco que vamos a usar:

```
gdisk /dev/sdc

GPT fdisk (gdisk) version 1.0.5

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries in memory.

Command (? for help):

```

Gdisk nos indica que no hay presente una tabla de particiones y, por lo tanto, nos crea una nueva entrada GPT. 

Los comandos de gdisk suelen ser similares a fdisk. Si queremos ver el disco usamos "p"

```
Command (? for help): p
Disk /dev/sdc: 4194304 sectors, 2.0 GiB
Model: VBOX HARDDISK   
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): DBECDDA1-3EC5-4216-9AB0-21E222D252AF
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 4194270
Partitions will be aligned on 2048-sector boundaries
Total free space is 4194237 sectors (2.0 GiB)

Number  Start (sector)    End (sector)  Size       Code  Name

Command (? for help): 

```

Nos indica que el disco ya tiene una tabla de partición pero que no tiene ninguna partición asignada. Podemos pulsar la "n" para crear una partición.

```
Command (? for help): n
Partition number (1-128, default 1): 
```

En este caso se puede ver que gdisk nos permite crear hasta 128 particiones. Si pulsamos enter, se numera por defecto y nos va a pedir que indiquemos el primer sector de la partición y luego el final (con enter estos espacios se seleccionan de forma automática). Vamos a dejar el inicio por defecto y el final lo vamos a fijar en 1G con +1G. Luego, el programa nos dice que por defecto va a seleccionar el tipo de sistema de archivos como linux aunque nos da la opción de cambiarlo, vamos a dejarlo así.

```
First sector (34-4194270, default = 2048) or {+-}size{KMGTP}: 
Last sector (2048-4194270, default = 4194270) or {+-}size{KMGTP}: +1G
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): 
Changed type of partition to 'Linux filesystem'
```

Con "p" podemos ver el resultado:

```
Command (? for help): p
Disk /dev/sdc: 4194304 sectors, 2.0 GiB
Model: VBOX HARDDISK   
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): DBECDDA1-3EC5-4216-9AB0-21E222D252AF
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 4194270
Partitions will be aligned on 2048-sector boundaries
Total free space is 2097085 sectors (1024.0 MiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem

Command (? for help):
```

Si queremos crear otra partición volvemos a usar la "n" ...

Para guardar usamos "w". 

Una vez generadas las particiones podemos ver lo que se hizo con `gdisk -l /dev/sdc`

```
GPT fdisk (gdisk) version 1.0.5

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.
Disk /dev/sdc: 4194304 sectors, 2.0 GiB
Model: VBOX HARDDISK   
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): DBECDDA1-3EC5-4216-9AB0-21E222D252AF
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 4194270
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem
   2         2099200         4194270   1023.0 MiB  0700  Microsoft basic data
```

#### Sistemas de archivos

Una vez que tenemos las particiones hechas, si queremos usarlas para guardar información como archivos, directorios, etc necesitamos que tengan una estructura, es decir que es necesario que tengan un sistema de archivos.

Cada uno de los SO utiliza un tipo de sistema de archivos.

La utilidad para realizar esta tarea es mkfs (MaKe File System). El comando es mkfs. y seguido del tipo de partición.

- mkfs.ext4
- mkfs.ext3
- mkfs.vfat
- mkfs.ntfs
- mkfs.btrfs (btrfs-progs)
- mkswap

Para generar el sistema de archivos simplemente debemos utilizar el comando con el tipo de sistema que deseamos generar. Por ejemplo, para generar un sistema de archivos ext4 en la partición sdc1 ejecutamos:

```
mkfs.ext4 /dev/sdb1
```

Entre las opciones de las que disponemos, -L nos servirá para asignarle una etiqueta al sistema de archivos, mediante la cual podremos identificarlo más fácilmente a la hora de montarlo y disponerlo finalmente para su uso. En este caso quedaría así:

```
mkfs.ext4 -L etiqueta /dev/sdb1
```

Una vez hecho esto, ya podemos montar la partición.

```
mkdir /tmp/disco
mount /dev/sdb1 /tmp/disco/
```

#### LVM (Logical Volume Management)

Ya vimos en la [clase1](clase1.md) qué es LVM. Ahora vamos a dedicarnos a crearlos y manipularlos.

Los LVM pueden ser creados a partir de discos enteros o particiones de los mismos.

La estructura de LVM consiste en diferentes componentes:
- 2 o más discos o particiones sobre los que se crearán los volúmenes físicos (PV, physical Volume). Si vamos a trabajar con particiones tenemos que indicar que la partición sea del tipo linux LVM (8e en fdisk o 8e00 en gdisk).
- Grupo de volúmenes (VG, volume Group): Se obtiene a partir de uno a más volúmenes físicos.
- En cada VG podemos crear varios volúmenes lógicos, los que podrían considerarse análogos a las particiones de discos.

Vamos a usar los 3 discos que creamos anteriormente y vamos a convertirlos en volúmenes físicos. Para esto primero eliminamos la información que pueda haber quedado en los discos ejecutando:

```
wipefs -a /dev/sdb
wipefs -a /dev/sdc
wipefs -a /dev/sdd
```

Ahora podemos crear los PV:

```
pvcreate /dev/sdb
 Physical volume "/dev/sdb" successfully created.
pvcreate /dev/sdc
 Physical volume "/dev/sdc" successfully created.
pvcreate /dev/sdd
 Physical volume "/dev/sdd" successfully created.
```

Podemos usar pvdisplay o pvs ara mostrar los atributos o ver información sobre un volumen físico, respectivamente y la ruta del PV (pvdisplay /dev/sdb o pvs /dev/sdb). Si omitimos la ruta veremos la lista de todos los volúmenes físicos que existen en nuestro sistema. 

```
pvs
  PV         VG        Fmt  Attr PSize  PFree
  /dev/sda3  ubuntu-vg lvm2 a--  <9,00g    0 
  /dev/sdb             lvm2 ---   2,00g 2,00g
  /dev/sdc             lvm2 ---   2,00g 2,00g
  /dev/sdd             lvm2 ---   2,00g 2,00g

pvs /dev/sdb
  PV         VG Fmt  Attr PSize PFree
  /dev/sdb      lvm2 ---  2,00g 2,00g

pvdisplay /dev/sdb
  "/dev/sdb" is a new physical volume of "2,00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb
  VG Name
  PV Size               2,00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               rGXJJj-I87g-16q6-fvKU-f95k-5sMo-XxTHmu

```

Ahora ya podemos crear el grupo de volumenes. Vamos a usar solo 2 de los discos (sdb y sdc), con sdd vamos a extender el VG que hicimos al instalar el SO.

Vamos a llamar a este VG curso-vg:

```
vgcreate curso-vg /dev/sdb /dev/sdc
 Volume group "curso-vg" successfully created
```

```
vgs
  VG        #PV #LV #SN Attr   VSize  VFree
  curso-vg    2   0   0 wz--n-  3,99g 3,99g
  ubuntu-vg   1   1   0 wz--n- <9,00g    0 


vgdisplay
  --- Volume group ---
  VG Name               curso-vg
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               3,99 GiB
  PE Size               4,00 MiB
  Total PE              1022
  Alloc PE / Size       0 / 0   
  Free  PE / Size       1022 / 3,99 GiB
  VG UUID               QtbPqK-ilwi-TD70-jmZt-Qk0U-3zy1-okfRfp
   
  --- Volume group ---
  VG Name               ubuntu-vg
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <9,00 GiB
  PE Size               4,00 MiB
  Total PE              2303
  Alloc PE / Size       2303 / <9,00 GiB
  Free  PE / Size       0 / 0   
  VG UUID               dngU2r-IFiM-i4sq-TC8i-JgIX-WKnl-NFfVGe

```
Como se puede ver, el espacio de curso-vg es ahora de ~ 4GB, esto nos permite crear una unidad de almacenamiento de mayor capacidad utilizando discos o particiones de menor tamaño.

Ahora vamos a crear el primer volumen logico de 500M llamado lv1 sobre curso-vg:

```
lvcreate -n lv1 -L 500M curso-vg

  Logical volume "lv1" created.
```

```
lvs curso-vg/lv1
  LV   VG       Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv1  curso-vg -wi-a----- 1,00g                                                    
lvdisplay curso-vg/lv1
  --- Logical volume ---
  LV Path                /dev/curso-vg/lv1
  LV Name                lv1
  VG Name                curso-vg
  LV UUID                HJJHRO-txEZ-cUis-QGbs-TXNM-z91H-uNwyaL
  LV Write Access        read/write
  LV Creation host, time ubuntu-curso, 2021-11-18 02:34:30 +0000
  LV Status              available
  # open                 0
  LV Size                1,00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```

Si se omite -n <nombre>, el nombre se asigna automáticamente.

Ya podemos crear el sistema de archivos sobre lv1 y montarlo en algún directorio creado previamente. Por ejemplo, /home/<usuario>/curso.

```
mkdir /home/pale/curso
mkfs.ext4 /dev/curso-vg/lv1

mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: bd17b192-8775-4697-858e-474c91a8e86b
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

mount /dev/curso-vg/lv1 /home/pale/curso

chown pale:pale .
```

Si hacemos lsblk podemos ver que el LV está montado:

```
sda                        8:0    0   10G  0 disk 
├─sda1                     8:1    0    1M  0 part 
├─sda2                     8:2    0    1G  0 part /boot
└─sda3                     8:3    0    9G  0 part 
  └─ubuntu--vg-ubuntu--lv
                         253:0    0    9G  0 lvm  /
sdb                        8:16   0    2G  0 disk 
└─curso--vg-lv1          253:1    0    1G  0 lvm  /home/pale/curso
sdc                        8:32   0    2G  0 disk 
sdd                        8:48   0    2G  0 disk 
```

Ahora vamos a extender el VG que hicimos durante la instalación. Para agregar un PV a un VG, utilizaremos el comando vgextend seguido del nombre del VG y finalmente del PV:

```
vgextend ubuntu-vg /dev/sdd
  Volume group "ubuntu-vg" successfully extended
```

Si ejecutamos `vgs ubuntu-vg` vemos:

```
vgs ubuntu-vg
  VG        #PV #LV #SN Attr   VSize  VFree
  ubuntu-vg   2   1   0 wz--n- 10,99g <2,00g
```

Podemos ver que el tamaño se incrementó. 

Ya incrementamos el tamaño del VG y ahora podemos hacer lo mimo con el tamaño del LV. 

```
lvresize --resizefs --size 10.99G ubuntu-vg/ubuntu-lv

  Rounding size to boundary between physical extents: 10,99 GiB.
  Size of logical volume ubuntu-vg/ubuntu-lv changed from <9,00 GiB (2303 extents) to 10,99 GiB (2814 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 2
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 2881536 (4k) blocks long.

```

Ahora comprobamos el espacio del directorio raiz del SO:
```
df -h /
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv   11G  4,3G  6,0G  42% /

```

Como podemos ver, pasó de 9GB a 11G en "caliente", es decir, no tuvimos que desmontar el disco, ni particiones, simplemente ejecutamos un comando y listo.

```
lsblk 
NAME                     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                        8:0    0   10G  0 disk
├─sda1                     8:1    0    1M  0 part
├─sda2                     8:2    0    1G  0 part /boot
└─sda3                     8:3    0    9G  0 part
  └─ubuntu--vg-ubuntu--lv
                         253:0    0   11G  0 lvm  /
sdb                        8:16   0    2G  0 disk
└─curso--vg-lv1          253:1    0    1G  0 lvm  /home/pale/curso
sdc                        8:32   0    2G  0 disk
sdd                        8:48   0    2G  0 disk
└─ubuntu--vg-ubuntu--lv  253:0    0   11G  0 lvm  /
```


#### Fstab

El archivo /etc/fstab contiene información sobre los sistemas de archivos que deben montarse al inicio del sistema. Cada línea en el archivo posee los siguientes 6 campos:

```
tail -6 /etc/fstab

# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-dngU2rIFiMi4sqTC8iJgIXWKnlNFfVGewEG6AYTDp8B9AQ9qehCeq83nfFABkG83 / ext4 defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/99753967-872c-4a15-97fa-a8c831c75b45 /boot ext4 defaults 0 1
#[---------------------------------------------------] [---] [--] [------] - -
#                     1                                  2     3      4    5 6
/swap.img	none	swap	sw	0	0
```
- 1. Dispositivo de almacenamiento (local o remoto) que debe montarse. Puede especificarse mediante el identificador de dispositivo (por ejemplo, /dev/sdb1) o mediante la etiqueta asignada al momento de crear el sistema de archivos (LABEL=etiqueta), o mediante el UUID del sistema de archivos (lo cual puede averiguarse mediante el comando blkid)
- 2. Punto de montaje en el que se anexará el sistema de archivos.
- 3. Tipo de sistema de archivos (ext4, xfs, etc.)
- 4. Opciones de montaje (separadas por comas si hay más que una). En `man  mount` podemos encontrar una descripción detallada.
- 5. Opción en desuso.
- 6. Indica si el sistema de archivos debe chequearse al iniciar el sistema y en qué orden: 
		- Para / (la raíz del árbol de directorios) siempre debería ser 1, 
		- Mientras que para el resto siempre 2. 
		- Si este campo no existe o es 0 significa que no se debe chequear el sistema de archivos.

Vamos a montar el volúmen lógico "curso-lv1" en un directorio del usuario:

```
# chequeamos el nombre del disco con blkid
blkid | grep curso
/dev/mapper/curso--vg-lv1: UUID="bd17b192-8775-4697-858e-474c91a8e86b" TYPE="ext4"

# creamos el directorio:
mkdir /home/pale/lv1
# agregamos la siguiente linea en /etc/fstab:
vi /etc/fstab
/dev/mapper/curso--vg-lv1 /home/pale/lv1 ext4 defaults 0 2
# Aplicamos los cambios ejecutando 
mount -a
# asignamos el directorio al usuario 
chown -R pale.pale /home/pale/lv1
```

#### mount

Para montar un dispositivo bajo demanda podemos usar la función mount:

```
mount -t ext4 LABEL=etiqueta /mnt/pruebas
mount -t ext4 /dev/sde1 /mnt/pruebas
```

Con el comando umount desmontamos

```
umount /mnt/pruebas
```
