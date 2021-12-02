# Clase 4

## Temas:

- 11. Logs del sistema
- 12. Gestión de procesos
- 13. Tareas programadas

# Logs del sistema

Los logs o registros del sistema son archivos de texto que registran cronológicamente la totalidad de actividades e incidencias importantes que ocurren en el sistema operativo o red. El sistema registra todo lo que ocurre en estos logs. 

## Registros de logs más comunes:

- Paquetes instalados o desinstalados.
- Accesos remotos al equipo.
- Intentos fallidos de autenticación.
- Registro de errores que se dan en los programas o servicios.
- Accesos o salidas bloqueadas por el firewall.

Mediante estos logs se puede saber todo lo que está pasando en el equipo y así poder detectar problemas que pueda tener el equipo, registrar posibles intrusiones, evaluar el rendimiento, etc.

Por defecto estos logs se almacenan en /var/log y subdirectorios. Sin embargo, este servicio puede configurarse a traves del archivo /etc/rsyslog.conf y archivos de configuración en /etc/rsyslog.d/ (el archivo por defecto es 50-default.conf).

## rsyslog.conf

Presenta diferentes secciones.

La sección **Modules** nos permite agregar funcionalidades especificas, como la posibilidad de recibir logs de equipos remotos.

La sección **Global directives** nos permite modificar la configuración por defecto de los archivos. Podemos generar un template que establezca donde se van a guardar ciertos archivos, el propietario de los archivos generados, etc. Por ejemplo, en la siguiente linea le indicamos al servidor que los logs generados en equipos remotos se almacenen en el directorio /var/log/remote/nombre del host/nombre del programa.log.

```
$template RemoteLogs,"/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log"
```

