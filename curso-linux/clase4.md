# Clase 4

## Temas:

- 10. Logs del sistema
- 11. Gestión de procesos
- 12. Ssh server

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
- [Otros](https://geekland.eu/logs-en-linux/)


