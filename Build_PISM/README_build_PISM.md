## This file tells how to build prerequisites and pism on Odyssey
``` Last Modified by Weiwen Ji on Aug.30 2019 wwji@pku.edu.cn```

###   ===============   Load Prerequisities   ===============

`Mar.12 2019`  (http://pism-docs.org/sphinx/installation/pism.html?highlight=precise)
    
   Add options to get reproducible model results:
   
    `export CFLAGS="-fp-model precise"`
    `export CXXFLAGS="-fp-model precise".` 
    
### Begin   
     
     1). Load Main Modules
        module load intel/17.0.4-fasrc01 impi/2017.2.174-fasrc01 netcdf/4.1.3-fasrc02
        module load cmake/3.5.2-fasrc01
        module load udunits/2.2.26-fasrc01  #comp
        module load fftw/3.3.7-fasrc04  #mpi
        module load nco/4.7.4-fasrc01  #comp
        module load Anaconda/5.0.1-fasrc02  #python 2 only
        module load proj/4.9.3-fasrc01
        
     2). Install Python Packages
        conda create -n PISM_python_packages python=2.7.14  
        source activate PISM_python_packages
        conda install numpy
        conda install scipy
        conda install netcdf4
        
     3). Load Other Modules
        module load nco/4.7.4-fasrc01
        module load ncl_ncarg/6.4.0-fasrc01
        module load ncview/2.1.7-fasrc01
        module load ffmpeg/4.0.2-fasrc01
        
      # The Currently Loaded Modules: (for references)
      1) intel/17.0.4-fasrc01      7) gsl/2.4-fasrc01          13) opus/1.0.3-fasrc01     19) opencore-amr/0.1.3-fasrc01  25) libvorbis/1.3.4-fasrc01  31) Anaconda/5.0.1-fasrc02
      2) impi/2017.2.174-fasrc01   8) antlr/2.7.7-fasrc01      14) fdk-aac/0.1.3-fasrc01  20) libass/0.11.2-fasrc01       26) ffmpeg/4.0.2-fasrc01     32) proj/4.9.3-fasrc01
      3) zlib/1.2.8-fasrc07        9) ncl_ncarg/6.4.0-fasrc01  15) lame/3.99.5-fasrc01    21) fribidi/0.19.1-fasrc01      27) cmake/3.5.2-fasrc01
      4) szip/2.1-fasrc02         10) ncview/2.1.7-fasrc01     16) nasm/2.13.03-fasrc01   22) enca/1.19-fasrc01           28) udunits/2.2.26-fasrc01
      5) hdf5/1.8.12-fasrc12      11) yasm/1.3.0-fasrc01       17) x264/20180705-fasrc01  23) libogg/1.3.2-fasrc01        29) fftw/3.3.7-fasrc04
      6) netcdf/4.1.3-fasrc02     12) xvidcore/1.3.3-fasrc01   18) libvpx/1.7.0-fasrc01   24) libtheora/1.1.1-fasrc01     30) nco/4.7.4-fasrc01
        
       
          
      4). # Install PETSc
         # Download
          git clone -b maint https://gitlab.com/petsc/petsc.git petsc
          
         # Configure
          ./config/configure.py \
            --with-fc=0 \
            --with-64-bit-indices=1 \
            --known-64-bit-blas-indices \
            --known-mpi-shared-libraries=1 \
            --with-debugging=0 \
            --with-valgrind=1 \
            --with-batch=1  \
            --with-shared-libraries=1 \
            --with-mpiexec="mpirun" \
            --download-f2cblaslapack=1
            
        Submit ./conftest-arch-linux2-c-debug to 1 processor of your batch system or system you are cross-compiling for; this will generate the file reconfigure-arch-linux2-c-debug.py 
        sbatch -c 1 -p huce_intel -t 100 --mem=4000 --wrap='./conftest-arch-linux2-c-debug'
        
        Run ./reconfigure-arch-linux2-c-debug.py at HOME node (to complete the configure process).

   make PETSc with batch jobs
   
          sbatch -c 8 -p huce_intel -t 100 --mem=20000 --wrap=
         ' make PETSC_DIR=$PETSC_DIR PETSC_ARCH=$PETSC_ARCH all;
          make PETSC_DIR=$PETSC_DIR PETSC_ARCH=$PETSC_ARCH check '
         
###   ===============   Build PISM   ===============

```
   # Download  
   git clone git://github.com/pism/pism.git pism-stable
   mkdir ~/pism-stable/build
   cd ~/pism-stable/build
   

   Add "if (POLICY CMP0074)
          cmake_policy(SET CMP0074 NEW) # CMake 3.12
        endif ()"
   in CMakeList.txt  
   
   PISM_INSTALL_PREFIX=~/pism CC=mpicc CXX=mpicxx cmake .. -DPETSC_EXECUTABLE_RUNS=ON
   PISM_INSTALL_PREFIX=~/pism CC=mpiicc CXX=mpiicpc cmake .. -DPETSC_EXECUTABLE_RUNS=ON
   # May have some failed tests


   # Turn on Proj4
   make edit_cache
   Turn on 'Pism_USE_PROJ4' Press "c" "c" "g"
   make

   make install -j 8
   
   do 
   make
   make install
   everytime changes something in the source code

 ```
 

