# Clase 2

## Temas:
- 5. Gestión de paquetes
	- Dpkg y rpm
	- Apt y yum
	- Código fuente
- 6. Bibliotecas compartidas
- 7. Gestión de usuarios
    - Crear, eliminar y permisos usuarios
	- Grupos
	- Permisos de directorios y archivos

# Gestión de paquetes
Cuando hablamos de "paquetes" de software nos referimos a los programas, datos y otros archivos que se requieren para realizar una función determinada. También suele utilizarse el termino programa o software para referirse a los mismos. 

Podemos definir a la gestión de paquetes como las acciones de instalar, actualizar o eliminar paquetes de nuestro sistema. En linux, en la actualidad, estas tareas pueden llevarse a cabo de 3 maneras diferentes:

- **Archivos precompilados:** son archivos ejecutables **compatibles para una distribución en particular** y que pueden instalarse de manera local con una herramienta de la línea de comandos.
- **Repositorios:** son servidores centrales, mantenidos generalmente por una distribución en particular, por la comunidad, o por otros terceros, en los que se dispone de una colección de software para la instalación a través de internet o de una red local. Un repositorio puede residir en un servidor remoto (que es lo más común) o en un (o varios) dispositivos de almacenamiento locales.
- **Código fuente:** es la forma inicial mediante la cual se distribuía el software en Linux en sus comienzos. Aunque ha sido superada por las dos anteriores, todavía continúa usándose para ofrecer a los usuarios finales la posibilidad de compilar un programa dado con opciones particulares. También se utiliza este método de distribución de software para poner al alcance del público una versión más actualizada que tenga mayores prestaciones que las presentes en los repositorios. 

## Archivos precompilados
Estos comandos administran paquetes desde la línea de comandos luego de obtener un archivo del paquete precompilado (no los descarga). Existen diferentes utilidades para las difentes distribuciones como dpkg (Debian y derivados) y rpm (CentOS y familiares), solamente instalan el programa presente en el archivo .deb o .rpm pero sin instalar dependencias.


### Comando dpkg y paquetes .deb 
Administra los paquetes desde la línea de comandos. Se usan ampliamente no solo en Debian, sino también en sus derivados, como Ubuntu y los derivados de él.

La mayoría de las veces, un paquete puede depender de otros para que funcione. Por ejemplo, un editor de imágenes puede necesitar bibliotecas para abrir archivos JPEG, u otra utilidad puede necesitar un kit de herramientas como Qt o GTK para su interfaz de usuario.
dpkg verificará si esas dependencias están instaladas en su sistema y no podrá instalar el paquete si no lo están. En este caso, dpkg listará qué paquetes faltan. Sin embargo, no puede resolver dependencias por sí mismo. Depende del usuario encontrar los paquetes .deb con las dependencias correspondientes e instalarlos.

Opciones:
- -i: instala un paquete. Necesita la ruta completa al archivo .deb a instalar. `dpkg -i apache2_2.4.41-4ubuntu3.8_amd64.deb`. Si hay errores: `apt --fix-broken install`
- -s: muestra información y el estado del paquete instalado. `dpkg -s apache2`
- -I: muestra información de un archivo .deb antes de instalar. `dpkg -I apache2_2.4.41-4ubuntu3.8_amd64.deb`
- -l: lista todos los paquetes que coinciden con un patrón determinado. `dpkg -l apache*`
- -L: muestra todos los ficheros que ha instalado un paquete. `dpkg -L apache2_2.4.41-4ubuntu3.8_amd64.deb`
- -S: muestra los paquetes que contienen archivos que coincidan con el patrón indicado. `dpkg -S apache*`
- -r: borra un paquete pero deja los archivos de configuración (/etc/paquete.conf, etc). Se usa el nombre del programa, no el archivo de instalación `dpkg -r apache2`
- -P: borra un paquete incluyendo todos los archivos de configuración. `dpkg -P apache2`

**dpkg-reconfigure:** Se utiliza para reconfigurar un paquete instalado en el sistema. `dpkg-reconfigure nslcd`

Cuando se instala un paquete, hay un paso de configuración llamado post-install donde se ejecuta un script para configurar todo lo necesario para que el software se ejecute, como permisos, ubicación de archivos de configuración, etc. Esto también puede generar algunas preguntas de configuración al usuario para establecer preferencias sobre cómo se ejecutará el software.

A veces, debido a un archivo de configuración dañado o con formato incorrecto, es posible que se quiera restaurar las configuraciones de un paquete a su estado “funcional”. O puede que se desee cambiar las respuestas que se dieron a las preguntas de configuración inicial. 

Este programa realizará una copia de seguridad de los archivos de configuración antiguos, descomprimirá los nuevos en los directorios correctos y ejecutará el script post-install proporcionado por el paquete, como si el paquete se hubiera instalado por primera vez.

### Comando rpm y paquetes .rpm
El comando rpm administra los paquetes en Red Hat y derivados como Fedora o CentOS

Opciones:
- -i: Instala un paquete. Necesita la ruta completa del archivo .rpm a instalar
- -U: Instala un paquete si no existe en el sistema o lo actualiza si ya está instalado.
- -F: Actualiza un paquete sólo si está instalado.
- Para todas las opciones anteriores se puede añadir (se añaden despues de las anteriores):
    - h: Indica el progreso de la operación
    - v: muestra el progreso de cada paquete. por ejemplo -ihv
- -e: Elimina un paquete
- -V: verifica un paquete. Muestra si está instalado, si ha cambiado, etc.
- -q: Consultas sobre paquetes
    - a: todos los paquetes
    - i paquete: información de un paquete en particular
    - f: muestra los paquetes que contienen un archivo especificado
    - l: muestra los archivos que se modificaron al instalar un paquete
    - p: info sobre un paquete que no se ha instalado (es necesaria la ruta al archivo de instalación)

Ejemplos:
```
rpm -i htop...rpm
rmp -e htop
```

## Repositorios
Como mencionamos previamente un repositorio es una ubicación de almacenamiento desde la cual el sistema recupera, instala y actualiza aplicaciones y maneja automáticamente las dependencias. Según la distribución linux que usemos vamos a tener diferentes herramientas para su uso. En el caso de derivados de Debian utilizaremos **Advanced Package Tool (apt)**, mientras que en derivados de Red Hat se utiliza la herramienta **YellowDog Updater Modificado (yum)**.

