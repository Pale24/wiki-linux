# Secuencia de arranque de un núcleo Linux

Aquí aprenderemos la secuencia de arranque de un sistema operativo libre y de código abierto, GNU/Linux, lo que nos va a remitir también a conocer y entender algunos de los componentes de la computadora, y su función.

Entender el proceso de arranque nos permite entender también mucho del hardware de la computadora, y cómo interactúa con el software, y además nos brindará conocimientos para depurar el inicio del sistema y permitirnos solucionar cualquier problema relacionado.
Al encender el equipo, el BIOS (Basic Input-Output System) o UEFI (Unified Extensible Firmware Interface) llevan a cabo una revisión del hardware conocida comunmente como POST (Power-On Self Test). A continuación, se busca un gestor de arranque (por lo general, GRUB) en el MBR (Master Boot Record) o en la partición EFI de un dispositivo de almacenamiento.

A partir de ese momento, el control del proceso se pasa a manos del GRUB, que se encargará de cargar el kernel. Este reconocerá y configurará los dispositivos de hardware presentes en el equipo y los preparará para su uso. En el próximo paso, el núcleo ejecutará su primer proceso, conocido como **init**. Finalmente, este último utilizará el gestor del sistema ( **systemd o Upstart** ) o los scripts de  **SysVinit** para continuar el inicio de los demás servicios. El proceso de arranque termina al presentar una interfaz de inicio de sesión de modo texto o gráfico, según el caso.

La secuencia de arranque de Linux puede dividirse en varias etapas:

1. La **fuente de energía** de la computadora envía un OK al timer de la placa madre.
2. **Bootstrap** y el proceso de arranque del hardware.
3. El **BIOS** y el proceso de inicio.
4. El **MBR** (Master Boot Record) y el **GRUB**.
5. Carga de la imagen del **kernel Linux** (initrd)
6. Carga de los servicios de inicio (SysVinit, systemd, upstart, etc.)

## 1. Suministro électrico

Uno de los componentes principales de la computadora es la fuente de energía, y su principal objetivo es el de proveer el nivel de voltaje necesario a la placa madre y demás componentes. Este voltaje trabaja con corriente contínua (DC), por lo que la fuente también transforma la corriente alterna domiciliaria (AC) en corriente continua.

Más allá de esto, la fuente informa sobre un buen suministro eléctrico a la computadora enviando una señal acorde, POWER GOOD, a la placa madre. Si la placa madre no recibe dicha señal de la fuente todo el tiempo, a su vez enviará una señal de reset al procesador y no podrá iniciar.

## 2. Bootstrap y secuencia de arranque

El bootstrap consiste en que el procesador busque en una ROM (Read Only Memory) de la placa madre las instrucciones para arrancar. Estas direcciones de memoria en la que se encuentran las instrucciones son por lo general 0xFFFF:0000 en procesadores x86.

Estas instrucciones contienen un JUMP o salto a otra dirección de memoria, la del programa BIOS (Basic Input/Output System) de la placa madre.

## 3. BIOS

Aquí comienza realmente el proceso de inicio lógico de la computadora. Una de las primeras tareas que lleva a cabo el BIOS es denominada POST (Power on Self Test). El POST realiza una serie de verificaciones dirigidas por la BIOS que permiten determinar el estado del hardware y con él, saber si la computadora puede iniciar o no.

El POST es muy importante en la secuencia de arranque, y es un paso previo a la carga del sistema operativo, ya que verifica, entre otras cosas, la placa madre, puertos de periféricos como el teclado, los discos, la integridad de la memoria, la CPU, la ROM de video, etc.

En general hay dos tipos de inicio de la computadora, Warm Start y Cold Start. El Cold Start ejecuta el POST conducido por el BIOS cuando inicia la computadora desde su estado apagado, mientras que el Warm Start no ejecuta el post, y se da cuando iniciamos la computadora presionando el botón de Reset del gabinete.

Una confusión generalizada en pensar que el BIOS y el CMOS son la misma cosa. El BIOS, como dijimos, es un componente de software almacenado en una memoria ROM. El CMOS por su parte es una pequeña memoria RAM (Random Access Memory) alojada en la placa madre. Esta memoria almacena las configuraciones que efectuemos en el programa BIOS.