Actualmente este archivo incluye una referencia a los archivos presentes en /etc/rsyslog.d/*.conf, que son los archivos donde se guardan las reglas o rules. Sin embargo, también es posible escribir estas reglas en el propio archivo rsyslog.conf.

### Rules
La estructura de las reglas es la siguiente:
```
recurso.prioridad                   acción
```
Donde recurso es algún servicio que genera los mensajes a registrar, la prioridad es la severidad o importancia del mensaje que emite ese servicio, puede ir en orden creciente de importancia desde debug, info, notice, warning, error, crit, alert, emerg. La acción es lo que debe hacer rsyslog con ese mensaje.

Ejemplos:

```
auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/syslog
kern.*                          -/var/log/kern.log
mail.*                          -/var/log/mail.log
mail.err                        /var/log/mail.err
*.emerg                         :omusrmsg:*
*.=notice;*.=warn               /dev/tty8
*.*                             @@192.168.2.2
```

Algunas opciones:
- Los recursos con la misma prioridad pueden separarse por ",". Así, podemos ver que los servicios *auth* y *authpriv*, servicios que generan mensajes de autenticación, son enviados al archivo /var/log/auth.log, sin importar la prioridad que tengan, de ahí el "*".
- Si queremos separar varios servicios y prioridades para aplicarles la misma acción podemos usar ";".
- Si se fija una prioridad, por ejemplo `mail.err`, el sistema va a reportar todos los mensajes con esta prioridad y también los superiores. Para que solo se registre una sola prioridad podemos usar "=". Ejemplo: `mail.=err`, de esta forma sólo se reportan los mensajes de error del servicio "mail".
- `:omusrmsg:*` Significa que se notifica a todos los usuarios logueados
- `-/var/log/mail.log`: El signo - delante se utilizaba en versiones anteriores para omitir la sincronización con el archivo inmediatamente luego de cada escritura. Hoy es una opción en desuso.
- `authpriv.none`: Indica que no deben registrarse ningún mensaje de authpriv en la acción indicada.
- `*.=notice;*.=warn  /dev/tty8`: Indica que todos los mensajes con prioridad notice y warn de todos los servicios se van a mostrar en la terminal virtual tty8
- `*.*       @@192.168.2.2`: Indica que todas las notificaciones irán al servidor rsyslog del equipo con la IP 192.168.2.2 mediante el protocolo UDP (un solo "@" se usa para el protocolo TCP). El equipo al que se dirigen los logs debe tener habilitado el servicio (en el archivo /etc/rsyslog.conf)

### El mensaje

El mensaje se compone de 3 campos que no deben superar los 1024 bytes:

- Prioridad: Indica el servicio y la prioridad como describimos antes.
- Cabecera: La cabecera indica la fecha y hora del mensaje, el host desde el que se origina.
- Texto: Mensaje, describe el motivo del evento.

```
Nov 25 15:20:01 ubuntu-curso sudo:     pale : TTY=pts/0 ; PWD=/home/pale ; USER=root ; COMMAND=/usr/bin/su -
```
En el ejemplo anterior se puede ver la fecha y la hora en la que el usuario pale ejecutó el comando `/usr/bin/su -` con sudo para loguearse como root. Nos dice la ubucación desde donde accedió (PWD) y la terminal que usó (TTY=pts/0)

Podemos probar rsyslog con el comando logger:

```
logger -p auth.notice "Aviso"

vi /var/log/auth.log
Nov 25 16:04:22 ubuntu-curso pale: Aviso
```

### Algunos archivos interesantes:

- /var/log/auth.log: Actividades de autenticación. Por ejemplo, usuarios logueados al sistema. Registra el día, hora, usuario y ordenes que se han ejecutado con el comando sudo, los cronjobs que se han ejecutado, los intentos fallidos de autenticación, etc. Consulta: `less /var/log/auth.log`
- /var/log/syslog: Contiene la totalidad de logs capturados por rsyslogd. Por lo tanto en este fichero encontraremos multitud logs y será difícil de consultar y filtrar. Por este motivo, los logs se distribuyen en otros ficheros siguiendo la configuración del fichero /etc/rsyslog.conf. Consulta: `less /var/log/syslog`
- /var/log/kern.log: Proporciona información detallada de mensajes del kernel. Puede ser útil para intentar detectar y solucionar problemas con la detección de hardware. Consulta: `less /var/log/kern.log`
- /var/log/dmesg: Información relacionada con el hardware de nuestro equipo. Por lo tanto podremos obtener información para concluir si nuestro hardware funciona de forma adecuada. Consulta: `dmesg | less`
- /var/log/btmp: Almacena los intentos fallidos de logins en un equipo. Si alguien realizará un ataque de fuerza bruta a un servidor ssh, el fichero registraría la IP del atacante, el día y hora en que ha fallado el login, el nombre de usuario con que se ha intentado loguear, etc. Consulta: `utmpdump /var/log/btmp` o `last -f /var/log/btmp | less`

	Ejemplo de intento de ataque por fuerza bruta al servidor:

	`hydra -l pale -V -x 4:4:aA1 192.168.0.18 ssh`

	```
	utmpdump /var/log/btmp
	[6] [02055] [    ] [pale    ] [ssh:notty   ] [192.168.0.3         ] [192.168.0.3    ] [2021-11-25T20:10:32,000000+00:00]
	[6] [02070] [    ] [pale    ] [ssh:notty   ] [192.168.0.3         ] [192.168.0.3    ] [2021-11-25T20:10:32,000000+00:00]
	[6] [02083] [    ] [pale    ] [ssh:notty   ] [192.168.0.3         ] [192.168.0.3    ] [2021-11-25T20:10:32,000000+00:00]
	[6] [02060] [    ] [pale    ] [ssh:notty   ] [192.168.0.3         ] [192.168.0.3    ] [2021-11-25T20:10:34,000000+00:00]
	```
	```
	vi /var/log/auth.log

	Nov 25 17:10:28 ubuntu-curso sshd[2070]: Failed password for pale from 192.168.0.3 port 46392 ssh2
	Nov 25 17:10:28 ubuntu-curso sshd[2069]: Failed password for pale from 192.168.0.3 port 46390 ssh2
	Nov 25 17:10:28 ubuntu-curso sshd[2083]: Failed password for pale from 192.168.0.3 port 46398 ssh2
	Nov 25 17:10:30 ubuntu-curso sshd[2054]: error: maximum authentication attempts exceeded for pale from 192.168.0.3 port 46366 ssh2 [preauth]
	Nov 25 17:10:30 ubuntu-curso sshd[2054]: Disconnecting authenticating user pale 192.168.0.3 port 46366: Too many authentication failures [preauth]
	```
- /var/log/dpkg.log: Contiene información sobre la totalidad de paquetes instalados y desinstalados mediante el comando dpkg. `less /var/log/dpkg.log`
- /var/log/apt/history.log: Detalle de los paquetes instalados, desinstalados o actualizados mediante el gestor de paquetes apt-get.

### Mecanismo de rotación

Si se observa el contenido del directorio /var/log se puede observar que existen archivos .log, .log.1, .log.2.gz, .log.3.gz y .log.4.gz:

```
ls -l /var/log/auth.log*
-rw-r----- 1 syslog adm 59615 nov 25 19:17 /var/log/auth.log
-rw-r----- 1 syslog adm 30296 nov 23 17:54 /var/log/auth.log.1
-rw-r----- 1 syslog adm  4500 nov 15 19:03 /var/log/auth.log.2.gz
-rw-r----- 1 syslog adm  1959 nov  6 20:38 /var/log/auth.log.3.gz
-rw-r----- 1 syslog adm  2148 oct 31 11:46 /var/log/auth.log.4.gz
```
Como podemos ver en la salida de ls, cada uno de estos archivos se crea cada una semana y si se ingresa a cada uno de ellos se puede ver que contienen información de los logs creados hasta esa fecha. Ademas, los últimos 3 se encuentran comprimidos (.gz). Este comportamiento es llevado a cabo por el mecanismo de rotación de logs. Su función es evitar que los logs consuman todo el espacio de almacenamiento y sean fáciles de consultar. Si analizamos el comportamiento del log auth.log veremos que semanalmente realiza las siguientes operaciones:

- 1. El contenido que estaba en el fichero auth.log.4.gz se borrará y perderá de forma definitiva.
- 2. El contenido que estaba en el fichero auth.log.3.gz se trasladará al fichero comprimido auth.log.4.gz.
- 3. El contenido que estaba en el fichero auth.log.2.gz se trasladará al fichero comprimido auth.log.3.gz.
- 4. El contenido que estaba en el fichero auth.log.1 se comprimirá y trasladará al fichero auth.log.2.gz.
- 5. Finalmente, la información almacenada en auth.log se trasladará al fichero auth.log.1 y el fichero auth.log quedará vacío y listo para seguir registrando información.

De esta forma únicamente estaremos almacenando los logs de las últimas 4-5 semanas. Por lo tanto, para consultar las autenticaciones al sistema operativo de 3 semanas atrás deberemos consultar el log auth.log.3.gz

La rotación de los logs es una tarea programada que se realiza de forma periódica. Los logs se rotan en función de un criterio que nosotros mismos podemos configurar. El criterio de rotación de logs se establece en los ficheros de configuración ubicados en /etc/logrotate.d

## Journalctl

En systemd tenemos un servicio denominado journald que se encarga de recolectar, almacenar y administrar los mensajes de logs del sistema. Esta herramienta tiene la particularidad de almacenar la información en binario, lo que hace necesario el uso de la herramienta journalctl para poder consultar los logs. 

Por defecto, al ejecutar sólo `journalctl` sin ninguna opción adicional el comando nos muestra todo el contenido de journald guardado. Todos los usuarios pueden visualiar sus propios journals pero los archivos de logs principales y los de otros usuarios solo pueden ser accedidos por usuarios con privilegios de administrador.

```
journalctl
-- Logs begin at Thu 2021-10-28 08:38:46 -03, end at Mon 2021-11-29 19:14:35 -03. --
oct 28 08:38:46 ubuntu-curso kernel: Linux version 5.4.0-89-generic (buildd@lgw01-amd64-044) (gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1>
oct 28 08:38:46 ubuntu-curso kernel: Command line: BOOT_IMAGE=/vmlinuz-5.4.0-89-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro maybe>
oct 28 08:38:46 ubuntu-curso kernel: KERNEL supported cpus:
oct 28 08:38:46 ubuntu-curso kernel:   Intel GenuineIntel
oct 28 08:38:46 ubuntu-curso kernel:   AMD AuthenticAMD
oct 28 08:38:46 ubuntu-curso kernel:   Hygon HygonGenuine
oct 28 08:38:46 ubuntu-curso kernel:   Centaur CentaurHauls
oct 28 08:38:46 ubuntu-curso kernel:   zhaoxin   Shanghai  
oct 28 08:38:46 ubuntu-curso kernel: x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
oct 28 08:38:46 ubuntu-curso kernel: x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
oct 28 08:38:46 ubuntu-curso kernel: x86/fpu: Supporting XSAVE feature 0x004: 'AVX registers'
```
Como pueden ver nos muestra los logs desde el 28/10, que es el día que instalé la máquina virtual y a medida que vamos bajando vamos viendo el contenido actualizado. El resultado se visualiza sobre el comando `less` por lo que, para ver más lineas podemos usar las flechas de abajo y arriba del teclado y para ver el contenido completo de una linea podemos usar las flechas laterales. También podemos saltar de página en página con la tecla "espacio" y realizar busquedas de patrones con la tecla "/" seguido del patrón que buscamos (luego podemos saltar de patrón en patrón encontrado con la tecla "n" o shift + "n" para ir para atrás) entre otras opciones (para ver todas las opciones se puede tipear "help"). Para salir se usa la tecla "q".

### Otras salidas: 
- `journalctl -r`: Imprime la salida del comando en orden inverso (los últimos primero)
- `journalctl -f`: Nos permite ver el journal en tiempo real, las nuevas lineas irán apareciendo al final de la terminal.
- `journalctl -u servicio`: Nos muestra los registros de un servicio o unidad de systemd dado, por ejemplo, ssh.
	- ```
	  journalctl -u ssh

	  nov 29 19:13:49 ubuntu-curso systemd[1]: Starting OpenBSD Secure Shell server...
	  nov 29 19:13:55 ubuntu-curso sshd[825]: Server listening on 0.0.0.0 port 22.
	  nov 29 19:13:55 ubuntu-curso sshd[825]: Server listening on :: port 22.
	  nov 29 19:13:55 ubuntu-curso systemd[1]: Started OpenBSD Secure Shell server.
	  nov 29 19:14:04 ubuntu-curso sshd[828]: Accepted password for pale from 192.168.0.3 port 32854 ssh2
	  nov 29 19:14:04 ubuntu-curso sshd[828]: pam_unix(sshd:session): session opened for user pale by (uid=0)
	  lines 652-663/663 (END)
	  ```
	- Se puede ver la salida de varios servicios a la vez ingresandolos uno detras de otro `journalctl -u ssh -u cups`
	- Para ver los servicios que están corriendo en nuestro ordenados podemos correr `sudo systemctl -t service -a`
- `journalctl -b`: Muestra los eventos del inicio del equipo. Esta opción es muy útil para ver posibles errores al inicio o si algún servicio inició con errores. La opción "--list-boots" no permite ver la lista de inicios de los que se posee información. Para seleccionar uno de ellos podemos usar "-n" donde n es el número que aparece en la lista, por ejemplo, `journalctl -b -0` nos muestra los datos del último inicio. 
- `journalctl -k`: Muesta los eventos del kernel de linux.
- Filtrar por tiempo:
	- -S o --since: 
	- -U o --until: 
	- Formatos de tiempo:
		- Se pueden usar algunos nombres relativos:
			- yesterday
			- today
			- now
		- YYYY-MM-DD HH:MM:SS 
			- `journalctl -S "2021-01-10 17:15:00"` 
			- `journalctl -S -01h15min01sec` 
- Filtrar por categoría del mensaje: con la opción -p:
	- 0: emerg `journalctl -p 0` o `journalctl -p emerg`
	- 1: alert
	- 2: crit
	- 3: err
	- 4: warning
	- 5: notice
	- 6: info
	- 7: debug 

### Configuración:
**Persistencia**: Para lograr que se guarden los registros de arranque del sistema de manera persistente podemos habilitarlo de la siguiente forma:
```
mkdir /var/log/journal
systemd-tmpfiles --create --prefix /var/log/journal
systemctl restart systemd-journald
```

Otra manera de configurar la permanencia de los logs es a través del archivo /etc/systemd/journald.conf indicando Storage=persistent debajo de la sección [Journal].

# Tareas programadas:

Una de las tareas habituales de un administrador de sistemas consiste en la planificación de la ejecución de algún comando, script, etc de forma periodica y en lo posible haciendo el menor esfuerzo posible. Para esto linux cuenta con una utilidad llamada **cron** que nos permite realizar tareas repetitivas de manera automática y periódica. 

Cron despierta cada minuto y chequea si hay tareas para ejecutar en la tabla de tareas. Por su parte el comando **crontab** nos permite editar y visualizar la tabla de actividades (en realidad se trata de un archivo de texto plano).

Por defecto todos los usuarios pueden automatizar sus tareas mediante una tabla personal, la cual se encuentra en /var/spool/cron/crontabs/<usuario>. Cada linea del archivo corresponde a una tarea con las indicaciones de cuando se debe ejecutar.

## Crear una tarea:

Para crear una tarea utilizamos el comando `crontab -e`. La primera vez que lo ejecutamos nos pide que definamos que editor de textos vamos a utilizar:

```
crontab -e
no crontab for pale - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/vim.tiny
  4. /bin/ed

Choose 1-4 [1]: 2

```
Elegimos vim.basic. Una vez seleccionado el editor nos aparece el contenido del fichero con lineas comentadas conteniendo información y ejemplos:

```
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
```
En la última linea vemos el formato con el que se definen las tareas. 

```
 Ejemplo de definición de tareas:
 .---------------- m - minuto (0 - 59)
 |  .------------- h - hora (0 - 23)
 |  |  .---------- dom - day of month - dia del mes (1 - 31)
 |  |  |  .------- mon - mes (1 - 12) o jan,feb,mar,apr ...
 |  |  |  |  .---- dow - day of week - dia de la semana (0 - 6) (Sunday=0 o 7) o sun,mon,tue,wed,thu,fri,sat
 |  |  |  |  |
 *  *  *  *  * comando que se ejecuta cada minuto
*/5 *  *  *  * comando que se ejecuta cada 5 minutos 
17  *  *  *  * comando que se ejecuta todos los dias, cada hora al minuto 17
25  6  *  *  * comando que se ejecuta todos los dias a las 6:25
47  6  *  *  7 comando que se ejecuta los Domingos a las 6:47
52  6  1  *  * comando que se ejecuta el dia 1 de cada mes a las 6:52
55  8  *  9  1 comando que se ejecuta todos los lunes de septiembre a las 8:55
10,30,55  8  *  9  1 comando que se ejecuta todos los lunes de septiembre a las 8:10, 8:30 y 8:55
*/15 11 * * 1-5 comando que se ejecuta de lunes a viernes a las 11:00, 11:15, 11:30, 11:45
```
Como se puede ver cron nos permite usar "*" para indicar que una condición se cumpla siempre, */valor para indicar una frecuencia temporal, "," para separar valores y "-" para indicar una secuencia. También se puede especificar el momento deseado para la ejecución de una tarea empleando las siguientes indicaciones:

| Indicaciones    | Descripción                    |
|:---------------:|:------------------------------:|
| @reboot comando | Ejecutar al iniciar el sistema |
| @yearly comando | Ejecutar una vez por año       |
| @monthly comando| Una vez al mes                 |
| @weekly comando | Una vez por semana             |
| @daily comando  | Una vez por día                |
| @hourly comando | Cada 1 hora                    |

Para ver nuestra propia tabla de tareas agendadas utilizaremos `crontab -l`. El usuario root también puede ver (y también editar) la tabla de otros usuarios. Para eso se debe agregar la opción -u seguida del nombre del usuario en cuestión.

## Ubicaciones:

Además, los scripts que se depositen en las carpetas /etc/cron.hourly, /etc/cron.daily, /etc/cron.weekly, y /etc/cron.monthly se ejecutarán una vez por hora, por día, por semana, o por mes, respectivamente. Por lo general, ese es el caso de algunas aplicaciones que al momento de su instalación configuran su tarea agendada de mantenimiento propio.
```
ls /etc/cron.{hourly,daily,weekly,monthly}

