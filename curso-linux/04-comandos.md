
# Comandos

Vamos a agregar este repositorio a nuestro directorio personal para poder navegar por él, ya que este último está vacio.
```
git clone https://github.com/Pale24/wiki-linux/
```
Git es un software de control de versiones diseñado por Linus Torvalds.

A continuación vamos a ver algunos de los comandos mas comunes y que nos van a permitir navegar e interactuar con el sistema.

## pwd
Muestra el directorio donde estamos parados dentro del sistema de archivos.

## cd
Change directory. Cambia de directorios. Utilizamos el comando cd seguido de la ubicación a la que deseamos movernos. Las ubicaciones las podemos ingresar de forma relativa a nuestra ubicación actual o de forma absoluta desde el directorio raíz (/).

```
cd                              # Nos lleva al directorio por defecto del usuario con el que estamos logueados. /home/usuario
cd ~                            # Idem anterior "~" simboliza al directorio del usuario
cd .                            # Nos lleva al mismo directorio en el que estamos. Para linux "." indica el directorio actual
cd ..                           # Nos lleva al directorio anterior en el arbol de directorios. Si estamos en /home/usuario -> /home
cd /                            # Nos lleva al directorio raiz 
cd Documentos                   # Nos lleva al directorio Documentos (que está dentro del directorio actual). Ruta relativa
cd /home/usuario/Documentos     # Nos lleva al directorio /home/usuario/Documentos. Ruta absoluta
cd wiki-linux
```

## ls
Muestra el contenido de un directorio.

Permite distinguir los siguientes tipos de contenidos en un directorio. Si el argumento representa un archivo, enlace simbólico, dispositivo, etc., veremos datos sobre el mismo en la salida de ls.

- En blanco (en el caso de que el color de fondo de la terminal sea negro u otro color oscuro, que es lo más común), los archivos de texto o binarios no ejecutables.
- En verde, los archivos de texto (scripts) y otros binarios ejecutables.
- En celeste, los enlaces simbólicos.
- En fondo negro con letras rojas, los enlaces simbólicos rotos. Nos referimos a aquellos que apunten a un recurso del sistema que no existe.
- En amarillo, los dispositivos de bloques.
- En violeta, los archivos de imágenes o archivos temporales.
- En rojo, los archivos comprimidos.


```
ls              # Lista el contenido de la carpeta actual
ls carpeta      # Lista el contenido de la carpeta "carpeta"
ls -l           # Lista el contenido como una lista en vez de solo los nombre 
                # y pone información adicional como permisos, propietario (usuario y grupo),
                # tamaño, fecha de modificación.
ls -lh          # Idem -l pero el tamaño es más legible (no está en bits sino en Kb, Mb, etc)
ls -lS          # Ordena por tamaño (mayor a menor)
ls -lSr         # Ordena por tamaño (menor a mayor), r: invierte el orden
ls -lt          # Ordena por fecha de modificación, r: invierte el orden
ls -a           # Muestra archivos ocultos
ls -lR			# Muestra el contenido de forma recursiva (contenido de los directorios que estan dentro de la carpeta)
```

Al listar con el modificador -l obtenemos mayor información de los archivos y directorios. La salida del comando en este caso está dispuesta en columnas para que nos resulte más sencillo reconocerla. A continuación vemos cómo queda cada columna:

`-rw-rw-r-- 1 pale pale 1710 sep 22 13:21 amber20.md`

- 1. El primer caracter que aparece en la primera columna indica si lo que estamos viendo es un archivo (-), un directorio (d), un enlace simbólico (l) por nombrar algunos ejemplos. A continuación, vemos los permisos que poseen sobre el mismo a) el dueño del archivo, b) el grupo dueño, y c) el resto de los usuarios del sistema.
- 2. Esta columna nos dice si este objeto posee enlaces que lo estén apuntando.
- 3. Usuario dueño del objeto.
- 4. Grupo dueño del objeto.
- 5. Tamaño en bytes del objeto. Para mostrar este dato en una unidad más amigable (KB, MB, etc.) podemos agregar la opción -h.
- 6. Fecha de última modificación del objeto.

## mkdir

Crea directorios

```
mkdir dir1
# crear directorio y sus directorios padres (si no existen)
mkdir -p dir1/dir2/dir3
```

## cat
Puede visualizar y concatenar archivos de texto. Se suele usar para mostrar su contenido por pantalla
```
cat archivo.txt 
cat -n archivo.txt # muestra el numero de linea antes de cada una de estas
```

## more
Muestra el texto poco a poco, con el espacio avanzamos una pantalla, con el intro una linea, con la q salimos y la h nos muestra ayuda.