### Comando apt
No es un sustituto de dpkg, puede considerarse como una interfaz, que optimiza la funcionalidad. Por ejemplo, la resolución de dependencias.

Trabaja en conjunto con los repositorios de software que contienen los paquetes disponibles para instalar. Dichos repositorios pueden ser un servidor local o remoto o (menos común) incluso un disco CD-ROM.

Las distribuciones de Linux, como Debian y Ubuntu, mantienen sus propios repositorios, y los desarrolladores o grupos de usuarios pueden mantener otros repositorios para proporcionar software que no está disponible en los principales repositorios de distribución.

#### Ubicación
Las listas de repositorios se encuentran en el archivo **/etc/apt/sources.list** y en los archivos dentro de la carpeta **/etc/apt/sources.list.d**. El formato de estos archivos es similar:

```
# deb cdrom:[Kubuntu 20.04.3 LTS _Focal Fossa_ - Release amd64 (20210819.1)]/ focal main multiverse restricted universe

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://ar.archive.ubuntu.com/ubuntu/ focal main restricted
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://ar.archive.ubuntu.com/ubuntu/ focal-updates main restricted
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://ar.archive.ubuntu.com/ubuntu/ focal universe
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal universe
deb http://ar.archive.ubuntu.com/ubuntu/ focal-updates universe
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal-updates universe
## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://ar.archive.ubuntu.com/ubuntu/ focal multiverse
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal multiverse
deb http://ar.archive.ubuntu.com/ubuntu/ focal-updates multiverse
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://ar.archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse
# deb-src http://ar.archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu focal partner
# deb-src http://archive.canonical.com/ubuntu focal partner

deb http://security.ubuntu.com/ubuntu focal-security main restricted
# deb-src http://security.ubuntu.com/ubuntu focal-security main restricted
deb http://security.ubuntu.com/ubuntu focal-security universe
# deb-src http://security.ubuntu.com/ubuntu focal-security universe
deb http://security.ubuntu.com/ubuntu focal-security multiverse
# deb-src http://security.ubuntu.com/ubuntu focal-security multiverse

# This system was installed using small removable media
# (e.g. netinst, live or single CD). The matching "deb cdrom"
# entries were disabled at the end of the installation process.
# For information about how to configure apt package sources,
# see the sources.list(5) manual.
deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /
# deb-src https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /
```

Veamos ahora en detalle cómo están compuestas las líneas de arriba:

- deb y deb-src indican si el repositorio contiene paquetes binarios compilados previamente o los paquetes fuente (que contienen todo lo necesario para que uno compile un programa en particular), respectivamente.
- Luego tenemos la dirección URL del repositorio.
- El nombre de la distribución para la que se proporcionan los paquetes. Un repositorio puede alojar paquetes para múltiples distribuciones. 
- Cada componente representa un conjunto de paquetes. Estos componentes pueden ser diferentes en diferentes distribuciones de Linux. Por ejemplo, en Ubuntu y derivados, son:
	- main: contiene paquetes de código abierto con soporte oficial.
	- restricted: contiene software de código cerrado con soporte oficial, como controladores de dispositivo para tarjetas gráficas, por ejemplo.
	- universe: contiene software de código abierto mantenido por la comunidad.
	- multiverse: contiene software no compatible, de código cerrado o con patente gravada.
- Las líneas que comienzan con el carácter # se consideran comentarios y se ignoran.

**El Directorio /etc/apt/sources.list.d**

Dentro del directorio /etc/apt/sources.list.d puede agregar archivos con repositorios adicionales para ser utilizados por APT, sin la necesidad de modificar el archivo principal /etc/apt/sources.list. Estos son archivos de texto simples, con la misma sintaxis descrita anteriormente y la extensión de archivo .list.

#### Agregar un repositorio nuevo

Para agregar un nuevo repositorio de paquetes, se puede agregar la línea correspondiente al final de sources.list o crear un archivo en /etc/apt/sources.list.d, se guarda el archivo y se agrega la clave pública con el comando curl o wget:

```
# Ejemplo de https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#using-deb-packages-recommended
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" >> /etc/apt/sources.list.d/mongodb-org-5.0.list
curl -L https://www.mongodb.org/static/pgp/server-5.0.asc | apt-key add -
```

#### Actualización de los Índices de Paquetes

Antes de instalar o actualizar un software con APT, se recomienda actualizar primero el índice de paquetes para recuperar información sobre paquetes nuevos y actualizados. Esto se hace con el comando apt o apt-get, seguido del parámetro update:

```
apt update
```

#### Instalar y Remover Paquetes

Con los índices de paquetes actualizados, ahora se puede instalar un paquete. Esto se hace con apt install, seguido del nombre del paquete que desea instalar:

```
apt install nginx
```

Del mismo modo, para eliminar un paquete, use apt remove, seguido del nombre del paquete:

```
apt remove nginx
```
Hay que tener en cuenta que cuando se elimina un paquete, los archivos de configuración correspondientes quedan en el sistema. Para eliminar el paquete y cualquier archivo de configuración, hay que usar el parámetro purge en lugar de remove o el parámetro remove con la opción --purge:

#### Actualizar Paquetes

APT se puede utilizar para actualizar automáticamente cualquier paquete instalado a las últimas versiones disponibles desde los repositorios. Esto se hace con el comando apt upgrade. Antes de ejecutarlo, primero debe actualizarse el índice de paquetes con apt update:

```
apt update
apt upgrade
```

#### Reparar dependencias rotas

Tener dependencias rotas significa que uno o mas paquetes instalados dependen de otros paquetes que no han instalado o que fueron eliminados. Esto puede pasar al instalar un paquete de forma manual (con dpkg) o por un error. Para resolver este problema se utiliza el comando apt install -f, que intentará arreglar las dependencias que faltan. 

#### Caché

Al instalar un paquete, el archivo .deb se descarga antes de instalarlo. Por defecto, este directorio es /var/cache/apt/archives. Por su parte los archivos descargados parcialmente se copian a /var/cache/apt/archives/partial/.

Con el paso del tiempo este directorio puede ser bastante grande. Para recuperar espacio, se puede vaciar la caché utilizando el comando apt clean. Esto eliminará el contenido de ambos directorios.

Con el comando apt-cache se puede realizar operaciones sobre el indice de paquetes, como buscar un paquete especifico o listar qué paquetes contienen un archivo específico. 