/etc/cron.daily:
apport  apt-compat  bsdmainutils  dpkg  logrotate  man-db  popularity-contest  update-notifier-common

/etc/cron.hourly:

/etc/cron.monthly:

/etc/cron.weekly:
man-db  update-notifier-common
```

El administrador también puede editar el archivo /etc/crontab para definir actividades programadas. En este caso las lineas incluyen una columna adicional que especifica el usuario que ejecuta el comando:

```
cat /etc/crontab 
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
```

Tanto en este archivo como en nuestras tablas de tareas personales se pueden definir variables de entorno, es decir, podemos otorgarle valores a variables que queremos que esten disponibles durante s la ejecución de nuestras tareas. Por ejemplo:
- PATH contiene los directorios donde crontab podrá buscar archivos ejecutables ejecutables. Si el directorio donde se encuentra uno en particular forma parte de esta variable, podremos utilizar una ruta relativa (en vez de una absoluta) en el sexto campo de la entrada de crontab o septimo de /etc/crontab.
- SHELL especifica la shell a utiliza para ejecutar las tareas agendadas. Por defecto se usa /bin/bash
- MAILTO permite especificar una dirección de correo a la que se deberá enviar la salida de un programa (incluyendo cualquier error)

## Acceso a cron

Si no deseamos que todos los usuarios del sistema tengan acceso a cron podemos restringir el acceso creando (si no existen ya) los siguientes archivos para permitir y rechazar respectivamente:
- /etc/cron.allow
- /etc/cron.deny

En estos archivos se agregan los usuarios, uno por linea. En el caso del archivo .allow se encontrarán los usuarios autorizados y en .deny los que no lo están.

## Otras utilidades:
- anacron: permite ejecutar tareas agendadas que no corrieron mientras el equipo estaba apagado
- at: Permite ejecutar una tarea programada por única vez

# Gestión de procesos

El monitoreo y la manipulación de los procesos son actividades fundamentales para el administrador de sistemas. Cuando hablamos de procesos nos referimos a programas que se están ejecutando en un momento dado en el sistema. Estos a su vez pueden iniciar otros procesos, y así suscesivamente (se dice que estos procesos son hijos de los primeros).

Dependiendo de la forma en la que estos procesos son ejecutados los podemos clasificar en tres categorías:
- Procesos normales: Son ejecutados en una terminal y corren bajo el nombre de un usuario.
- Procesos daemon (servicios): También se ejecutan por un usuario pero corren en segundo plano, es decir, no tienen salida directa por una terminal. Estos procesos escuchan peticiones a través de un puerto.
- Procesos zombies: Son procesos que han finalizado su ejecución pero no han enviado su señal de finalización a su proceso padre o este no pudo recibir esta señal. Estos procesos pueden ser consecuencia de un error.

Los procesos en linux están representados en el directorio /proc, que es un directorio virtual que se genera durante el arranque. Este directorio contiene subdirectorios que se corresponden con el PID (valor numerico que identifica el proceso), cada uno de estos subdirectorios contiene archivos y directorios con información sobre el proceso.

Para poder ver el contenido de este directorio en un formato más amigable linux dispone de varias herramientas:

## Comando pstree

Permite observar la relación entre procesos padre y sus procesos hijos al visualizarlos en forma de árbol. Con la opción "-p"podemos cer el PID (identificador de procesos) de cada proceso. El PID de un proceso padre es, a su vez, llamado PPID (Parent Process ID) del proceso hijo.

```
pstree -p

