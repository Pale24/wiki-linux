# Clase 3

## Temas:
- 8. Gestión de redes
    - IP estática
    - forward
    - fijar nombres de hosts
- 9. Gestión de dispositivos

# Gestión de redes

Una red de computadoras es un conjunto de equipos y software conectados entre sí por medio de dispositivos físicos que envían y reciben impulsos eléctricos, ondas electromagnéticas o cualquier otro medio para el transporte de datos, con la finalidad de compartir información, recursos y ofrecer servicios. 

Sin una forma de encaminar el tráfico de datos de un lugar a otro, la comunicación en red sería imposible, por lo cual, es necesaria la implementación de ciertos protocolos o reglas. El Protocolo de Control de Transmisión (TCP, Transmission Control Protocol) y el Protocolo de Internet (IP, Internet Protocol), o TCP/IP para abreviar se han convertido en los protocolos predominantes de Internet.

Algunos conceptos de redes:

- El Protocolo de Control de transmisión o TCP, es un protocolo que se encarga de una entrega fiable de la información de una red a otra.

- El Protocolo de Internet o IP, es responsable de entregar nuestros paquetes a los equipos correctos. El protocolo de Internet nos ayuda a encaminar la información. 

- Los dispositivos que pueden conectarse a una red tienen un identificador único llamado dirección MAC. Las direcciones MAC son por lo general permanentes y están codificadas en un dispositivo. Además para poder conectarse a la red a la cual el dispositivo debe disponer de una dirección IP.

- Los datos se envían a través de una red por medio de paquetes. Cuando movemos datos a través de la red, los dividimos en paquetes. Cuando un paquete llega a su destino, se volverá a ordenar.

- Servidores y clientes. Un servidor es algo que proporciona datos a algo que los solicita, el cliente. 

## Protocolo TCP/IP

Para entender la red de verdad, necesitamos entender todos los componentes involucrados. Hablamos de todo, desde los cables que conectan dispositivos entre sí a los protocolos que estos dispositivos utilizan para comunicarse. Hay un montón de modelos que permiten explicar cómo se comunican los dispositivos de red, pero en este curso, nos centraremos en un modelo de cuatro capas.

| TCP/IP                    |
|:-------------------------:|
| 4. Capa de aplicación     |
| 3. Capa de transporte     |
| 2. Capa de internet       |
| 1. Capa de acceso a Redes |

- 1. La capa de acceso a la red especifica las características del hardware (incluyendo el medio de transporte) y la forma en la que los datos deben enrutarse.
- 2. La capa de Internet permite administrar las direcciones de enrutamiento de datos. Es esta capa la que permite que diferentes redes se comuniquen entre sí a través de dispositivos conocidos como routers. Permite que un paquete llegue desde un origen a un destino. Esta capa engloba protocolos como IP (Internet Protocol) e ICMP (Internet Control Message Protocol).
- 3. La capa de transporte se encarga de transporte de datos, su división en paquetes y la administración de potenciales errores de transmisión. Es aquí donde encontramos al protocolo TCP (Transmission Control Protocol).
- 4. La capa de aplicación es administrada directamente por el software y está relacionado con el tipo de servicio que provee una determinada aplicación. Ejemplo de protocolos de esta capa son ssh, http, ftp, y rsyslog. Cada uno de estos protocolos tiene una finalidad específica (http es para la web, ftp para transferir archivos, etc).

## Dirección IP