No confundir tampoco esta RAM CMOS con la memoria RAM principal de la computadora.

**RAM CMOS y la persistencia**

Recordemos que una memoria ROM es persistente y no puede modificarse directamente, por lo que NO podemos modificar el BIOS, mientras que la RAM es volátil, puede modificarse, pero su contenido se pierde al perder energía eléctrica. El contenido del CMOS en general se mantiene gracias una batería que se encuentra en la placa madre.

Al quitar la pila que mantiene el CMOS se perderá información como la fecha y hora configuradas en la BIOS, o la password de acceso a la misma.

**Dispositivos de arranque**

Volviendo a la secuencia de arranque, entre las configuraciones almacenadas en el CMOS se encuentra el orden de inicio de los dispositivos de almacenamiento donde se buscará un sistema operativo al finalizar la carga del BIOS. Estos dispositivos son, por lo general:

- CD / DVD ROM
- Disco Rígido / SSD
- Dispositivos USB
- Red (PXE/iPXE)
- Disquettes / Floppy

Según el orden en el que lo hayamos configurado en el CMOS, al iniciar buscará en uno u otro dispositivo hasta encontrar uno que disponga un sistema operativo instalado utilizable.

## 4. MBR y el gestor de arranque (GRUB)

Si estamos iniciando desde un disco rígido o SSD, o USB por ejemplo, el BIOS buscará en el dispositivo, en una región de memoria denominada Boot Sector, o sector de inicio, las siguientes instrucciones a ejecutar, correspondientes al gestor de arranque del sistema operativo. De ahí en adelante ya es el sistema operativo instalado el que se encarga de continuar la secuencia de arranque.

Ese área del disco en que se encuentra el Boot Sector es el primer sector del disco, y suele denominárselo Master Boot Record, o MBR. Ese sector corresponde a solo 512 bytes y permiten iniciar la primer etapa del boot loader, o el gestor de arranque, como Grub o el viejo Lilo. De esos 512B, solo 440B se utilizan para el cargador de arranque, el resto almacena la tabla de particiones del disco.

Dicho sea de paso, podemos guardar en un archivo binario todo el MBR con un simple comando en una terminal GNU/Linux: dd. Este comando permitirá copiar un bloque de 512B del disco en un archivo que llamaremos mbr.img.

```
dd if=/dev/sda of=/tmp/mbr.img bs=512 count=1
```
En caso de usar la tabla de partición GPT, los pasos son más complejos

```
parted -ms /dev/sda print
# Formula = (128*N)+1024 N: nro de particiones
dd if=/dev/sda of=GPT_TABLE.img bs=1 count=FORMULA
```
Podemos analizar el contenido del sector de arranque con `fdisk -l /tmp/mbr.img` si la partición es MBR o con `parted -ms /dev/sda print` si es GPT

```
BYT;
/dev/sda:1000GB:scsi:512:4096:gpt:ATA HGST HTS541010A7:;
1:1049kB:274MB:273MB:fat32:EFI system partition:arranque, esp;
2:274MB:290MB:16.8MB::Microsoft reserved partition:msftres;
3:290MB:507GB:506GB:ntfs:Basic data partition:msftdata;
5:507GB:537GB:30.0GB:ext4::;
6:537GB:997GB:460GB:ext4::;
7:997GB:1000GB:2999MB:linux-swap(v1)::swap;
4:1000GB:1000GB:523MB:ntfs:Basic data partition:oculta, diag;

```

**GRUB – GRand Unified Bootloader**

El primer sector del disco, el sector 0 mencionado anteriormente, contiene lo que se denomina Stage 1, la primera etapa del cargador de arranque. 

El cargador de arranque GRUB tiene en total tres etapas o stages:

- GRUB Stage 1
- GRUB Stage 1.5
- GRUB Stage 2

Normalmente las particiones no comienzan antes del sector 63 del disco, por lo que los sectores 1-63 están vacíos (recordemos que el sector 0 contiene el MBR). Este espacio es utilizado para almacenar lo que se denomina Stage 1.5. A este espacio también se lo denomina MBR GAP.