systemd(1)─┬─accounts-daemon(722)─┬─{accounts-daemon}(756)
           │                      └─{accounts-daemon}(782)
           ├─agetty(763)
           ├─atd(749)
           ├─cron(725)
           ├─dbus-daemon(726)
           ├─multipathd(635)─┬─{multipathd}(636)
           │                 ├─{multipathd}(637)
           │                 ├─{multipathd}(638)
           │                 ├─{multipathd}(639)
           │                 ├─{multipathd}(640)
           │                 └─{multipathd}(641)
           ├─networkd-dispat(735)
           ├─polkitd(827)─┬─{polkitd}(828)
           │              └─{polkitd}(830)
           ├─rsyslogd(736)─┬─{rsyslogd}(764)
           │               ├─{rsyslogd}(765)
           │               └─{rsyslogd}(766)
           ├─snapd(738)─┬─{snapd}(866)
           │            ├─{snapd}(867)
           │            ├─{snapd}(868)
           │            ├─{snapd}(869)
           │            ├─{snapd}(881)
           │            ├─{snapd}(882)
           │            ├─{snapd}(925)
           │            └─{snapd}(1113)
           ├─sshd(826)───sshd(963)───sshd(1098)───bash(1099)───sudo(1115)───pstree(1117)
           ├─systemd(981)───(sd-pam)(982)
           ├─systemd-journal(377)
           ├─systemd-logind(744)
           ├─systemd-network(412)
           ├─systemd-resolve(685)
           ├─systemd-timesyn(686)───{systemd-timesyn}(690)
           ├─systemd-udevd(407)───systemd-udevd(1116)
           ├─udisksd(746)─┬─{udisksd}(770)
           │              ├─{udisksd}(783)
           │              ├─{udisksd}(831)
           │              └─{udisksd}(855)
           └─unattended-upgr(781)───{unattended-upgr}(902)
