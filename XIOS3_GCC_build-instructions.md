# Building XIOS 3 on ARCHER2 using GCC

## Load Required Modules

```bash
module -q load PrgEnv-gnu
module -q load cray-hdf5-parallel
module -q load cray-netcdf-hdf5parallel
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

### `arch-ARCHER2_GNU.path`

```bash
NETCDF_INCDIR="-I $NETCDF_INC_DIR"
NETCDF_LIBDIR="-L $NETCDF_LIB_DIR"
NETCDF_LIB="-lnetcdff -lnetcdf"

MPI_INCDIR=""
MPI_LIBDIR=""
MPI_LIB="-lcurl"

HDF5_INCDIR="-I $HDF5_INC_DIR"
HDF5_LIBDIR="-L $HDF5_LIB_DIR"
HDF5_LIB="-lhdf5_hl -lhdf5 -lhdf5 -lz"

BOOST_INCDIR="-I $BOOST_INC_DIR"
BOOST_LIBDIR="-L $BOOST_LIB_DIR"
BOOST_LIB=""

OASIS_INCDIR="-I$PWD/../../oasis3-mct/BLD/build/lib/psmile.MPI1"
OASIS_LIBDIR="-L$PWD/../../oasis3-mct/BLD/lib"
OASIS_LIB="-lpsmile.MPI1 -lscrip -lmct -lmpeu"
```

---

### `arch-ARCHER2_GNU.fcm`

```fcm
%CCOMPILER      CC
%FCOMPILER      ftn
%LINKER         ftn  

%BASE_CFLAGS    -w -std=c++11 -D__XIOS_EXCEPTION 
%PROD_CFLAGS    -O3 -DBOOST_DISABLE_ASSERTS
%DEV_CFLAGS     -g -O2 
%DEBUG_CFLAGS   -g -O0

%BASE_FFLAGS    -D__NONE__ -cpp -ffree-line-length-none
%PROD_FFLAGS    -O3
%DEV_FFLAGS     -g -O2
%DEBUG_FFLAGS   -g -O0

%BASE_INC       -D__NONE__
%BASE_LD        -lstdc++

%CPP            cpp
%FPP            cpp -P
%MAKE           gmake
```

---

### `arch-ARCHER2_GNU.env`

```bash
export HDF5_INC_DIR=$HOME/hdf5/hdf5/include
export HDF5_LIB_DIR=$HOME/hdf5/hdf5/lib

export NETCDF_INC_DIR=$HOME/netcdf4/include
export NETCDF_LIB_DIR=$HOME/netcdf4/lib

export BOOST_INC_DIR=$HOME/boost
export BOOST_LIB_DIR=$HOME/boost
```

---

## Build XIOS

From the root directory of XIOS:

```bash
cd /path/to/xios-3.0
./make_xios --verbose --fcm new --job 8 --dev --arch ARCHER2_GNU 2>&1 | tee compile.log
```

It should compile and link without errors.
