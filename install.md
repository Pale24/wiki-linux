# Instalación de un sistema linux desde cero
En este apartado vamos a ver como hacer la instalación de Debian desde un USB

Debemos seguir los siguientes pasos:

1. Configurar el equipo para que arranque desde la unidad USB (puede ser con F11, F8, etc).
2. Eegir la opción de install/instalar.
3. Seleccionar idioma, país y distribución de teclad.
4. Indicar el nombre que se desea utlizar para identificar el equipo en la red.
5. Dejar el nombre de dominio vacío (por ahora)
6. Ingresar 2 veces la contraseña del usuario root. En linux, el usuario root posee privilegios como administrador. Por ese motivo, a menudo se lo llama superusuario. Entre otras tareas, utilizando esta cuenta se pueden crear, modificar y eliminar usuarios y software del sistema. Debe usarse con sumo cuidado. En Ubuntu no pide crear la cuenta de root, pero el usuario creado pertenece al grupo sudo, que tiene privilegios de superusuario al anteponer el comando sudo al comando que quiere ejecutar como administrador.
7. Crear un usuario común y asiganerle una contraseña. Esta cuenta tendrá privilegios limitados y no podrá realizar las mismas tareas que root. Además, solo tendrá acceso completo a recursos que sean de su propiedad (directorio personal y archivos ubicados dentro del mismo).
8. En método de particionado, elegir Guiado - utilizar el disco completo y configurar LVM.
9. Seleccionar el disco a utilizar.
10. En esquema de particionado, elegir separar particiones /home y /var.
11. Seleccionar "No" cuando se pregunte si se desea analizar otro CD o DVD. Esta opción solamente es útil si no disponemos de una conexion a internet.
12. Elegir una réplica o mirror para descargar paquetes durante la instalación. 
13. Ingresar la información del proxy o dejarlo en blanco. Si nuestra conexión a Internet se establece a través de otro equipo en la red que actúa como proxy, en este paso debemos indicar el nombre de dicho equipo (o IP), el puerto de escucha (por lo general es 3128/tcp), y las credenciales de autenticación.
14. Seleccionar "No" cuando se pida si deseamos participar de la encuesta de uso de paquetes.
15. Elegir los componentes que se van a instalar. Para seleccionar un item se debe posicionar el cursor sobre el mismo y presionar la barra espaciadora. En este punto es importante seleccionar ssh server para poder conectarnos de forma remota al equipo y utilidades estándar del sistema que proveerá de herramientas para operar y administrar el sistema desde la linea de comandos.
16. Instalar el gestor de arranque en el registro principal de arranque. Un gestor de arranque es un componente de software que ayuda al hardware y al firmware del equipo en la carga del sistema operativo. El gestor de arranque más utilizado en linux es GRUB (GRand Unified Bootloader).
17. Reiniciar el equipo.

