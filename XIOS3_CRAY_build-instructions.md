# Building XIOS 3 on ARCHER2 using Cray

## Load Required Modules

```bash
module purge
module load cce/15.0.0
module load craype/2.7.19
module load craype-network-ofi
module load cray-mpich/8.1.23
module load cray-dsmml/0.2.2
module load cray-libsci/22.12.1.1
module load cray-hdf5-parallel/1.12.2.7
module load cray-netcdf-hdf5parallel/4.9.0.7
module load PrgEnv-cray/8.3.3
module load craype-x86-rome
```

## Download XIOS 3

```bash
mkdir XIOS_3
svn checkout https://forge.ipsl.jussieu.fr/ioserver/svn/XIOS3/trunk xios-3.0
```

## Create Architecture Files

Navigate to the `arch/` directory:

```bash
cd xios-3.0/arch/
```

Create the following files with the respective content:

---

### `arch-ARCHER2_CCE.path`

```bash
NETCDF_INCDIR="-I/opt/cray/pe/netcdf-hdf5parallel/4.9.0.7/crayclang/14.0/include"
NETCDF_LIBDIR="-L/opt/cray/pe/netcdf-hdf5parallel/4.9.0.7/crayclang/14.0/lib"
NETCDF_LIB="-lnetcdf -lnetcdff"

HDF5_INCDIR="-I/opt/cray/pe/hdf5-parallel/1.12.2.7/crayclang/14.0/include"
HDF5_LIBDIR="-L/opt/cray/pe/hdf5-parallel/1.12.2.7/crayclang/14.0/lib"
HDF5_LIB="-lhdf5_hl -lhdf5 -lz"

MPI_INCDIR=""
MPI_LIBDIR=""
MPI_LIB=""
```

---

### `arch-ARCHER2_CCE.fcm`

```fcm
%CCOMPILER           cc
%FCOMPILER           ftn
%LINKER              ftn

%BASE_CFLAGS
%PROD_CFLAGS         -O2 -std=c++17
%DEV_CFLAGS          -g -std=c++17
%DEBUG_CFLAGS        -O0 -g -std=c++17

%BASE_FFLAGS
%PROD_FFLAGS         -O2
%DEV_FFLAGS          -O2 -g
%DEBUG_FFLAGS        -O0 -g

%BASE_INC            -I./inc -D__NONE__
%BASE_LD             -lstdc++

%FPP                 cpp -P
%CPP                 cpp -P
%MAKE                make
%SRC_itimer.f90         FFLAGS=-O1
```

---

### `arch-ARCHER2_CCE.env`

```bash
export CC=cc
export CXX=CC
export FC=ftn

export NETCDF_INC=/opt/cray/pe/netcdf-hdf5parallel/4.9.0.7/crayclang/14.0/include
export NETCDF_LIB="-L/opt/cray/pe/netcdf-hdf5parallel/4.9.0.7/crayclang/14.0/lib -lnetcdf -lnetcdff"

export HDF5_INC=/opt/cray/pe/hdf5-parallel/1.12.2.7/crayclang/14.0/include
export HDF5_LIB="-L/opt/cray/pe/hdf5-parallel/1.12.2.7/crayclang/14.0/lib -lhdf5_hl -lhdf5 -lz"

export MPI_INC=""
export MPI_LIB=""
```

---

## Build XIOS

From the root directory of XIOS:

```bash
cd /path/to/xios-3.0
./make_xios --arch ARCHER2_CCE --prod --full
```

It should compile and link without errors.