Con apt-cache search seguido de un patrón se puede realizar la búsqueda de paquetes que contienen dicho patrón, ya sea en el nombre del paquete, la descripción o en los archivos. 

```
apt-cache search avogadro
```

Luego con apt-cache show seguido del paquete de interes se puede ver mas información acerca de este.

```
apt-cache show avogadro
```

#### Otros comandos:
##### Aptitude:
Actualmente no viene instalado en muchas distribuciones,

`aptitude search vlc`

Da mas información sobre el paquete:

- i   apt                             - Advanced front-end for dpkg
- pi  apt-build                       - frontend to apt to build, optimize and in
- cp  apt-file                        - APT package searching utility -- command-
- ihA raptor-utils                    - Raptor RDF Parser utilities

Cada resultado de la búsqueda aparece en una línea distinta. El primer carácter de cada línea indica el estado actual del paquete: los estados más comunes son **p**, no se encontró ninguna señal de que tal paquete exista en el sistema, **c**, el paquete se eliminó pero sus ficheros de configuración permanecen en el sistema, **i**, el paquete está instalado, y **v**, que significa que el paquete es virtual. El segundo carácter indica la acción programada (de existir, si no, verá un espacio en blanco) para el paquete. Las acciones principales son **i**, el paquete se va a instalar, **d**, el paquete se va a eliminar, y **p**, que significa que el paquete y sus ficheros de configuración se van a eliminar completamente (purgar). Si el carácter es **A**, es que el paquete se instaló automáticamente.


Para mas información consultar el manual `man aptitude`

### Comando yum:
Utiliza repositorios que se configuran en ficheros dentro del directorio /etc/yum.repos.d/. Ejemplo: CentOS-Base.repo

```
[base]
enabled = 0 # 0 = Desactivado; 1 = Activado
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
```

Nombre de repositorio, **mirrorlist** contiene  la URL a una lista de servidores, yum elegirá el más rápido. Si queremos una comprobación GPG lo activamos a 1 e indicamos el fichero de la llave.

YUM autocompleta algunas líneas con las variables $basearch (arquitectura) y $baserelease (versión de la distribución)

Variables de YUM:
- update: actualiza uno o más paquetes
- install: instala uno o más paquetes
- remove: desinstala uno o más paquetes
- check-update: comprueba actualizaciones de los paquetes que están instalados
- upgrade: actualiza la versión a la última que tengamos configurada en los repositorios.
- clean: limpia el directorio caché.
- list: lista los paquetes, podemos especificar all, available, updates, installed, obsoletes, recent
- repolist: lista los repositorios.
- info: muestra información de un paquete determinado
- search: busca un paquete que cntenga un patrón determinado en su nombre o descripción.

### Comando dnf
Es una evolución de YUM creada por Fedora. Está programado en una versión mas moderna de python y mejora la eficacia de YUM.

### Comando zypper
Es el gestor de paquetes de SUSE. También derivado de YUM.

## Código fuente

Com podemos ver los gestores de paquetes son herramientas que nos facilitan la tarea a la hora de instalar o actualizar paquetes en Linux. Sin embargo, existen muchas situaciones donde es necesario tener que instalar los paquetes desde el código fuente. Este es el caso de mucho software cientifico (por ejemplo, amber), también puede ser el caso de la instalación de una versión especifica de una biblioteca / software no disponible en los repositorios. Esto implica compilar código c, c++ o fortran (lenguajes de programación) a archivos que puedan ser ejecutados en Linux. 

Lo primero que debemos hacer es instalar las herramientas de desarrollo de linux mediante:

```
apt install build-essential linux-headers-$(uname -r)
```

Por lo general, el paquete puede descargarse desde la página web del desarrollador o desde paginas como github o gitlab. Es muy común que los mismos se descarguen como un archivo compactado (.tar varios ficheros y directorios en un sólo archivo) y comprimido (gzip, bzip2 o xz), por lo que es necesario extraerlo.

```
tar -xzf fichero.tar.gz    # Extrae .tar.gz
tar -xjf fichero.tar.bz2   # Extrae .tar.bz2
tar -xJf fichero.tar.xz    # Extrae
```

Antes de proceder a la instalación es importante leer la documentación del paquete o los archivos README / INSTALL para ver si hay información sobre posibles dependencias, en caso afirmativo deberiamos instalarlas. También puede haber información sobre opciones de configuración, etc.

Para  instalar posibles dependencias:
```
apt install dependencia1 dependencia2 ...
```
La secuencia tipica de comandos a ejecutar es la siguiente:

```
cd dir_descomp
./confugure 		# ./ le indica al sistema que se ejecuta el comando configure desde la ubicación actual.
make
sudo make install 	# Se debe ejecutar como root.
```
El primer comando ejecuta un script de configuración que analiza las bibliotecas instaladas en el sistema. Si una biblioteca requerida no está instalada, se genera un aviso y es necesario instalarla. Luego de la ejecución del comando configure va a crearse un archivo "Makefile" en el directorio. 

Al ejecutar make, este comando lee el archivo Makefile y corre los compiladores (gcc para c, g++ para C++ o gfortran para fortran) para compilar el programa.

El tercer comando no es estrictamente necesario pero es recomendado para acceder a los ejecutables desde cualquier lugar. Realiza la instalación del programa en el sistema.

Ejecutar el comando `./configure --help` puede mostrarnos más información acerca de las opciones de configuración. Estas opciones pueden ser muy especificas del proyecto, pero hay una que es importante conocer: `--prefix`. Esta opción nos permite indicar donde queremos instalar el paquete. Por defecto, los programas serán instalados en /usr/local, junto con el resto de los programas que se instalen, esto puede ser complejo al momento de querer desinstalar un programa instalado de forma manual. Es por esto que se considera una buena alternativa instalar estos paquetes en el directorio /opt, de forma que el paquete mantiene todos los ficheros dentro de un directorio `/opt/paquete/...`, siendo facilmente identificables al momento de querer desinstalar.

Ejemplo: [Instalación OpenMPI](../mpi.md)


# Bibliotecas compartidas

Casi todo el software comparte funcionalidades como acceder al disco, mostrar botones, usar formularios, etc. En lugar de que todos incluyan el código de las mismas operaciones, utilizan archivos que las ponen a disposición del sistema. Se llaman bibliotecas compartidas y son partes de código compilado y reutilizable como funciones o clases, que varios programas utilizan de manera recurrente.

