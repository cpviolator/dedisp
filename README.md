# dedisp
This repositry is derived from Ben Barsdell's original GPU De-dedispersion library (code.google.com/p/dedisp)

Installation Instructions (CMake):

Using command line CMake

  1. `git clone https://github.com/ajameson/dedisp.git`
  2. `mkdir build; cd build`

Using command line CMake, choose relevant ON/OFF values, ## two digit CUDA architecture,
and optional install path

  3. `cmake -DDEDISP_USE_TEXTURE=ON \
            -DDEDISP_BUILD_TESTS=ON \
            -DBUILD_SHARED_LIBS=ON \
            -DCMAKE_CUDA_ARCHITECTURES=70 \
            -DCMAKE_INSTALL_PREFIX="/scratch/CPviolator/work/DSA110/dedisp_local/install" \
            -DCMAKE_CUDA_COMPILER="/path/to/nvcc" ../dedisp`

Alternatively, use the CMake GUI via

  3. `ccmake ../dedisp`

Last, make (and install) the componenets

  4. `make -j install`

Using Makefile

  1.  `git clone https://github.com/ajameson/dedisp.git`
  2.  Update `Makefile.inc` with your CUDA path, Install Dir and GPU architecture. e.g.
      * `CUDA_PATH ?= /usr/local/cuda-12.4`
      * `INSTALL_DIR = $(HOME)/opt/dedisp`
      * `GPU_ARCH = sm_60`
  3.  `make && make install`
  
  Either of these will build a shared object library named `libdedisp.so` which is a prerequisite for Heimdall. The dedisp header files will be installed into `INSTALL_DIR/include`, the test and runtime library into `INSTALL_DIR/lib`. The CMake method will build static libraries `libdedisp.a, libdedisp_test.a` or a shared libraries `libdedisp.so, libdedisp_test.so` via the CMake variable `BUILD_SHARED_LIBS`. Install locations are unchanged, with the additions that the test executable will be placed into `INSTALL_DIR/bin` and `libdedisp_test.a/so` will be placed in `INSTALL_DIR/lib`. CMake's usual boiler plate configuration files are in the CMake default paths.