¿Por qué es necesaria una supuesta Stage 1.5 en GRUB?

Por lo general, la configuración del GRUB se encuentra en el archivo /boot/grub/grub.cfg... pero este archivo está en el disco, en una partición (que puede ser una partición aislada para /boot, o la misma partición donde tenemos el sistema operativo instalado).

¿Cómo puede el GRUB acceder a dicho archivo si no se ha montado la partición, ni dispone de los drivers o controladores? Puede porque estos controladores de sistema de archivos se encuentran en estos sectores, del 1 al 63.

Aquí es cuando el GRUB carga dicha configuración y nos muestra un menú en terminal donde podemos elegir qué sistema operativo iniciar. Esto corresponde con el Stage 2 del GRUB.

## 5. Cargando la imagen del kernel Linux

Una vez que elegimos un sistema operativo GNU/Linux para iniciar, el GRUB le pasa el control del arranque al sistema operativo mediante la carga de una imagen comprimida del núcleo del mismo.

De manera similar al GRUB, el kernel Linux también se carga en etapas o Stages. El kernel cabe aclarar que es quien administra la planificación de procesos, memoria, sistemas de archivos, dispositivos periféricos de E/S, mecanismos de comunicación entre procesos (IPC), e interfaces de red, entre otros.

El kernel nunca es utilizado por los usuarios, sino que representa una capa de abstracción entre el resto del sistema operativo y el hardware, monta un entorno en el que puede correr todo el resto del sistema.

El kernel es un archivo comprimido de imagen que generalmente se encuentra dentro del directorio /boot, ubicación que está configurada en el archivo de configuración del GRUB2. Básicamente es un archivo de imagen bzImage ejecutable. Su nombre característico contiene la palabra «vmlinuz«:

```
ls /boot
config-5.4.0-80-generic  initrd.img                   memtest86+.bin               System.map-5.4.0-81-generic  vmlinuz.old
config-5.4.0-81-generic  initrd.img-5.4.0-80-generic  memtest86+.elf               vmlinuz
efi                      initrd.img-5.4.0-81-generic  memtest86+_multiboot.bin     vmlinuz-5.4.0-80-generic
grub                     initrd.img.old               System.map-5.4.0-80-generic  vmlinuz-5.4.0-81-generic

```

**initrd e initramfs**

Para poder cargar el sistema operativo primero debemos cargar muchos controladores de diversos dispositivos de hardware. Por ejemplo, podríamos tener el disco configurado en RAID o LVM donde tenemos la partición raíz («/»), o podríamos disponer de varios módulos del núcleo que realicen diferentes tareas. Si toda la funcionalidad estuviera presente en el mismo kernel Linux, la imagen comprimida sería enorme!

No obstante, la imagen del núcleo necesita ser más pequeña, y por eso, entre otras cosas, se encuentra comprimida. Las funcionalidades adicionales no están presentes en dicha imagen, sino que son cargadas mediante un sistema de archivos similar al sistema raíz provisto por initrd, por lo que muchas veces a initrd se lo denomina un sistema de archivos raíz inicial.

Initrd es utilizado por el kernel antes de que se monte el sistema raíz «/» real. En general esta imagen de un sistema de archivos de inicio suele llamarse «initramfs» dentro del directorio /boot.

Una vez que se carga este «mini linux» en memoria RAM (de ahí initramfs), se procede a la verificación de hardware y controladores. Se comienza con la verificación de la arquitectura del procesador.

El kernel dirige una gran cantidad de instrucciones de hardware, y luego carga el primer proceso del sistema operativo, el proceso init, cuyo PID (Process ID) es el 1:

Este PID 1 no es intencional, el núcleo le da a cada proceso nuevo que se lanza un identificador incremental. En el caso de init es «1» porque es el primer proceso que se ejecuta en espacio de usuario. El proceso init se mantiene corriendo durante toda la ejecución del sistema operativo hasta que se apaga la computadora.

Luego de cargar los módulos necesarios para «levantar» los dispositivos de hardware del ordenador, comienza la carga «real» del sistema operativo: el sistema de inicio y gestor de servicios y eventos.

Aquí disponemos de varias alternativas, entre las más conocidas: SysV init, systemd, o upstart. Estos gestores se encargan de terminar el proceso de inicio y de gestionar todos los procesos de usuario.

