# Protocolo TCP/IP

## Modelo de capas:
Superior: cercana al usuario

- 4 Aplicación.	**L7 OSI** 	Dato
- 3 Transporte.	**L4 OSI**	Segmento
- 2 Red.		**L3 OSI**	Paquete. Direccionamiento logico, enrutamiento
- 1 Enlace. 	**L2 OSI** 	Trama. Direccionamiento fisico. 

Inferior: Hardware


# Comandos de redes con iproute2:

## Listado de interfaces de red:
```
ip address
ip addr
ip a
```

## Analizar la información de nuestra tabla de enrutamiento:
```
ip route # tambien 'ip ro' o 'ip r' 

default via 192.168.0.1 dev wlp3s0 proto dhcp metric 600
169.254.0.0/16 dev wlp3s0 scope link metric 1000
192.168.0.0/24 dev wlp3s0 proto kernel scope link src 192.168.0.6 metric 600
```

## Agregar IP estatica:
```
ip a add 10.0.1.2/24 dev enp0s3 brd +
```

## Levantar interfaz de red
```
ip li set dev enp0s3 up
```

## Agregar puerta de enlace:
```
ip r add default via 10.0.1.1/24 dev enp0s3
```

## Agregar una red remota 20.0.0.0/24 gateway: 192.168.10.254 

- El gateway tiene que ser una dirección que pertenezca a uno de los segmentos locales. De modo que, para poder alcanzar una red que no es local a la nuestra, vamos a mandar todo el trafico destinado a esa red a una puerta de enlace que si es local 
- Vamos a asociarla al dispositivo que tiene la ip de la puerta de enlace

```
ip r add 20.0.0.0/24 via 192.168.10.20 dev eth1 # eth1 tiene la IP 192.168.10.20

```

## Eliminar información de la tabla de enrutamiento 
```
ip r del 20.0.0.0/24
```

## Eliminar información de dirección de red
```
ip a del 192.168.10.20/24 dev eth1
```

## Eliminar toda configuración de red de un dispositivo
```
ip a flush dev eth1
```
## Ver tabla de ARP
Tabla de ARP: Asocia las direcciones IP con las que nosotros hemos interactuado localmente con su dirección MAC de forma de no tener que resolver esta relación cada vez que nos queremos volver a conectar con ese dispositivo

```
ip neigh

192.168.0.1 dev wlp3s0 lladdr <MAC> REACHABLE
192.168.0.3 dev wlp3s0 lladdr <MAC> REACHABLE
192.168.0.4 dev wlp3s0 lladdr <MAC> REACHABLE
fe80::267f:20ff:fe05:41fc dev wlp3s0 lladdr <MAC> router REACHABLE
```