```

Si a continuación de la opción -p indicamos un PID dado, el árbol de procesos se mostrará comenzando por el proceso al que le corresponde dicho PID:

```
pstree -p 826

sshd(826)───sshd(963)───sshd(1098)───bash(1099)───pstree(1320)

```

## Comando ps

Este comando es el más utilizado para listar los procesos. Por defecto ps selecciona los procesos con el mismo valor de EUID (effective user ID) que el usuario actual y que están asociados a la misma terminal por la que se invoca. Y nos muestra el PID, la terminal asociada a ese proceso (TTY), el tiempo de CPU acumulado ([DD-]hh:mm:ss) y el nombre del ejecutable (CMD)

```
ps

    PID TTY          TIME CMD
   1099 pts/0    00:00:00 bash
   1394 pts/0    00:00:00 ps
```

Como se puede ver, ps no es interactivo, sino que nos devuelve una captura de los procesos que estaban corriendo en el momento de su ejecución.

Las opciones más comunes para visualizar todos los procesos (aunque con distinto grado de detalle) que están corriendo en el sistema de este comando son "-ef" y "aux" (esta última sin el signo "-"):

```
ps -ef | head -4

UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 08:00 ?        00:00:02 /sbin/init maybe-ubiquity
root           2       0  0 08:00 ?        00:00:00 [kthreadd]
root           3       2  0 08:00 ?        00:00:00 [rcu_gp]

