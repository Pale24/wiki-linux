# Inicio del sistema y proceso de login.

## Secuencia de arranque.

Al encender el equipo, el BIOS (Basic Input-Output System) o UEFI (Unified Extensible Firmware Interface) llevan a cabo una revisión del hardware conocida comunmente como POST (Power-On Self Test). A continuación, se busca un gestor de arranque (por lo general, GRUB) en el MBR (Master Boot Record) o en la partición EFI de un dispositivo de almacenamiento.

A partir de ese momento, el control del proceso se pasa a manos del GRUB, que se encargará de cargar el kernel. Este reconocerá y configurará lso dispositivos de hardware presentes en el equipo y los preparará para su uso. En el próximo paso, el núcleo ejecutará su primer proceso, conocido como **init**. Finalmente, este último utilizará el gestor del sistema ( **systemd o Upstart** ) o los scripts de  **SysVinit** para comtinuar el inicio de los demás servicios. El proceso de arranque termina al presentar una interfaz de inicio de sesión de modo texto o gráfico, según el caso.





# Inicio de SysV Runlevels
- Varios modelos
- Cambios de modos
- Configuración por defecto
## Modos
| Nro | Debian/Ubuntu                    | CentOS/Suse        |
|-----|----------------------------------|--------------------|
| 0   | Halt                             | Halt               |
| 1   |	Single User mode                 | Single User Mode   |
| 2   |	Full, multiuser, GUI if installed| Multi user, sin red|
| 3   | Nada                             | Multi user, con red|
| 4   | Nada                             | Nada 			  |
| 5   | Nada                             | Multi user GUI     |
| 6   | Reboot                           | Reboot             |

## Cambios de modos y configuración por defecto
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

# Inicio con SystemD
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

# Manejo de servicio con SysV
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


# Manejo de servicios con systemD
- systemctl
	- enable / disable
	- start / stop / restart / status

A diferencia de SysV, los achivos de configuración de systemD no se encuentran en un solo lugar. esto hace muy engorrosa la administración de servicios.

*Manejo de servicios*
```
systemctl status sshd
systemctl start <servicio>
systemctl stop <servicio>
systemctl enable <servicio>
systemctl disable <servicio>
```