Para construir un archivo ejecutable a partir del código fuente de un programa, son necesarios dos pasos importantes. Primero, el compilador convierte el código fuente en código máquina (sistema de códigos directamente interpretable por el microprocesador) que se almacena en los llamados object files. En segundo lugar, el linker o enlazador combina los archivos de objetos y los vincula a las bibliotecas para generar el archivo ejecutable final. Este enlace puede hacerse estáticamente (statically) o dinámicamente (dynamically). Dependiendo del método que utilicemos, hablaremos de bibliotecas estáticas o, en caso de vinculación dinámica, de bibliotecas compartidas. Expliquemos sus diferencias.

**Bibliotecas estáticas**

Una biblioteca estática se fusiona con el programa en el momento del enlace. Una copia del código de la biblioteca se incrusta en el programa y se convierte en parte de él. Por lo tanto, el programa no tiene dependencias de la biblioteca en tiempo de ejecución porque ya contiene el código de la biblioteca. No tener dependencias puede verse como una ventaja, ya que no tiene que preocuparse por asegurarse de que las bibliotecas utilizadas siempre estén disponibles. En el lado negativo, los programas vinculados estáticamente son más pesados.

**Bibliotecas compartidas (o dinámicas)**

En el caso de las bibliotecas compartidas, el enlazador simplemente se encarga de que el programa haga referencia a las bibliotecas correctamente. Sin embargo, el enlazador no copia ningún código de biblioteca en el archivo del programa. En tiempo de ejecución, la biblioteca compartida debe estar disponible para satisfacer las dependencias del programa. Este es un enfoque económico para administrar los recursos del sistema, ya que ayuda a reducir el tamaño de los archivos de programa y solo se carga una copia de la biblioteca en la memoria, incluso cuando es utilizada por varios programas.

## Convenciones de nomenclatura de archivos de objetos compartidos

El nombre de una biblioteca compartida sigue un patrón que se compone de tres elementos:

- Nombre de la biblioteca (normalmente precedido por lib)
- so (que significa “objeto compartido”)
- Número de versión de la biblioteca

Por ejemplo: libpthread.so.0

Por el contrario, los nombres de las bibliotecas estáticas terminan en .a, p. ej. libpthread.a.

```bash
ls -l $AMBERHOME/lib
total 51764
-rw-rw-r-- 1 pale pale   237564 sep 27  2018 libarpack.a
-rw-rw-r-- 1 pale pale   304876 sep 27  2018 libblas.a
-rw-rw-r-- 1 pale pale   132652 sep 27  2018 libcifparse.a
-rwxrwxr-x 1 pale pale  8540656 sep 27  2018 libcpptraj.so
-rw-rw-r-- 1 pale pale   334268 sep 27  2018 libemil.a
-rw-r--r-- 1 pale pale  3358584 sep 27  2018 libfftw3.a
-rwxr-xr-x 1 pale pale      965 sep 27  2018 libfftw3.la
lrwxrwxrwx 1 pale pale       17 sep 27  2018 libfftw3.so -> libfftw3.so.3.3.0
lrwxrwxrwx 1 pale pale       17 sep 27  2018 libfftw3.so.3 -> libfftw3.so.3.3.0
-rwxr-xr-x 1 pale pale  1771208 sep 27  2018 libfftw3.so.3.3.0
-rw-rw-r-- 1 pale pale  1899778 sep 27  2018 libFpbsa.a
-rw-rw-r-- 1 pale pale  1472728 sep 27  2018 liblapack.a
-rwxrwxr-x 1 pale pale  2261264 sep 27  2018 libmdgx.so
-rw-rw-r-- 1 pale pale   920516 sep 27  2018 libnab.a
-rw-r--r-- 1 pale pale  1191508 sep 27  2018 libnetcdf.a
```

glibc (biblioteca GNU C) es un buen ejemplo de una biblioteca compartida. Su archivo se llama libc.so.6. Tales nombres de archivo bastante generales son normalmente enlaces simbólicos que apuntan al archivo real que contiene una biblioteca, cuyo nombre contiene el número de versión exacto. En el caso de glibc, este enlace simbólico se ve así:

```
ls -l /lib/x86_64-linux-gnu/libc.so.6
lrwxrwxrwx 1 root root 12 dic 16  2020 /lib/x86_64-linux-gnu/libc.so.6 -> libc-2.31.so
```

Las ubicaciones comunes para las bibliotecas compartidas en un sistema Linux son:

- `/lib`
- `/lib32`
- `/lib64`
- `/usr/lib`
- `/usr/local/lib`
- Los directorios guardados en la variable LD_LIBRARY_PATH. Se suelen usar para cambios temporales o librerías muy específicas. Tienen mayor preferencia que el resto.

## Configuración de rutas de bibliotecas compartidas

Las referencias contenidas en los programas vinculados dinámicamente se resuelven mediante el vinculador dinámico (ld.so o ld-linux.so) cuando se ejecuta el programa. El vinculador dinámico busca bibliotecas en varios directorios. Estos directorios están especificados por la ruta de la biblioteca. La ruta de la biblioteca se configura en el archivo /etc/ld.so.conf y, más común hoy en día, en archivos que residen en el directorio /etc/ld.so.conf.d. Normalmente, el primero incluye una sola línea include para los archivos \*.conf en el segundo:

```
cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

El directorio /etc/ld.so.conf.d contiene archivos \*.conf:

```
libc.conf  x86_64-linux-gnu.conf
```

Estos archivos \*.conf deben incluir las rutas absolutas a los directorios de las bibliotecas compartidas:

```
cat /etc/ld.so.conf.d/x86_64-linux-gnu.conf
# Multiarch support
/usr/local/lib/x86_64-linux-gnu
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
```
El comando ldconfig se encarga de leer estos archivos de configuración, creando el conjunto de enlaces simbólicos antes mencionados que ayudan a localizar las bibliotecas individuales y finalmente a actualizar el archivo de caché /etc/ld.so.cache. Por lo tanto, ldconfig debe ejecutarse cada vez que se agregan o actualizan archivos de configuración.

Las opciones útiles para ldconfig son:

- -v, --verbose: Muestra los números de versión de la biblioteca, el nombre de cada directorio y los enlaces que se crean:

```
sudo ldconfig -v
/usr/local/lib:
/lib/x86_64-linux-gnu:
    libnss_myhostname.so.2 -> libnss_myhostname.so.2
	libfuse.so.2 -> libfuse.so.2.9.7
	libidn.so.11 -> libidn.so.11.6.16
	libnss_mdns4.so.2 -> libnss_mdns4.so.2
	libparted.so.2 -> libparted.so.2.0.1
	(...)