ps aux | head -4

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  1.1 101972 11504 ?        Ss   08:00   0:02 /sbin/init maybe-ubiquity
root           2  0.0  0.0      0     0 ?        S    08:00   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   08:00   0:00 [rcu_gp]
```

Las opciones a, u, y x de manera separada devuelve una lista de a) todos los procesos que se están ejecutando en una terminal, u) muestra el estado de los procesos del usuario actual y qué cantidad de recursos está requiriendo cada uno, y x) indica la información de los servicios y procesos sin terminal. Al combinar estas opciones como aux podemos acceder a todos esos datos simultáneamente.

La opción -e nos permite seleccionar todos los procesos y -f nos provee información adicional.

Significado de cada columna:

- USER: usuario dueño del proceso
- PID: Process ID
- %CPU: Porcentaje de tiempo de CPU utilizado sobre el tiempo que el proceso ha estado en ejecución
- %MEM: Porcentaje de memoria física utilizada.
- VSZ: memoria virtual del proceso (KiB)
- RSS (Resident Set Size): cantidad de memoria física no swappeada que la tarea ha utilizado (KiB)
- TTY: Terminal asociada al proceso. El "?" significa que el proceso es un servicio que no utiliza ninguna terminal
- STAT: Estado. R: proceso en ejecución, S: proceso dormido o esperando que finalice un evento, X: proceso muerto, Z: proceso zombie. También podemos ver otros indicadores como la prioridad del proceso: \<: alta prioridad, N: baja prioridad, l: multi-threaded (corre en varios nucleos), +: proceso corriendo en primer plano 
- START: fecha y hora de inicio del proceso
- TIME: tiempo de CPU acumulado
- COMMAND: comando relacionado con el proceso

Además, ps nos permite adaptar la cantidad y el orden de las columnas a mostrar y hasta podemos ordenar los resultados en base a los valores de una de ellas mendiante las opciones -eo y --sort, respectivamente. Luego de -eo se agrega la lista de los campos que se desea incluir en la salida y luego de --sort el campo a partir del cual vamos a ordenar los datos indicando con los simbolos - o + si el orden es descendente o ascendente, respectivamente. Por ejemplo:

```
ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%mem

    PID    PPID CMD                         %CPU %MEM
    738       1 /usr/lib/snapd/snapd         0.0  3.7
    377       1 /lib/systemd/systemd-journa  0.0  2.3
    781       1 /usr/bin/python3 /usr/share  0.0  2.0
    735       1 /usr/bin/python3 /usr/bin/n  0.0  1.8
    635       1 /sbin/multipathd -d -s       0.0  1.8
    746       1 /usr/lib/udisks2/udisksd     0.0  1.3
```

Otras formas de visualzar las salidas del comando ps pueden consultarse en el manual de ps (`man ps`) en la sección Examples.

## Comandos kill y killall

Muchas veces es necesario interrumpir la ejecución de un proceso, ya sea porque nos olvidamos de incluir algún parámetro al ejecutar el comando, porque no hace lo esperadoo quizas está consumiendo muchos recursos. A esta acción la denominamos "matar" el proceso. Para matar un proceso especifico podemos usar el comando kill seguido del PID del proceso que deseamos eliminar. 

Supongamos que tenemos un programa denominado infinito.sh que se está ejecutando en una terminal y queremos matarlo. Primero podemos ver su PID con `ps aux | grep infinito.sh`, con el valor de PID podemos matar el proceso con el comando `kill PID_proceso`

```
ps aux | grep infinito.sh

