Como root
```
mkdir /usr/local/amber20
cd /usr/local
cp Amber20.tar.bz2
cp AmberTools20.tar.bz2
tar xvfj Amber20.tar.bz2
tar xvfj AmberTools20.tar.bz2
cd amber20_src/build
# Esto es lo que ejecute y anduvo. La primera vez tiró warning por miniconda, pero dice que si se vuelve a correr cmake miniconda se instala solo
cmake /usr/local/amber20_src -DCMAKE_INSTALL_PREFIX=$AMBER_PREFIX/amber20 -DCOMPILER=GNU -DMPI=TRUE -DCUDA=TRUE 
# Hay que testear si esto funciona
cmake /usr/local/amber20_src -DCMAKE_INSTALL_PREFIX=$AMBER_PREFIX/amber20 -DCOMPILER=GNU -DMPI=TRUE -DCUDA=TRUE -DINSTALL_TESTS=TRUE -DDOWNLOAD_MINICONDA=TRUE -DMINICONDA_USE_PY3=TRUE -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-9.2 2>&1 | tee  cmake.log
make install -j 4 2>&1 | tee amber_install.log
```
