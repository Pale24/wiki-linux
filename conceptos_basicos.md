# Conceptos básicos

Acá dejo algunos conceptos de los que no voy a hablar ya que esta información es facilmente accesible en internet, pero que son necesarios para entender como usar "linux"

- ¿Que es GNU/linux?
- Distribuciones GNU/Linux y sus diferencias
- Versiones 

## Filesystem Hierarchy Standard

El Linux todos los directorios se ubican en forma de árbol invertido con un origen común. Este primer directorio se denomina raíz o root (/) y contiene al resto de los directorios y archivos de manera organizada. 

Esta distribución de los directorios está estandarizada en un documento denominado **Filesystem Hierarchy Standard**. Donde se detalla el proposito de los directorios en un sistema Linux.

```mermaid
graph TD;
    /-->/bin/;
    /-->/opt;
    /-->/boot;
    /-->/root;
    /-->/dev;
    /-->/sbin/;
	  /-->/etc;
	  /-->/srv;
	  /-->/home;
	  /-->/tmp/;
	  /-->/lib/;
	  /-->/usr;
	  /-->/media;
	  /-->/var;
	  /-->/mnt;
	  /usr-->/bin;
	  /usr-->/include;
	  /usr-->/lib;
	  /usr-->/sbin;
	  /var-->/cache;
	  /var-->/log;
	  /var-->/spool;
	  /usr-->/tmp;
```