pale        2338  0.2  0.3   7024  3612 pts/0    S    13:03   0:00 /bin/bash ./infinito.sh
pale        2344  0.0  0.0   6432   736 pts/0    S+   13:03   0:00 grep --color=auto infinito

kill 2338
```

Si existen varios procesos con el mismo nombre pero diferentes PID y deseamos eliminarlos a todos juntos, podemos usar el comando killall, cuya sintaxis es similar al comando kill pero se reemplaza el PID por el nombre del comando. Supongamos una situación donde infinito.sh se ejecutó varias veces y deseamos terminarlo.

```
ps aux | grep infinito
pale        2467  0.1  0.3   7024  3724 pts/0    S    13:06   0:00 /bin/bash ./infinito.sh
pale        2471  0.2  0.3   7024  3580 pts/0    S    13:06   0:00 /bin/bash ./infinito.sh
pale        2475  0.2  0.3   7024  3596 pts/0    S    13:06   0:00 /bin/bash ./infinito.sh
pale        2489  0.0  0.0   6432   668 pts/0    S+   13:06   0:00 grep --color=auto infinito

killall infinito.sh

[1]   Terminated              ./infinito.sh
[2]-  Terminated              ./infinito.sh
[3]+  Terminated              ./infinito.sh
```
También podriamos haber usado el comando `kill 2467 2471 2475` para matar uno por uno los procesos indicados.

Cuando se usa kill para matar un proceso, se le envía una señal indicandole que modifique su funcionamiento normal. Por defecto kill envia la señal SIGTERM que le indica al proceso que inicie su procedimiento de apagado normal. Sin embargo, kill puede enviar diferentes tipos de señales, las que pueden ser listadas mediante la opción -l:

```
kill -L
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX
```

De ellas, las mas importantes a tener en cuenta son **SIGHUP (1)**, **SIGKILL (9)** y **SIGTERM (15)**.
- **SIGHUP (1)**: es la señal que se envía cuando se cierra la terminal asociada al proceso para que se detenga.
- **SIGKILL (9)**: indica al proceso que debe detenerse de inmediato, sin darle tiempo a liberar adecuadamente los recursos que está usando. Esta señal no puede ser ignorada por el proceso. 
- **SIGTERM (15)**: le permite al proceso terminar su ejecución normalmente, lo que le permite liberar los recursos utilizados. Estaseñal puede ser ignorada por el proceso. Por esto puede ser necesario utilizar SIGKILL

Para seleccionar la señal a utilizar podemos ejecutar kill con las siguientes opciones:

```
# SIGTERM
kill -15 PID
kill -TERM PID
kill -SIGTERM PID
kill -s SIGTERM PID

# SIGKILL

kill -9 PID
kill -KILL PID
kill -SIGKILL PID
kill -s SIGKILL PID
```

## Comando top

El comando top, a diferencia de ps, nos devuelve información de los procesos que se actualiza cada una cierta cantidad de tiempo (3 segundos por defecto) y nos brinda otros datos útiles sobre el estado del sistema, como son el número de procesos en ejecución, % de memoria RAM ocupada, etc.

```
top

top - 15:35:35 up  7:34,  1 user,  load average: 0,00, 0,00, 0,00
Tasks: 104 total,   1 running, 103 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,0 us,  0,3 sy,  0,0 ni, 99,3 id,  0,0 wa,  0,0 hi,  0,3 si,  0,0 st
MiB Mem :    981,2 total,    264,6 free,    150,1 used,    566,6 buff/cache
MiB Swap:   1735,0 total,   1735,0 free,      0,0 used.    683,3 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
    685 systemd+  20   0   24160  13080   8984 S   0,3   1,3   0:09.67 systemd-resolve
   1607 root      20   0       0      0      0 I   0,3   0,0   0:30.91 kworker/0:0-events
   2767 pale      20   0    9232   3892   3232 R   0,3   0,4   0:00.88 top
      1 root      20   0  101972  11508   8444 S   0,0   1,1   0:03.41 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd
      3 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 rcu_par_gp
      6 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/0:0H-kblockd
      9 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 mm_percpu_wq
     10 root      20   0       0      0      0 S   0,0   0,0   0:00.47 ksoftirqd/0
     11 root      20   0       0      0      0 I   0,0   0,0   0:08.89 rcu_sched
     12 root      rt   0       0      0      0 S   0,0   0,0   0:00.25 migration/0
     13 root     -51   0       0      0      0 S   0,0   0,0   0:00.00 idle_inject/0
     14 root      20   0       0      0      0 S   0,0   0,0   0:00.00 cpuhp/0