Según [wikipedia](https://es.wikipedia.org/wiki/Direcci%C3%B3n_IP):

La dirección IP es un conjunto de números que identifica, de manera lógica y jerárquica, a una interfaz en la red (elemento de comunicación/conexión) de un dispositivo (computadora, laptop, teléfono inteligente) que utilice el protocolo (Internet Protocol) o que corresponde al nivel de red del modelo TCP/IP.

La dirección IP puede cambiar a menudo debido a cambios en la red, o porque el dispositivo encargado dentro de la red de asignar las direcciones IP, decida asignar otra IP (por ejemplo, con el protocolo DHCP). A esta forma de asignación de dirección IP se le denomina también dirección IP dinámica (normalmente abreviado como IP dinámica). Los sitios de Internet que por su naturaleza necesitan estar permanentemente conectados, generalmente tienen la necesidad de una dirección IP fija (comúnmente, IP fija o IP estática). Esta no cambia con el tiempo.

Los dispositivos se conectan entre sí mediante sus respectivas direcciones IP. Sin embargo, para las personas es más fácil recordar un nombre de dominio que los números de la dirección IP. Los servidores de nombres de dominio DNS, "traducen" el nombre de dominio en una dirección IP. Si la dirección IP dinámica cambia, es suficiente actualizar la información en el servidor DNS. El resto de las personas seguirán accediendo al dispositivo por el nombre de dominio.

Actualmente, se usan 2 versiones de direcciones IP: IPv4 e IPv6 (pensada para reemplazar a IPv4).

### IPv4:

Las direcciones IPv4 se expresan mediante un número binario de 32 bits permitiendo un espacio de direcciones de hasta 4.294.967.296 (2^32) direcciones posibles. Una dirección IP está compuesta por 4 octetos y cada octeto se describe normalmente en números decimales. Ocho bits de datos, o un solo octeto, pueden representar todos los números decimales del 0 al 255 (0-255.0-255.0-255.0-255). Por ejemplo, la dirección 12.34.56.78 es una IP válida. Sin embargo, la dirección 123.456.789.100 no lo sería porque tiene números más grandes que los que se podrían representar mediante 8 bits.

```
       0.       0.       0.       .0
00000000.00000000.00000000.00000000
11111111.11111111.11111111.11111111 -> 32 bits
     255.     255.     255.     255 
```

Las direcciones IP se pueden dividir en dos secciones, la ID de red y la ID de host. Siguiendo está idea inicialmente las redes se dividieron en 4 clases según el numero de octetos que definia la red (classfull). 
- Las direcciones de clase A son aquellas en las que se utiliza el primer octeto para la ID de red, y los tres últimos se utilizan para la ID de host. 
- Las direcciones de clase B son las que utilizan los dos primeros octetos para la ID de red, y los dos segundos se utilizan para la ID de host. 
- Las direcciones de clase C son aquellas donde se utilizan los primeros tres octetos para la ID de red y solo se utiliza el octeto final para la ID de host. 

Pero, como dice wikipedia:

> El diseño de redes de clases (classful) sirvió durante la expansión de internet, sin embargo este diseño no era escalable y frente a una gran expansión de las redes en la década de los noventa, el sistema de espacio de direcciones de clases fue reemplazado por una arquitectura de redes sin clases (Classless Inter-Domain Routing - CIDR) en el año 1993. CIDR está basada en redes de longitud de máscara de subred variable (variable-length subnet masking VLSM), lo que permite asignar redes de longitud de prefijo arbitrario. Permitiendo por tanto una distribución de direcciones más fina y granulada, calculando las direcciones necesarias y "desperdiciando" las mínimas posibles.






## Localización de archivos de configuración de red

- /etc/hosts: Este archivo es un simple archivo de texto que asocia direcciones IP con nombres de dominio, una linea por dirección IP. Por cada host, hay una linea con: `IP hostname [alias...]`
- /etc/resolv.conf: "resolver" es un set de rutinas que proveen acceso al sistema de dominio de nombres (DNS, Internet Domain System). El archivo de configuración resolv.conf contiene información que es leida por esta rutina la primera vez que es invocada por el proceso 
- /etc/nsswitch.conf: El archivo de configuración Name Service Switch (NSS) es usado por librerias y otras aplicaciones para determinar fuentes desde donde obtener información de servicios y en que orden. Cada servicio es identificado por el nombre de la base de datos. En la línea host: files dns quiere decir que ante una pregunta de un nombre de dominio primero la buscará en files (en /etc/hosts) y luego si no la encuentra irá al servidor DNS configurado.
- /etc/network/interfaces: El archivo interfaces es usado para definir los nombres lógicos de las interfaces de redes, a los que se les asociará una configuración determinada. 
- /etc/netplan/* # Ubuntu: Archivos yaml con info de como manejar las redes/etc/netplan/*

## Configuración persistente de interfaces de red

### Configuración IP estática en Ubuntu (a partir de la versión 18.04):

Para configurar la red en Ubuntu debemos modificar los archivos /etc/netplan/*.

Por ejemplo:

```
vi /etc/netplan/00-installer-config.yaml

# This is the network config written by 'subiquity'
#network:
#  ethernets:
#    enp2s0:
#      dhcp4: true
#    enp3s0:
#      dhcp4: true
#  version: 2
network:
  ethernets:
    enp2s0:
      dhcp4: no
      addresses: [<IP estatica>/<Mascara de red>]
      gateway4: <IP Gateway>
      nameservers:
              addresses: [8.8.8.8,8.8.4.4]
    enp3s0:
      dhcp4: true
  version: 2

```

#### Configuración modo router:
`vi /etc/sysctl.conf`
Descomentamos `net.ipv4.ip_forward=1` y `net.ipv6.conf.all.forwarding=1` para permitir el trafico ipv4 y 6 (este ultimo para poder usar el servidor de nombres de google -8.8.8.8-). Luego, ejecutamos:
`iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE`
Lo anterior no es persistente, ya que al reiniciar el dispositivo se pierde la configuración.

Lo que hay que hacer es instalar iptables-persistent y agregar la configuración al archivo rules.v4 en /etc/iptables/rules.v4:
`apt-get install iptables-persistent`
Contenido de /etc/iptables/rules.v4:
```bash
*nat
...
:OUTPUT ACCEPT [0:0]
-A POSTROUTING -o enp0s3 -j MASQUERADE
...
```