```

Además de los archivos de configuración descritos anteriormente, la variable de entorno LD_LIBRARY_PATH se puede usar para agregar nuevas rutas para bibliotecas compartidas temporalmente. Está formado por un conjunto de directorios separados por dos puntos (:) donde se buscan las bibliotecas. Para agregar, por ejemplo, /usr/local/mylib a la ruta de la biblioteca en la sesión de shell actual, puede teclear:

```
echo $LD_LIBRARY_PATH 
/home/pale/programas/amber18/lib
```

## Buscando las dependencias de un ejecutable particular

El comando ldd nos muestra las librerías que necesita un programa del sistema.

Los ficheros de las librerías indican la versión y tienen extensión .so (shared object). Es frecuente que se creen enlaces entre versiones compatibles, por ejemplo si un programa necesita la version 6 y está instalada la versión 6.2 y ambas son compatibles, se puede hacer un enlace a la versión 6.2.

```
ldd $AMBERHOME/bin/cpptraj 
	linux-vdso.so.1 (0x00007fffb5d91000)
	libgfortran.so.4 => not found
	libbz2.so.1.0 => /lib/x86_64-linux-gnu/libbz2.so.1.0 (0x00007f9f71717000)
	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f9f716fb000)
	libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f9f71519000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f9f713ca000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f9f713af000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9f711bb000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f9f72082000)

```
# Gestión de usuarios:

La gestión de usuarios y grupos en Linux es una tarea fundamental de cualquier administrador de sistemas. Es importante conocer cómo crear, modificar y eliminar cuentas de usuarios y grupos así como establecer permisos para que dichos grupos y usuarios tengan acceso a archivos y directorios según lo necesiten.

En linux tenemos 2 tipos de usuarios:
- El usuario administrador (root)
- Usuarios del sistema que no tienen tantos privilegios.

Siempre podemos saber que usuario es el que está ejecutando los comandos ya que está escrito en el "prompt" a la izquierda del arroba.

Para acceder a los permisos de administrador en sistemas como Ubuntu podemos usar la orden "sudo" (super user do) previa al comando que queremos ejecutar como administrador. En otros sistemas podemos usar el comando su, que sirve para cambiar de usuario. Con este comando podemos también cambiar de usuario ingresando la contraseña de acceso del mismo. Si se ejecuta "su" solo el sistema interpreta que se está queriendo acceder al usuario root.

```
# Ubuntu u otros sistemas que tengas habilitado sudo
sudo cat /etc/shadow
# Login como root
su
Contraseña:
root@...# cat /etc/shadow
# Login como otro usuario
su user1
Contraseña:
user1@...$ 
```

Una aclaración respecto al comando sudo, es que sólo lo pueden usar aquellos usuarios que pertenecen al grupo sudo.

En Linux, los grupos constituyen el primer método de control de acceso a los recursos del sistema. Cuando se crea una cuenta de usuario, también se crea un grupo con el mismo nombre (conocido como grupo primario para dicha cuenta) y ambos son vinculados. Para permitir una administración flexible de los permisos de los usuarios, Linux permite estructurar los usuarios a través de grupos y los permisos pueden ser asignados a un grupo. Por ejemplo, en una institución podemos tener un grupo especifico de usuarios (el grupo de investigadores, por ejemplo) que tiene acceso a ciertos archivos, al momento de añadir un nuevo investigador al sistema, solo tenemos que asignarle a su cuenta de usuario el grupo investigador.

Cuando listamos el contenido de un directorio con `ls -l` podemos ver que hay una columna que nos indica cual es el usuario propietario de ese objeto (fichero o directorio) y en la columna siguiente vemos al grupo propietario (por lo general es el grupo principal del usuario que lo creó)

```
ls -l

total 120
-rw-rw-r-- 1 pale pale 59196 oct 29 17:51 clase1.md
-rw-rw-r-- 1 pale pale 42450 nov  8 21:09 clase2.md
-rw-rw-r-- 1 pale pale   136 oct 29 17:29 clase3.md
-rw-rw-r-- 1 pale pale    94 oct 29 17:29 clase4.md
-rw-rw-r-- 1 pale pale   133 oct 28 16:53 home.md
drwxrwxr-x 2 pale pale  4096 oct 28 17:07 imagenes
```

Los datos de los usuarios se encuentran en 2 archivos:
- /etc/passwd: es una base de datos simple contiene información de todas las cuentas de usuario en el sistema. Es propiedad de root. El archivo solo puede ser editado por usuarios root o usuarios con privilegios de sudo y todos los usuarios del sistema pueden leerlo pero no modificarlo.
- /etc/shadow: contiene la contraseña encriptada con SHA512 y más datos seguros del usuario

Estructura archivo /etc/passwd
```
pale:x:1000:1000:Pablo,,,:/home/pale:/bin/zsh
[---] - [--] [--] [-----] [--------] [--------]
 |    |  |    |      |          |       |
 |    |  |    |      |          |       +-> 7. Login shell
 |    |  |    |      |          +---------> 6. Home directory
 |    |  |    |      +--------------------> 5. GECOS
 |    |  |    +---------------------------> 4. GID
 |    |  +--------------------------------> 3. UID
 |    +-----------------------------------> 2. Password
 +----------------------------------------> 1. Username
