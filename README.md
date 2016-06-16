# AHCAL-Reconstruction
# AHCAL Testbeam Reconstruction Framework
Copyright DESY

## INSTALL:

Can be built standalonly or using [ilcsoft](http://ilcsoft.desy.de/portal).

The install procedure is managed by [CMake](http://cmake.org)

### Dependencies

All the needed packages are installed on-the-fly by CMake, except two of them :
* [ROOT](http://root.cern.ch)
* [Qt](www.qt.io).

Note that these two packages are provided by ilcsoft.

### Standalone install

In the root directory :

```bash
mkdir build
cd build
cmake [-DOPTIONS=...] ..
make
```

where OPTIONS can be :
* BUILD_DOCUMENTATION [ON/OFF] to install [doxygen](www.doxygen.org) code documentation

Example :

```bash
cmake -DBUILD_DOCUMENTATION=ON ..
```

All options area by default set to OFF

### Install with ilcsoft

```bash
mkdir build
cd build
cmake [-DOPTIONS=..] -C $ILCSOFT/ILCSoft.cmake ..
make
```

### Bug report

You can send emails to <eldwan.brianne@desy.de>
or use the github issues interface