```

Podemos dividir la salida de top en 2 partes:
- La primera parte consiste en la información general del sistema
- La segunda parte es la tabla de procesos.

### Información general:

- Fila 1:
	- top: nombre del programa
	- 15:35:35: hora actual
	- up   7:34: tiempo desde el inicio del equipo
	- 1 user: cantidad de usuarios
	- load average: 0,00, 0,00, 0,00: muestra la cantidad de procesos (en promedio) que están utilizando (o han estado esperando para utilizar) el CPU durante los últimos 60 segundos, 5 minutos, y 15 minutos, respectivamente. Para ilustrar el significado de estos valores, supongamos que los valores sean diferentes (digamos 0.64, 0.30, 0.69, por ejemplo). En este supuesto, 0.64 significa que durante los últimos 60 segundos el CPU ha estado desocupado el 36% del tiempo. De la misma manera, 0.30 y 0.69 indican que durante los últimos 5 y 15 minutos el CPU ha estado desocupado el 70% y el 31% del tiempo, respectivamente. 
- Fila 2: 
	- Tasks: 104 total,   1 running, 103 sleeping,   0 stopped,   0 zombie: indica la cantidad de procesos que están corriendo en elsistema y los estados de estos.
		- running: son los procesos que se encuentran corriendo actualmente. Se los puede ver en la tabla de procesos con la letra R (running)
		- sleeping: procesos que no están corriendo actualmente pero que se encuentran esperando que ocurra un evento para despertarse. Son los indicados con S en la tabla.
		- stopped: procesos que han sido detenidos. Se identifican con la letra T. 
		- zombie: procesos que no han finalizado aunque deberían haberlo hecho. Se identifican con la Z.
- Fila 3: Muestra el estado del CPU. 
- Fila 4 y 5: estado de la memoria RAM y SWAP respectivamente. Los valores se expresan en KiB (kibibytes). Las columnas de la fila 4 representan los valores de memoria física total, libre, usada y usada por la caché. En el caso de la fila 5 representan valores de memoria de intercambio (swap) total, libre, usada y memoria física disponible para iniciar un nuevo proceso sin virtualizar 

### Tabla de procesos:

- PID: Process ID.
- USER: Usuario dueño del proceso.
- PR: Prioridad de ejecución del proceso.
- NI: Es un valor que refleja la prioridad sobre el uso de los recursos del sistema otorgada a cada proceso en particular.
- VIRT: Cantidad de memoria virtual que el proceso está manejando.
- RES: Es la representación más cercana a la cantidad de memoria física que un proceso está utilizando.
- SHR: La cantidad de memoria compartida (potencialmente con otros procesos) disponible para este proceso en particular, expresada en KiB.
- S: Estado del proceso.
- %CPU: Porcentaje de tiempo de CPU utilizado por el proceso desde el último refresco de pantalla.
- %MEM: Indica el porcentaje correspondiente al uso de memoria física de un proceso en particular.
- TIME+: Tiempo de CPU que ha utilizado el proceso desde que inició, expresado en minutos:segundos.centésimas de segundo.
- COMMAND: Comando que se utilizó para iniciar el proceso o el nombre de este último.

top posee opciones que podemos indicar al ejecutar o también luego de haberlo hecho (modo interactivo).

Algunos ejemplos:
- `top -d 2`: actualizar cada 2 segundos.
- `top -o %MEM`: Ordenar salida por uso de RAM
- `top -p 326,459,11389` monitorear sólo los procesos indicados.
- `top -d 1 -n 3 -b > top_processes.txt`: -b: batch mode (no es interactivo), -d: actualización, -n: veces que se actualiza la información antes de terminar.

Opciones del modo interactivo:
- h: ayuda
- espacio: actualización manual
- d: actualización en segundos.
- q: salir de top
- u: mostrar procesos por usuario. Ej: u + pale + enter
- m: cambia la visualización de la memoria
- 1: Cambia los datos por CPU
- \<: ordena según la columna de la izquierda
- \>: ordena según la columna de la derecha
- W: Guarda los cambios

## Comando htop

Es una herramienta muy similar a top pero un poco más amigable.

## comandos nice y renice

Para modificar la prioridad de un proceso en ejecución podemos usar el comando renice. Los usuarios normales (con privilegios limitados) solo pueden aumentar la prioridad de los procesos de los cuales son dueños, mientras que root puede modificar este valor para cualquier proceso sin importar quién sea el dueño.

Mientras mayor sea la prioridad de un proceso, es menos nice (del inglés bueno) ya que consumirá más recursos del sistema. El valor de niceness puede ubicarse en algún lugar del intervalo comprendido entre -20 y 19. Estos dos límites representan la mayor y la menor prioridad posibles, respectivamente.


Por defecto, cualquier nuevo proceso se ejecuta con una prioridad igual a 0. Si deseáramos iniciarlo con una prioridad diferente, podemos hacer uso del comando nice seguido de la opción -n, del nuevo valor de niceness deseado, y del comando a ejecutar. Por ejemplo, iniciemos top con un valor de niceness igual a 10:

```
nice -n 10 top
```

Por ejemplo:
- `renice -n 10 -p 319`: cambia la prioridad del proceso 319 a 10 
- `renice -n 10 -u pale`: cambia la prioridad de todos los procesos del usuario pale a 10


# Anexo
```
KiB = kibibyte = 1024 bytes
MiB = mebibyte = 1024 KiB = 1,048,576 bytes
GiB = gibibyte = 1024 MiB = 1,073,741,824 bytes
TiB = tebibyte = 1024 GiB = 1,099,511,627,776 bytes
PiB = pebibyte = 1024 TiB = 1,125,899,906,842,624 bytes
EiB = exbibyte = 1024 PiB = 1,152,921,504,606,846,976 bytes
```