## less
Parecido a more, pero más potente. Acepta los controles mencionados y también puede realizar búsquedas o modificaciones en los ficheros. Recomendable para ficheros muy grandes.

## head
Muestra las N primeras líneas de uno o varios ficheros de texto. Por defecto las 10 primeras

- -n: cambia la cantidad de lineas a mostrar
- -q: no muestra el nombre de los ficheros si se le pasan varios al mismo tiempo

## tail 
Igual que head pero muestra las últimas lineas
- -f: muestra las ultimas lineas de forma interativa

## touch 
Actualiza el tiempo de acceso y modificación de un archivo al tiempo actual. Si el archivo no existe lo crea vacio.
```
touch file
```

## nano	
Editor de texto
```
nano file
```
Si el archivo no existe, lo crea. Las opciones pueden verse en el menú inferior de la ventana del navegador.

## vi/vim 

```
vi file
```

Al invocar a vi seguido del nombre de un archivo, lo crea si no existe. En la pantalla aparecerá la posición actual del cursor resaltada, las líneas en blanco indicadas con el símbolo ~, y en la parte inferior la línea de estado. En este sector se mostrará el nombre del archivo y el número de caracteres que contiene.

Existen dos modos de operación en el vi:
- Modo entrada: Se usa para añadir texto al archivo.
- Modo comando: Es el modo de operación por defecto. Se usa para introducir comandos que realizan funciones específicas del editor.

Cuando se abre por primera vez un archivo, vi siempre se encuentra en modo comando. Por esa razón, antes de poder escribir texto en el archivo se necesita presionar una de las teclas que habilitan el modo entrada:
- i (insert), para insertar texto en la posición actual del cursor.
- a (append) para insertar texto después de la posición actual del cursor.

Ahora podemos empezar a escribir sobre el archivo. Para grabar nuestro trabajo sin salir del vi basta pulsar la secuencia:

```
Esc :w [ENTER]
```
Para salir cuando no se han hecho modificaciones:
```
Esc :q [ENTER]
```

Para salir cuando se han hecho modificaciones:

Si queremos descartar: 
```
Esc :q! [ENTER]
```

Si queremos guardar los cambios: 
```
Esc :wq [ENTER]
```

## cp
copia archivos
```
cp file1 file2			# copia file1 a file2 en el mismo directorio
cp file1 dir1/file2		# copia file1 a dir1 con otro nombre
cp -i file1 file2		# copia file1 a file2 y pregunta al usuario si desea sobreescribir 
cp -p file1 file2		# copia file1 a file2 preservando los permisos, grupo y dueño al copiar
cp -v file1 file2		# modo verbose (da más información de lo que esta haciendo)
cp -r dir1 dir2/		# copia en forma recursiva dir1 en dir2
# copia muchos archivos y directorios de origen en uno de entrada
cp -r file1 file2 dir1 dir2 destination_directory/	
cp {file1, file2, file3} directorio_destino/
```

## mv
```
mv file1 file2 			# renombrar el archivo
mv file1 dir1/			# mueve file1 a dir1
mv -i file1 dir1/		# pregunta si queremos sobreescribir file2
mv -n file1 dir1/		# previene que se sobreescriba un archivo
mv -u file1 dir1/		# mueve file1 solo si el origen es nuevo respecto al destino o si no existe en el destino
mv file1 dir1/file2		# mueve file1 a dir1/file2
# mueve todos los archivos y carpetas de origen a destino
mv file1 file2 dir1/ dir2/ destino/
```

## rm
Elimina objetos
```
rm file1			# elimina file1
rm -v file1			# modo verboso
rm -r dir1/			# elimina dir1 y su contenido
rm -rf dir1/		# elimina dir1 y su contenido sin imprimir salida
rm -ri file1 dir1/	# elimina file1 y dir1 pidiendo confirmación
```

## shred
Elimina de forma segura
```
shred -vu -n 100 file1 # 100 ciclos de sobreescritura
```

## rmdir
Elimina directorios vacios

## stat

```
stat file
```
La información que se puede obtener de stat es:

