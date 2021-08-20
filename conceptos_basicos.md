# Conceptos básicos

Acá dejo algunos conceptos de los que no voy a hablar ya que esta información es facilmente accesible en internet, pero que son necesarios para entender como usar "linux"

- ¿Que es GNU/linux?
- Distribuciones GNU/Linux y sus diferencias
- Versiones 

## Filesystem Hierarchy Standard

En Linux todos los directorios se ubican en forma de árbol invertido con un origen común. Este primer directorio se denomina raíz o root (/) y contiene al resto de los directorios y archivos de manera organizada. 

Esta distribución de los directorios está estandarizada en un documento denominado **Filesystem Hierarchy Standard**. Donde se detalla el proposito de los directorios en un sistema Linux.

![filesystem](https://thesagediary.files.wordpress.com/2018/09/linuxfile.png)

En el siguiente nivel hacia abajo podemos encontrar directorios como:
- /bin/: contiene los archivos ejecutables correspondientes a los comandos que todos los usuarios del sistema pueden utilizar
- /boot/: Contiene archivos necesarios para el arranque del sistema (incluyendo el propio kernel).
- /dev/: Aparecen dispositivos presentes en el sistema. Por ejemplo:
	- Representaciones de discos: 
		- /dev/sda: Primer disco 
		- /dev/sdb: Segundo disco
		- /dev/sdc: Tercer disco
	- Representaciones de particiones:
		- /dev/sda1: primer partición del primer disco
		- /dev/sda2: segunda partición del primer disco
- /home/: Es el directorio donde se alojan los directorios personales de los usuarios del sistema 
- /root/: Es el directorio personal del usuario root o superusuario (es el usuario con privilegios ilimitados)
- /sbin/: Ejecutables correspondientes a utilidades del sistema.
- /usr/: Contiene comandos accesibles para los usuarios, aunque es de solo lectura. Es decir, son archivos que los usuarios sin privilegios no pueden modificar
- /var/: Contiene subdirectorios y archivos variables, sujetos a cambios permanentes (registros de logs del sistema o del funcionamiento de procesos)
- /tmp/: Es una ubicación especial en la que los distintos programas pueden escribir y leer archivos temporales. Es posible que el contenido de este directorio se borre al reiniciar el sistema.

