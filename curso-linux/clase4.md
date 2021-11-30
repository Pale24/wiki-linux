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

# Tareas programadas ???

# Gestión de procesos