```

- 1. Nombre de usuario. La cadena introducida al iniciar sesión en el sistema. Cada nombre de usuario debe ser una cadena única en la máquina. La longitud máxima del nombre de usuario está limitada a 32 caracteres.
- 2. Contraseña. En sistemas Linux más antiguos, la contraseña cifrada del usuario se almacenaba en el /etc/passwd. En la mayoría de los sistemas modernos, este campo se establece en x y la contraseña del usuario se almacena en el /etc/shadow.
- 3. UID. El identificador de usuario es un número asignado a cada usuario. Es utilizado por el sistema operativo para referirse a un usuario.
- 4. GID. El número de identificación de grupo del usuario, que se refiere al grupo principal del usuario. Cuando un usuario crea un archivo, el grupo del archivo se establece en este grupo. Normalmente, el nombre del grupo es el mismo que el del usuario. Los subgrupos de usuarios se enumeran en /etc/groups.
- 5. GECOS o el nombre completo del usuario. Este campo contiene una lista de valores separados por comas con la siguiente información:
	- Nombre de usuario completo o nombre de la aplicación.
	- Número de habitación.
	- Número de teléfono del trabajo.
	- Número de teléfono fijo.
	- Otra información de contacto.
- 6. Directorio home. La ruta absoluta al directorio de inicio del usuario. Contiene archivos de usuario y configuraciones. De forma predeterminada, los directorios de inicio de los usuarios reciben el nombre del usuario y se crean en el directorio /home
- 7. Shell de inicio de sesión. La ruta absoluta al shell de inicio de sesión del usuario. Este es el shell que se inicia cuando el usuario inicia sesión en el sistema. En la mayoría de las distribuciones de Linux, el shell de inicio de sesión predeterminado es Bash.

Estructura del archivo /etc/shadow

```
bin:*:17110:0:99999:7:::
daemon:*:17110:0:99999:7:::
linoadmin:!!:17289:0:99999:7:::
pale:$6$tDH3qFb42aUdtg5E$S3obuA1wCZj8e4MQjzjv8BLPBl5AF./.fhIu4R.ZAuf9jS5Gnr6FySw7kvq/N84uZappXI12wV9rG6h7fdiPG.:18928:0:99999:7:::
[--] [--------------------------------------------------------------------------------------------------------] [---] - [---] ----
  |                                  |                                                                            |   |   |   ||||
  +-> 1. Usuario                     +-> 2. Contraseña                                                            3   4   5   6789
```

- 1. Nombre de usuario: Este primer campo denota el nombre de usuario que debe usarse al iniciar sesión en el sistema.
- 2. Contraseña: El segundo campo almacena la contraseña en formato cifrado. la $ xx $ inicial ($ 6 $ para el ejemplo anterior) justo después del primer campo (raíz:) indica el tipo de cifrado. El asterisco * significa que esta cuenta no se puede utilizar para iniciar sesión y el !! significa que el usuario no tiene contraseña, por lo que ha sido creado sin contraseña.
- 3. Fecha del último cambio de contraseña: Fecha del último cambio de contraseña, expresada como el número de días desde el 1 de enero de 1970. El valor 0 significa que el usuario debe cambiar su contraseña la próxima vez que inicie sesión en el sistema.
- 4. Días mínimos: Número mínimo de días después de los cuales un usuario puede cambiar su contraseña. No podrá cambiar la contraseña antes de eso.
- 5. Días máximos: Indica el número máximo de días que la contraseña es válida. Después de eso, el usuario está obligado a cambiar su contraseña.
- 6. Período de advertencia de la contraseña: Número de días antes de los cuales el usuario recibirá una notificación de advertencia sobre la caducidad de la contraseña y debe cambiarse.
- 7. Periodo de inactividad: Indica el número de días después de la expiración de la contraseña después de los cuales se deshabilitará la cuenta. Cuando está vacío, este campo indica que no hay cumplimiento de un período de inactividad.
- 8. Fecha de caducidad: Indica los días desde el 1 de enero de 1970 en que la cuenta está deshabilitada.
- 9. Reservado: el noveno campo está reservado para uso futuro.

Como este archivo almacena las contraseñas de los usuarios, su acceso y modificación están restringidos solo al usuario root:

```
-rw-r----- 1 root shadow 1953 may 27 12:16 /etc/shadow
```

Los métodos de encriptación con los que se puede cifrar la contraseña son:
- $1$: MD5
- $2a$: Blowfish
- $5$: SHA-256
- $6$: SHA-512

El algoritmo utilizado para cifrar el campo de contraseña se conoce técnicamente como función hash unidireccional. Este algoritmo es fácil de calcular en una dirección, pero muy difícil de calcular en la dirección inversa.

Cuando se genera la contraseña de usuario, se codifica con un valor generado aleatoriamente llamado sal (salt). Esto significa que cualquier contraseña en particular podría almacenarse de diferentes formas. Luego, el valor de sal se almacena con la contraseña cifrada. `$id$salt$encrypted`

Cuando un usuario inicia sesión y proporciona una contraseña, la sal se recupera primero de la contraseña cifrada almacenada. Luego, la contraseña proporcionada se codifica con el valor de sal y luego se compara con la contraseña encriptada. Si las contraseñas coinciden, el usuario está autenticado.

Siempre se recomienda seleccionar contraseñas que sean fuertes: contengan una combinación de alfabetos, dígitos y símbolos especiales y evite seleccionar palabras del diccionario.

## Crear, modificar y eliminar usuarios

Cuando se agrega un usuario, el sistema operativo agrega una linea al archivo /etc/passwd y al archivo /etc/group (se añade un grupo para cada usuario). También se crea un archivo de correos en /var/spool/mail donde el usuario recibirá notificaciones del sistema y otros usuarios. Ademas, se genera su directorio personal, el lugar donde el usuario podrá guardar sus archivos personales. Fuera de este lugar el usuario no podrá escribir (excepto en /tmp y /var/tmp). 

Para crear un usuario se pueden utilizar 2 comandos:
- adduser
- useradd

useradd es un comando que ejecuta un binario del sistema, mientras que adduser es un script en perl que utiliza el binario useradd.

adduser y addgroup ofrecen una interfaz más sencilla que los programas de bajo nivel useradd, groupadd y usermod. 

La mayor ventaja del comando adduser es que crea el directorio home (/home/usuario/) del usuario de manera automática, cosa que no hace useradd (hay que usar la opción -m). 

**Ejemplo adduser**

```
adduser user1

Adding user `user1' ...
Adding new group `user1' (1002) ...
Adding new user `user1' (1002) with group `user1' ...
Creating home directory `home/user1` ...
Copying files from `/etc/skel` ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for user1
Enter the new value, or press ENTER for the default
	Full Name []:
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n]
```

Si buscamos al usuario en /etc/passwd vemos:

```
user1:x:1002:1002:,,,:/home/user1:/bin/bash
```

Con solo el comando y el nombre del usuario se genera el mismo junto con la configuración del home y el shell por defecto que usará el usuario.

Opciones:
- --conf FICHERO: Usa FICHERO en vez de la configuración en /etc/adduser.conf
- --disabled-login: No ejecuta passwd para establecer la clave. El usuario no podrá usar la cuenta hasta que se establezca una clave.
- --disabled-password: Como «--disabled-login», pero todavía es posible usar la cuenta, por ejemplo mediante claves SSH RSA, pero  no usando autenticación de claves.
- --home DIRECTORIO: Usa DIRECTORIO para el directorio personal, en vez del predeterminado en el fichero de configuración. 
- --shell CONSOLA: Usa CONSOLA como la consola de entrada del usuario, en vez de la predeterminada.
- --no-create-home
- --ingroup GRUPO

**Ejemplo useradd**

Primero vamos a ver que pasa cuando hacemos lo mismo que con adduser:

```
useradd user2
```
Chequeamos el archivo /etc/passwd:

```
grep user2 /etc/passwd

