### Instalar Open MPI
```
apt update # Actualización de datos de repositorios

apt install build-essential # Instalamos las librerias necesarias para la compilación de Open MPI

wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.1.tar.bz2 # Descargamos el tarbar con el codigo fuente de la pagina

tar -xvjf openmpi-4.1.1.tar.bz2 # descomprimimos el archivo

cd openmpi-4.1.1

./configure --prefix=/opt/openmpi --enable-mpirun-prefix-by-default 2>&1 | tee config.out 
# --prefix: ubicacion del directorio donde vamos a compilar open mpi
# --enable... permite utilizar los comandos de open mpi sin tener que poner todo el path hacia el archivo
# --with-cuda=/usr/local/cuda en el caso de tener placas de video Nvidia
make all 2>&1 | tee make_all.out

make -j8 install 2>&1 | tee install.out

# | tee file.out permite imprimir por pantalla la salida del comando pero a la vez se guarda en un archivo.
# Esto es muy util en caso de que se produzcan errores para poder buscar sobre el archivo
```
Agregar la ubicación de los archivos al archivo .bashrc

PATH:            /opt/openmpi/bin

LD_LIBRARY_PATH: /opt/openmpi/lib

```
vi $HOME/.bashrc
```
```
export MPIROOT=/opt/openmpi
export PATH=$MPIROOT/bin
export LD_LIBRARY_PATH=$MPIROOT/lib
```
Finalmente se ejecuta el comando: `source $HOME/.bashrc` para actualizar los cambios
