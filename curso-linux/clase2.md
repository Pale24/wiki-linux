# Clase 2

## Temas:
- 5. Bibliotecas compartidas
- 6. Gestión de paquetes
- 7. Gestión de usuarios
    - crear, eliminar y permisos usuarios


# Bibliotecas compartidas

Casi todo el software comparte funcionalidades como acceder al disco, mostrar botones, usar formularios, etc. En lugar de que todos incluyan el código de las mismas operaciones, utilizan ficheros que las ponen a disposición del sistema. Se llaman bibliotecas compartidas. Son partes de código compilado y reutilizable como funciones o clases, que varios programas utilizan de manera recurrente.

Para construir un archivo ejecutable a partir del código fuente de un programa, son necesarios dos pasos importantes. Primero, el compilador convierte el código fuente en código de máquina que se almacena en los llamados object files. En segundo lugar, el linker combina los archivos de objetos y los vincula a las bibliotecas para generar el archivo ejecutable final. Este enlace puede hacerse estáticamente (statically) o dinámicamente (dynamically). Dependiendo del método que utilicemos, hablaremos de bibliotecas estáticas o, en caso de vinculación dinámica, de bibliotecas compartidas. Expliquemos sus diferencias.

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

# Gestión de paquetes