- Fichero: El nombre del fichero (en linux todo es un fichero, directorios, dispositivos, etc). Usualmente, es el nombre que se le pasa al comando pero puede cambiar si le pasamos un enlace simbolico.
- Tamaño: El tamaño del archivo en bytes.
- Bloques: El numero de bloques del sistema de archivos requeridos para ser almacenados en el disco.
- Bloques E/S: Tamaño del bloque.
- Tipo de archivo: El tipo de objeto. Los más comunes son archivos y directorios, pero también pueden ser links, sockets, etc.
- Dispositivo: El número de dispositivo en hexadecimal y decimal. Es el ID del disco donde está almacenado el archivo.
- Inode: Número de inodo. Esto es el número del objeto en una base de datos. Cada inodo identifica a un archivo.
- Enlaces: Este número indica la cantidad de enlaces duros que apuntan al archivo. Cada archivo se identifica por un inodo. Sin embargo, en linux podemos crear enlaces duros, que son archivos que apuntan al mismo inodo, por lo que si eliminamos uno de los archivos ahora el inodo queda disponible para el otro. Cada vez que se crea un enlace duro este número crece y cuando se elimina decrece. Si el número es igual a cero, el inodo se elimina. Si se usa co un directorio este número representa la cantidad de archivos en el directorio, incluyendo "." y ".." que son archivos que representan al propio directorio y al directorio padre respectivamente.
- Accesso: Indica los permisos de acceso en notación octal y tradicional rwx (read, write, executs).
	- Uid: User ID y nombre de la cuenta del usuario dueño.
	- Gid: Group ID y nombre del grupo dueño.
- Accesso: Fecha del último acceso al archivo.
- Modificación: Fecha de la última modificación del contenido del archivo.
- Cambio: Fecha del último cambio del archivo. Esto implica el cambio del contenido o de los atributos (por ejemplo, cambiar los permisos). En el caso de cambiar los atributos, el tiempo de modificación no se altera.
- Creación: Reservado para ver la fecha original de creación del archivo, pero no se implementa en linux.

## locate y updatedb
Para realizar búsquedas rápidas de archivos en Linux disponemos de estas dos herramientas que se utilizan en conjunto. El propósito de locate es buscar la ubicación de archivos en las bases de datos escritas por updatedb. esta herramienta busca archivos que tengan la expresión buscada y la lsta linea por linea. La función de updatedb es actualizar la base de datos, por ejemplo, si creamos un archivo recientemente no va a estar indexado en la base de datos y, por tanto, si queremos que aparezca con locate debemos ejecutar updatedb previamente (updatedb se ejecuta diariamente en segundo plano para actualizar la base de datos).

La sintaxis básica de locate es la siguiente:

locate [OPCIONES] [PATRÓN]

