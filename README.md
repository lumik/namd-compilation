# Description of namd2.12 compilation
```
# download NAMD_2.12_Source.tar.gz to namd/ directory

# clone repository with patches and installation instructions to 'namd' directory.
cd namd
git init
git remote add origin https://github.com/lumik/vmd-compilation.git
git fetch
git reset --hard origin/master

# extract package
tar -xvzf NAMD_2.12_Source.tar.gz
cd NAMD_2.12_Source
tar -xvf charm-6.7.1.tar

# build Charm++/Converse library
cd charm-6.7.1
./build charm++ multicore-linux32 --with-production
cd multicore-linux32/tests/charm++/megatest
make pgm
./pgm +p4
cd ../../../../..

# Download and install TCL and FFTW libraries:
wget http://www.ks.uiuc.edu/Research/namd/libraries/fftw-linux.tar.gz
tar -xzvf fftw-linux.tar.gz
mv linux fftw
wget http://www.ks.uiuc.edu/Research/namd/libraries/tcl8.5.9-linux-x86.tar.gz
tar -xzvf tcl8.5.9-linux-x86.tar.gz
mv tcl8.5.9-linux-x86 tcl
# compile 32bit threaded tcl
wget http://www.ks.uiuc.edu/Research/namd/libraries/tcl8.5.9-src.tar.gz
tar -xzvf tcl8.5.9-src.tar.gz
mv tcl8.5.9 tcl-threaded
cd tcl-threaded/unix
./configure --enable-threads --enable-shared=no --prefix=/home/user/Downloads/namd/NAMD_2.12_Source/tcl-threaded
make distclean
make
make install

# compile namd2
./config Linux-x86-g++ --charm-arch multicore-linux32 --with-python --cc-opts -lpthread
cd Linux-x86-g++
make -j2

# test it
./namd2 src/alanin
./charmrun +p2 ./namd2 src/alanin

# install namd2
make release
cd NAMD_2.12_Linux-x86-multicore
mkdir bin
mv charmrun flip* namd2 psfgen sortreplicas bin/
cd ..
sudo mkdir /opt/namd
sudo mv NAMD_2.12_Linux-x86-multicore /opt/namd/2.12
echo 'export PATH=/opt/namd/2.12/bin\:$PATH' >> ~/.bashrc

```
