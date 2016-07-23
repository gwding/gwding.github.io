---
layout: post
title: OpenCV 3.1.0 installation under Ubuntu 16.04 and Cuda 7.5
---

My OS version is Ubuntu 16.04.1 LTS. 

My Cuda 7.5 was installed by

```
sudo apt-get install nvidia-cuda-dev nvidia-cuda-toolkit
```

The opencv 3.1.0 package is downloaded from <http://opencv.org/downloads.html>.

In short, I did the following steps

```
export PYTHON2_EXECUTABLE=/usr/bin/python
export PYTHON_INCLUDE_DIR=/usr/include/python2.7
export PYTHON_INCLUDE_DIR2=/usr/include/x86_64-linux-gnu/python2.7
export PYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython2.7.so
export PYTHON2_NUMPY_INCLUDE_DIRS=/usr/lib/python2.7/dist-packages/numpy/core/include/
export PYTHON_LIBRARIES=

cd ~/pkgs/opencv-3.1.0
mkdir build
cd build

cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" ..
make -j7
sudo make install

cd ..
cp 3rdparty/ippicv/unpack/ippicv_lnx/lib/intel64/libippicv.a /usr/local/lib/
```

There are 3 problems I had with directly running instructions from the [website tutorial](http://docs.opencv.org/trunk/d7/d9f/tutorial_linux_install.html).

The **first** is that without adding `-D CUDA_NVCC_FLAGS="-D_FORCE_INLINES"`, there will be the following OpenCv compilation error.

>[  5%] Building NVCC (Device) object modules/core/CMakeFiles/cuda_compile.dir/src/cuda/cuda_compile_generated_gpu_mat.cu.o
/usr/include/string.h: In function ‘void* __mempcpy_inline(void*, const void*, size_t)’:
/usr/include/string.h:652:42: error: ‘memcpy’ was not declared in this scope
   return (char *) memcpy (__dest, __src, __n) + __n;
                                          ^
CMake Error at cuda_compile_generated_gpu_mat.cu.o.cmake:264 (message):
  Error generating file
  /home/gavin/pkgs/opencv-3.1.0/build/modules/core/CMakeFiles/cuda_compile.dir/src/cuda/./cuda_compile_generated_gpu_mat.cu.o
>
>modules/core/CMakeFiles/opencv_core.dir/build.make:399: recipe for target 'modules/core/CMakeFiles/cuda_compile.dir/src/cuda/cuda_compile_generated_gpu_mat.cu.o' failed
make[2]: *** [modules/core/CMakeFiles/cuda_compile.dir/src/cuda/cuda_compile_generated_gpu_mat.cu.o] Error 1
CMakeFiles/Makefile2:1507: recipe for target 'modules/core/CMakeFiles/opencv_core.dir/all' failed
make[1]: *** [modules/core/CMakeFiles/opencv_core.dir/all] Error 2
Makefile:160: recipe for target 'all' failed
make: *** [all] Error 2

This solution is found at <https://github.com/BVLC/caffe/issues/4046>.

The **second** problem is the following error when I tried to compile my own code.

>/usr/bin/ld: cannot find -lippicv
collect2: error: ld returned 1 exit status
Makefile:5: recipe for target 'dual_camera' failed
make: *** [dual_camera] Error 1

So somehow you need to copy the libippicv.a to the right path to resolve this. This solution is found at <http://stackoverflow.com/questions/34401117/compiling-code-with-opencv-usr-bin-ld-cannot-find-lippicv>. And the solution from the same webpage of adding `-D WITH_IPP=ON` didn't work for me.

The **third** problem is related to Anaconda python. Opencv doesn't seem to compile well with Anaconda's libraries. If your python library paths were set to the anaconda directories, you probably need to change the paths before compilation.