user2:x:1003:1003::/home/user2:bin/sh
```
Si intentamos loguearnos como user2, vamos a ver que no se puede ya que no tiene contraseña definida:

```
sudo grep user2 /etc/shadow

user2:!:18939:0:99999:7:::
```
La contraseña se debe generar con el comando `passwd user2` y luego ingresando la contraseña 2 veces.

Otro problema es que no existe el directorio home: /home/user2, ya que primero debemos crearlo (también puede ser después) y cambiar los permisos del mismo (ver más adelante):

```
mkdir /home/user2
usermod -s /bin/bash -d /home/user2 -c "Usuario 2" # Cambiamos shell, asignamos home y agregamos comentarios (GECOS)
chown user2.user2 /home/user2 # Cambiamos permisos del directorio home de user2 (lo vemos después)
```

Ahora:

```
grep user2 /etc/passwd

user2:x:1003:1003:Usuario 2:/home/user2:/bin/bash

grep user2 /etc/shadow

user2:$6$5sv2BNfw80Z0yUX7$XJc.AkzWQ/1iaKrZJPp2mUXEOTJ6qnPAbyAI9E0SbJuwLX8axnojRVt9/YwCa4SMiu7lWkMDSnvocgsN9X/2v1:18939:0:99999:7:::
```

También se podría haber realizado todo esto de manera más prolija:

```
# Crear user3 con useradd
sudo useradd -m -d /home/user3 -s /bin/bash -c "Usuario 3" user3

sudo passwd user3

New password: 
Retype new password: 
passwd: password updated successfully

```

Cómo se puede ver useradd es más complejo de manejar aunque más manipulable.

Opciones:
- -d: directorio home
- -m: crea el directorio home
- -g: grupo principal
- -G: grupo/s secundario/s
- -s: interprete de comandos
- -k: directorio de plantilla

Para ver mas opciones de useradd: `man useradd`

Una vez que hemos creado la cuenta de usuario, el comando chage con su opción -l nos permitirá ver información detallada sobre el período de validez de la misma (por defecto, una cuenta de usuario nunca expira) y de la contraseña.

```
Last password change								: nov 06, 2021
Password expires									: never
Password inactive									: never
Account expires										: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
```

Podemos editar la cuenta de user1 de tal manera que la cuenta expire (-E) el 31 de diciembre de 2021, y mientras tanto se le permita cambiarla solamente (-m) una vez por semana (7 días). Cada contraseña será válida como máximo (-M) durante 60 días, y 3 días antes se le va a inviar un recordatorio para que la modifique (-W). Finalmente, 5 días después de la fecha de vencimiento de la contraseña se procederá a bloquear (-I) la cuenta:

```
chage -E 2021-12-31 -m 7 -M 60 -W 3 -I 5 user1
```

Podemos forzar a que user1 cambie su contraseña la próxima vez que inicie sesión:

```
chage -d 0 user1
```

Otra forma de visualizar información de una cuenta dada consiste en utilizar el comando finger seguido por el nombre de usuario en cuestión.

Si lo que queremos es borrar la cuenta del usuario user3, junto con su directorio home (en el caso de que nos interese eliminar la cuenta, pero conservando dichos recursos deberemos omitir la opción -r):

```
# Elimina solo el usuario
deluser user3
userdel user3
# Elimina el home y otros datos (cola de correo del usuario)
deluser --remove-home user3
userdel -r user3
```

usermod: modifica un usuario

## Grupos

Para crear un nuevo grupo adicional llamado *grupo2* se puede utilizar los comandos groupadd o addgroup. A continuación, para agregar algún usuario al grupo podemos usar el comando usermod con sus opciones -a (de append) y -G (group) seguidos por el nombre del grupo y el usuario que deseamos agregar al mismo:

```
groupadd grupo2 # También: addgroup grupo2
usermod -a -G grupo2 user2 # Se puede abreviar las opciones con -aG
```

Para ver la lista de grupos a los que pertenece un determinado usuario podemos utilizar los comandos groups o id, seguido por el nombre de usuario:

```
id user2
groups user2
```

Si un usuario quiere cambiar el grupo pimario por uno de sus grupos secundarios puede usar el comando newgrp seguido del grupo secundario que quiere asignar como primario.

Para eliminar una cuenta de usuario de un grupo suplementario tenemos dos alternativas:

- Utilizar `usermod -G` seguido de los grupos en los que queremos que el usuario permanezca activo, y finalmente del nombre del usuario. Esta opción puede tornarse un tanto engorrosa si el usuario pertenece a muchos grupos suplementarios.
- Emplear `gpasswd -d` seguido del nombre de usuario y del grupo del que se desea eliminarlo.

```
usermod -G user2 user2 
gpasswd -d user2 grupo2
```

Para borrar un grupo dado (grupo2 en el siguiente ejemplo), utilizaremos:

```
groupdel grupo2
```


## Permisos en Linux

Podemos dividir la política de permisos de directorios y ficheros en 3 grandes categorías:
- Permisos aplicados al usuario dueño (Owner)
- Permisos aplicados al grupo (Group)
- Permisos aplicados a los otros (Others) usuarios del sistema que no están contemplados en las opciones anteriores.

En cada una de estas 3 categorías se pueden aplicar también 3 tipos de permisos: **read** (lectura), **write** (escritura), y **execute** (ejecución):
- r: El usuario tiene permisos de lectura. Podrá ejecutar comandos de lectura como ls, cat, 
- w: El usuario tiene permisos de escritura. Esto le permite editar archivos de texto o agregar / eliminar archivos en un directorio
- x: El usuario tiene permisos de ejecución, es decir podrá ejecutar scripts. Aclaración: los directorios deben tener permisos de ejecución

Como vimos antes, al hacer ls -l sobre un directorio vemos algo similar a esto:
```
-rw-rw-r-- 1 pale pale 59196 oct 29 17:51 clase1.md
-rw-rw-r-- 1 pale pale 44131 nov  9 06:47 clase2.md
-rw-rw-r-- 1 pale pale   136 oct 29 17:29 clase3.md
-rw-rw-r-- 1 pale pale    94 oct 29 17:29 clase4.md
-rw-rw-r-- 1 pale pale   133 oct 28 16:53 home.md
drwxrwxr-x 2 pale pale  4096 oct 28 17:07 imagenes
-[-][-][-] - [--] [--]  [--] [----------] 
| |  |  |  |   |    |     |        | 
| |  |  |  |   |    |     |        +-> Fecha de creación
| |  |  |  |   |    |     +----------> Tamaño objeto
| |  |  |  |   |    +----------------> Grupo
| |  |  |  |   +---------------------> Usuario
| |  |  |  +-------------------------> Numero de enlaces duros
| |  |  +----------------------------> Permisos otros
| |  +-------------------------------> Permisos grupo
| +----------------------------------> Permisos usuario
+------------------------------------> Tipo
```

Vamos a crear un directorio llamado **directorio** en /tmp (este es un directorio donde se puede almacenar información temporal, una vez que se reinicia el equipo se pierde)

```
mkdir /tmp/directorio
ls -ld /tmp/directorio

