# SSH

## Instalar OpenSSH (servidor y cliente)

```
sudo apt update && sudo apt install openssh-server openssh-client
```

## Conectar con el servidor ssh
```
ssh -p 22 username@server_ip        # => Ex: ssh -p 2267 john@192.168.0.100
ssh -p 22 -l username server_ip
ssh -v -p 22 username@server_ip     # => verbose
```

## ssh-key

```
ssh-keygen -t ecdsa -b 521
# Seleccionar archivo donde se guarda (por defecto ~/.ssh/id-<algoritmo>)
# Poner contraseña (2 veces)
ssh-copy-id -i ~/.ssh/file-key-ecdsa user@host
```
Con esto tenemos un método de conexion más seguro. Sin embargo, cada vez que queramos conectarnos al servidor al que pasamos la clave publica vamos a tener que ingresar la contraseña. Para evitar esto podemos:
- Dejar la contraseña en blanco cuando creamos las claves, pero esto no es una buena práctica ya que si alguien tiene acceso fisico a la PC donde están almacenadas ambas claves puede ingresar al servidor. Sin embargo, puede ser útil entre maquinas que se manejan en una red privada (por ejemplo, los nodos de un cluster HPC)
- Usar ssh-agent y ssh-add:
	- Primero ejecutamos: `eval $(ssh-agent)`
	- Segundo: `ssh-add`. Aca nos pide la contraseña que usamos al crear las claves y ya podemos acceder a los servidores que tienen la clave pública sin contraseña mientras la terminal permanezca abierta.