Cada uno tiene características distintivas, y aunque hoy en día systemd es el más común (no sin controversias), existen distribuciones GNU/Linux que utilizan SysV init tales como Devuan.

Por su parte, Upstart fue introducido por primera vez en Ubuntu 6.10, pero cuando Debian migró a systemd, Ubuntu también lo hizo. Algo similar pasó con Fedora, que utilizaba upstart en su v9 para reemplazar a SysV. No obstante, Fedora 15 reemplazó a upstart por systemd también.

Dependiendo del gestor de servicios que use la distro será la forma en la que cargará los siguientes procesos. SysV trabaja con una serie de runlevels predefinidos, mientras que systemd utiliza targets con algunas equivalencias con los runlevels de Systemd.

## SysVinit
Muchas distribuciones, hasta no hace mucho tiempo, utilizaban este sistema de arranque y administración. Contempla 5 niveles útiles de funcionamiento numerados del 1 al 5 (de ahí el nombre systemV). Ademas se suma el nivel 0 (apagado) y 6 (reinicio del sistema).Estos niveles se conocen como niveles de corrida o runlevels. 

Cada **runlevel** se ecuentra asociado a un cierto número de servicios que por defecto deben iniciarse automáticamente cuando encendemos el equipo, y que deben detenerse al reiniciarlo o apagarlo. 

### Inicio de SysV Runlevels

- Varios modelos
- Cambios de modos
- Configuración por defecto

#### Modos
| Nro | Debian/Ubuntu                    | CentOS/Suse        |
|-----|----------------------------------|--------------------|
| 0   | Halt                             | Halt               |
| 1   |	Single User mode                 | Single User Mode   |
| 2   |	Full, multiuser, GUI if installed| Multi user, sin red|
| 3   | Nada                             | Multi user, con red|
| 4   | Nada                             | Nada 			  |
| 5   | Nada                             | Multi user GUI     |
| 6   | Reboot                           | Reboot             |

#### Cambios de modos y configuración por defecto
```
runlevel  # Indica el runlevel actual
> N 5 	  # Respuesta: N: anterior (nada), 5: estado (Multi user)
telinit 3 # Cambio de runlevel (Multi user con red sin GUI)
telinit 5 # Cambio a Multi User GUI.
runlevel
> 3 5
vi /etc/inittab # Archivo de configuracion
...
id:5:initdefault: # Estado por default
...
```

### Inicio con SystemD
- Varios modos
- Cambios de modos
- Configuración por defecto

| Runlevel | Boot Target |
|----------|-------------|
| 0        | poweroff    |
| 1		   | rescue      |
| 2        | multi-user  |
| 3        | graphical   |
| 5        | reboot      |

*Ver estado*
```
systemctl get-default
> graphical.target
```

*Cambiar estado por defecto*
```
systemctl set-default multi-user.target
```
luego de reiniciar, cambia el modo

*Cambiar estado*
```
systemctl isolate multi-user
```

#### Manejo de servicio con SysV
- `/etc/init.d/`
- service
- chkconfig

En /etc/init.d están los servicios disponibles y se manejan con el comando service.

```
service sshd start
service sshd stop
service sshd status
```

*Configuración*
```
chkconfig --list sshd # Lista como actua el servicio en cada runlevel
> sshd 0:off	1:off	2:off	3:off	4:off	5:off	6:off
chkconfig sshd on # Activa sshd en todos los niveles (no en 0, 1 y 6)
chkconfig --level 3 sshd on
```


#### Manejo de servicios con systemD
- systemctl
	- enable / disable
	- start / stop / restart / status

A diferencia de SysV, los achivos de configuración de systemD no se encuentran en un solo lugar. Esto hace muy engorrosa la administración de servicios.

*Manejo de servicios*
```
systemctl status sshd
systemctl start <servicio>
systemctl stop <servicio>
systemctl enable <servicio>
systemctl disable <servicio>
```


- Fuentes:
	- [Secuencia de arranque de un núcleo Linux](https://juncotic.com/secuencia-de-arranque-linux/)
	- Carrera linux - Curso operador linux
