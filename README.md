# How to run R code on the c2b2 cluster.
Based on Jaime's Python tutorial here: https://github.com/ChaosDonkey06/c2b2_cluster_example

## Basic Steps

1. Connect to the cluster. Replace mah2350 with your uni. The password should be the same as to your email.

    -  ssh -p 59922 mah2350@login.c2b2.columbia.edu
    
2. Once you log onto the cluster you will automatically be placed in your home directory.
```bash
pwd
=>/ifs/home/msph/ehs/mah2350
```
The ending is based on your deparment and uni. Make sure to change it accorindgly throughout the tutorial. Move to your scratch directory, where you will want to store results and data.
```bash
cd /ifs/scratch/msph/ehs/mah2350
```
Create a folder to store your R libraries.
```bash
mkdir R_libs
```

3. Install your R libraries with the following steps:
```bash
qrsh
    module load R/4.1.1 (or whatever R version you are using)
    R
        install.packages("package_name", lib="/ifs/scratch/msph/ehs/mah2350/R_libs")
        quit()
    exit
```

4. Create your R file. When loading libraries, include the following parameter in order to specify their location.
```
library(package_name, lib.loc="/ifs/scratch/msph/ehs/mah2350/R_libs")
```

5. Create your bash script. It can look something like this:
```bash
#!/bin/sh

module load R/4.1.1
R=/nfs/apps/R/4.1.1/bin/R

${R} --vanilla < filepath/test.R >  filepath/test.log 2>  filepath/test.log
```
This will send the error and output of test.R to test.log. Otherwise, the error and output will automatically appear in your home directory.

6. Run your bash script.
```bash
qsub run.sh
```

7. Check the status of your bash script with 
```bash
qstat
```

## Possible debugging steps

If R crashes while you try to install a package, it may be running out of memory. Increase memory size in your qrsh session by typing
```bash
qrsh -l mem=4G
```
instead of qrsh.

If you get a weird C++ error while installing a package, type
```bash
source /opt/rh/devtoolset-8/enable
```
while in qrsh. Together, you would do:
```bash
qrsh -l mem=4G
    source /opt/rh/devtoolset-8/enable
    module load R/4.1.1 (or whatever R version you are using)
    R
        install.packages("package_name", lib="/ifs/scratch/msph/ehs/mah2350/R_libs")
        quit()
    exit
```

Your package may have dependencies that are not installed on the cluster or whose version is out-of-date. For example, CARBayesST is dependent on GDAL. You will have to download these dependencies yourself. Note that you must download from source using wget because you probably do not have the admin priviledges to use sudo apt.
```bash
qrsh
    cd /ifs/scratch/msph/ehs/mah2350
    wget http://download.osgeo.org/gdal/3.5.0/gdal-3.5.0.tar.gz
    tar -xvzf gdal-3.5.0.tar.gz
    cd gdal-3.5.0
    mkdir build
    cd build
    module load cmake/3.4.2
    cmake ..
    cmake --build .
    cmake --build . --target install
    exit
```

Some dependencies may not use cmake to install. For example to install SQLite, you would do
```bash
qrsh
    cd /ifs/scratch/msph/ehs/mah2350
    wget http://download.osgeo.org/gdal/3.5.0/gdal-3.5.0.tar.gz
    tar -xvzf gdal-3.5.0.tar.gz
    cd gdal-3.5.0
    ./configure --prefix=/path/to/installation/directory
    make
    make install
    exit
```

You may find that your dependencies require their own dependencies. I had to download 5 new libraries in order to install CARBayesST. When downloading a library with a manually installed dependency, you must specify the location of that dependency's include folder and libisqlite3.so file. This is done in the "cmake .." step. For example, installing proj requires sqlite and tiff. These dependencies were specified as such:
```bash
 cmake -D SQLITE3_INCLUDE_DIR=/ifs/scratch/msph/ehs/mah2350/sqlite/include -D SQLITE3_LIBRARY=/ifs/scratch/msph/ehs/mah2350/sqlite/lib/libsqlite3.so -D TIFF_LIBRARY=/ifs/scratch/msph/ehs/mah2350/tiff-4.3.0/lib64/libtiff.so -D TIFF_INCLUDE_DIR=/ifs/scratch/msph/ehs/mah2350/tiff-4.3.0/include -D PROJ_INCLUDE_DIR=/ifs/scratch/msph/ehs/mah2350/proj-9.0.0/include -D PROJ_LIBRARY=/ifs/scratch/msph/ehs/mah2350/proj-9.0.0/lib64/libproj.so .. -DCMAKE_INSTALL_PREFIX=/ifs/scratch/msph/ehs/mah2350/gdal-3.5.0
```
Once the original dependency is installed, you will have to let your cluster know its location by changing your .bashrc file (which is in your home directory).
```bash
cd
vi .bashrc
```
Include the following line at the end of your .bashrc file:
```
export PATH="/ifs/scratch/msph/ehs/mah2350/gdal-3.5.0:$PATH"
```

Now you should be able to run install.packages succesfully.

If you run into errors, ChatGPT is surprisngly helpful in debugging package managment.