- -r (--regexp): PATRÓN puede reemplazarse por una expresión regular
- -i (--ignore-case): ignora mayúsculas o minúsculas al buscar el patrón.
- -b '\PATRON': permite buscar sólo los archivos que coinciden con el patrón (entre comillas simples y barra invertida adelante.

```
locate -r clase\[1-4\].md # Utilizar expresión regular
locate -i CLASE1.MD # Ignorar mayúsculas y minúsculas
locate -b '\clase1.md' # Sólo si existe un archivo clase1.md
```

## find

Con `find` podemos realizar busquedas por distintos criterios como nombre, dueño, grupo dueño, permisos, tipo de archivo, etc.

Sintaxis:

find [RUTA] [OPCIONES]

```
find curso-linux -name '*.md'        # busca archivos con el patrón .md en el directorio curso-linux
find . -size +10k -size -20480k      # busca archivos de tamaño entre 10kb y 20480 kb
find . -type f -user pale -mtime -60 # busca archivos del usuario pale que se hayan modificado hace menos de 60 días 
find . -type f -user pale -mtime +60 # busca archivos del usuario pale que se hayan modificado hace más de 60 días 
find . -type f -user pale -mtime 60  # busca archivos del usuario pale que se hayan modificado hace exactamente 60 días 
find . -type f -perm -o=x            # busca archivos con permisos de ejecución
```

## whereis

Al realizar búsquedas con whereis podemos especificar qué estamos buscando. 
- Si nos interesa encontrar los binarios usamos la opción -b. 
- Si queremos el archivo correspondiente al man page, usamos -m.
- Con -s indicaremos que deseamos ubicar el código fuente. 
- Si se omiten estas opciones, whereis devolverá los tres recursos mencionados simultáneamente. 
- También podemos combinar dos de ellas para devolver la información correspondiente.

```
whereis htop
htop: /usr/bin/htop /usr/share/man/man1/htop.1.gz

whereis -b htop
htop: /usr/bin/htop

whereis -m htop
htop: /usr/share/man/man1/htop.1.gz
```

## which
El comando which muestra la ruta absoluta del programa que le indiquemos.

```
which python
/usr/bin/python
```

## sudo
El comando sudo permite a los usuarios no root ejecutar comandos que normalmente requerirían privilegios de superusuario. Se antepone al comando que se quiere ejecutar como administrador. También permite cambiar de usuario. Sólo los usuarios que se encuentran en el grupo sudo pueden hacer uso de este comando.

## lspci
Muestra información sobre los buses PCI y los dispositivos que tienen conectados.
- "-v": Amplia la información. "-vv": más detalles
- "-s": Muestra información sólo del dispositivo seleccionado.

## lsusb
Muestra información sobre los buses y dispositivos usb conectados.
- "-v", "-vv" y "-s" igual a lspci
- "-t": muestra información en árbol y la velocidad del puerto

## history
Podemos buscar instrucciones que hayamos usado anteriormente con la tecla de la flecha para arriba (también se puede bajar con la tecla de abajo). Una vez que encontramos la instrucción que queremos, podemos ejecutarla o modificarla.

Tambén podemos buscar la instrucción combinando las teclas ctrl + r y entonces puedo empezar a buscar poniendo las palabras que creo que tenia y cuando la encuentro la selecciono. Si la que sale no es la que busco puedo volver a tipear ctrl + r y voy viendo las opciones.

El comando history nos abre un listado de instrucciones anteriores, para seleccionar una de ellas rapido podemos ingresar !numero de la orden. Por ejemplo !1000.

## echo

Muestra el texto que recibe.

Opciones:
- -e: Interpreta los caracteres especiales después de \ `echo -e "Hola \nMundo"` Sin el -e se interpreta de forma literal
- Al poner comillas dobles interpreta las variables del sistema que estan adentro. Si uso comillas simples esto no pasa.

## type

El intérprete de comandos BASH tiene comandos internos y otros que son ficheros ejecutables externos. Podemos usar la orden **type** seguido del nombre del comando para saber a qué categoría pertenece dicho comando.

```
type cd 
cd es una orden interna del intérprete de ordenes
type find
find is /usr/bin/find
```

## man

Muestra ayuda de comandos, ficheros de configuración, etc

## uname

Muestra información sobre el S.O. (sin parámetros sólo el ombre). -a muestra toda la información.

```
uname
Linux
uname -a
Linux r2-d2 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

## set
Muestra o modifica la configuración de nuestro entorno. Sin parámetros visualiza variables del sistema y con -o una lista de las opciones y sus estados. (con -o opción activamos y con +o desactivamos).

## export
Crea o modifica variables de entorno.

Ejemplo: `export NOMBRE=Pablo`

## unset
Elimina una variable del entorno.

Ejemplo: unset NOMBRE

## env
Sin parámetros muestra las variables de entorno. Lo podemos usar para ejecutar un comando modificando el valor de variables de entorno.

Ejemplo: `env PATH=/new/path /bin/bash`

## Apagado

Desde el terminal podemos enviar comandos para apagar o reiniciar el sistema:

- shutdown: apaga el sistema de forma planificada
- halt: apaga el sistema sin enviar señal ACPI de apagado de alimentación electrica (apaga solo el software - SO -)
- poweroff: apaga el sistema con señal ACPI 
- reboot: reinicia el sistema


| Acción            |            |               |                 |
|-------------------|------------|---------------|-----------------|
| Reiniciar         | `reboot`   | `shutdown -r` | `halt --reboot` |
| Cerrar el sistema | `halt`     | `shutdown -h` | `reboot --halt` |
| Apagar            | `poweroff` | `shutdown -P` | `halt -p`       |

## Ejemplos:

```
man halt # info de halt, poweroff y reboot
man shutdown # solo info de shutdown
whereis halt
# Resultado
# halt: /sbin/halt /usr/share/man/man8/halt.8.gz
ls -l /sbin/halt
# lrwxrwxrwx 1 root root 14 jul 21 16:00 /sbin/halt -> /bin/systemctl
ls -l /sbin/ | grep systemctl
# lrwxrwxrwx 1 root root        14 jul 21 16:00 halt -> /bin/systemctl
# lrwxrwxrwx 1 root root        14 jul 21 16:00 poweroff -> /bin/systemctl
# lrwxrwxrwx 1 root root        14 jul 21 16:00 reboot -> /bin/systemctl
# lrwxrwxrwx 1 root root        14 jul 21 16:00 runlevel -> /bin/systemctl
# lrwxrwxrwx 1 root root        14 jul 21 16:00 shutdown -> /bin/systemctl
# lrwxrwxrwx 1 root root        14 jul 21 16:00 telinit -> /bin/systemctl

halt # Cierra el sistema pero no lo apaga
# System halted
shutdown +5 "El sistema se apagará en 5 min"
shutdown -c # cancela la orden anterior con shutdown
```
shutdown [opción] TIEMPO [mensaje]

Opciones:
- -c: cancela una orden programada de shutdown
- -k: envía mensajes pero no apaga

El tiempo puede expresarse en horas y minutos (HH:MM), el tiempo en minutos que faltan para el apagado: +M o con la palabra **now**