drwxrwxr-x 2 pale pale 4096 nov  9 20:21 /tmp/directorio
```
Si queremos cambiar de usuario dueño o el grupo propietario podemos utilizar el comando chown (change owner). Para cambiar el grupo propietario también podemos usar chgrp

```
chown user1: /tmp/directorio # Cambia el usuario
chown :user1 /tmp/directorio # Cambia el grupo
# Con chgrp
chgrp user1 /tmp/directorio
chown user1:user1 /tmp/directorio # Cambia ambos
```

Para ambos comandos podemos usar la opción -r que hace que la operación se aplique de forma recursiva a todo el contenido del directorio.

Para modificar los permisos de un elemento podemos usar la orden chmod. Tenemos 3 formas de hacerlo:
- + y -: es el más concreto ya que sólo afecta al permiso que indicamos. Ejemplos:
	- `chmod +x fichero`: habilitamos permisos de ejecución a todos los grupos (`-rwxrwxr-x 1 pale pale 0 nov  9 20:38 fichero`). También puede usarse la opción a+x
	- `chmod u+x fichero`: habilitamos permisos de ejecución solo al usuario propietario (`-rwxrw-r-- 1 pale pale 0 nov  9 20:38 fichero`)
	- `chmod g+x fichero`: habilitamos permisos de ejecución solo al grupo propietario (`-rw-rwxr-- 1 pale pale 0 nov  9 20:38 fichero`)
	- `chmod o+x fichero`: habilitamos permisos de ejecución solo al resto (`-rw-rw-r-x 1 pale pale 0 nov  9 20:38 fichero`)
	- `chmod g+x fichero`: habilitamos permisos de ejecución solo al grupo propietario (`-rw-rwxr-- 1 pale pale 0 nov  9 20:38 fichero`)
- =: cambia un grupo entero de permisos.
	- `chmod u=rw,g=r,o=x fichero`: El usuario sólo tiene permisos de escritura y lectura, el grupo sólo lectura y el resto sólo ejecución. `-rw-r----x 1 pale pale 0 nov  9 20:38 fichero` 
- Usando numeros (cambia todos los permisos)

Para usar números consideramos cada posición de los permisos como un bit que pondremos a 1 para concederlo o a 0 para denegarlo. Los agrupamos en conjunto de 3 para representar los del usuario, grupo u otros y los pasamos de binario a decimal .

| R | W | X | R | W | X | R | W | X |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |

Para cada nivel (usuario, grupo, otros):

- `100: 4:      r--`
- `010: 2:      -w-`
- `001: 1:      --x`
- `011: 2+1:3   -wx`
- `101: 4+1:5   r-x`
- `110: 4+2:6   rw-`
- `110: 4+2+1:7 rwx`

De está forma si queremos poner permisos al archivo de forma que el usuario pueda leer y escribir, el grupo sólo leer y el resto nada: rw- r-- --- en binario 110 100 000 y en decima 6 4 0. El comando sería: `chmod 640 fichero`

Con la opción -R podemos modificar los permisos de forma recursiva. 

### Permisos especiales:
- SetUID: El programa que lo tenga activado se ejecutará con los permisos del usuario propietario del archivo y no con los permisos de quién invoca al programa. Se representa por la letra s como permiso de ejecución. `rws`. Para administrarlos se usan las letras: `chmod u+s tux.sh` o añadiendo una cifra a la izquierda usando números: `chmod 4777 /home/tmp/`
- SetGID: Igual que setUID pero con los permisos del grupo. En caso de ser un directorio los elementos creados pertenecerán al grupo del directorio y no al grupo del usuario que crea el elemento. Letras: `chmod g+s tux.sh`. Números: `chmod 2777 /home/tmp/`

```
# UID
ls -l /usr/bin/passwd 
-rwsr-xr-x 1 root root 68208 jul 14 19:08 /usr/bin/passwd
# GID
mkdir gid
chmod g+s gid
ls -ld gid/
drwxrwsr-x 2 pale pale 4096 dic  8 17:46 gid/
```

- Sticky Bit: En el directorio que lo tenga activado, los ficheros que contenga sólo podrán ser borrados por sus propietarios o por el propietario del directorio. Se representa por t en el permiso de ejecución de "los otros"

```
# Sticky bit
# Creamos un directorio con todos los permisos para todos los usuarios
mkdir sticky 
chmod 777 sticky
# Creamos un archivo en sticky
touch sticky/file.txt
# Cambiamos de usuario
su user1
# Con user1 intentamos eliminar file.txt en sticky
rm sticky/file.txt
rm: remove write-protected regular empty file 'sticky/file.txt'? y
# Comprobamos que el fichero se eliminó
ls sticky

# Repetimos, pero antes asignamos sticky bit al directorio sticky con el usuario dueño
chmod +t sticky
ls -ld sticky/
drwxrwxrwt 2 pale pale 4096 dic  8 17:58 sticky/
touch sticky/file.txt
su user1
rm sticky/file.txt
rm: remove write-protected regular empty file 'sticky/file.txt'? s
rm: cannot remove 'sticky/file.txt': Operation not permitted
ls sticky/
file.txt
# Luego probamos eliminar con el usuario dueño
rm sticky/file.txt
ls sticky

```

