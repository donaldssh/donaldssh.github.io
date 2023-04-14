---
layout: post
title:  "Install OpenCV 4 from source inside a conda environment"
date:   2021-01-03 12:00:00
categories:
    - blog
tags:
    - system
---

# Motivation

In this guide we'll see how to install OpenCV from source inside a conda environment.

This is useful when we want to set some specific compilation flags, i.e. to install also the patented modules inside `cv::xfeature2d`, in order to use surf and sift descriptors.

Otherwise, if we just need the basic Python installation, we could install opencv-python with pip.

# Installation

Download OpenCV and OpenCV-Contrib latest release archives.
```sh
wget -c https://github.com/opencv/opencv/archive/4.5.1.tar.gz -o opencv4
wget -c https://github.com/opencv/openc_contrib/archive/4.5.1.tar.gz -o opencv4_contrib
```
Extract the tar.gz (or zip) archives
```sh
tar -xvzf opencv4
tar -xvzf opencv4_contrib
```
Place opencv folders where you want, in my case I'll place them in /opt.
```sh
mv ~/opencv4 /opt
mv ~/opencv4_contrib /opt
```
Create the build directory where the source code will be installed.
```sh
cd /opt/opencv4
mkdir build && cd build
```
Select the path of the conda environment, in my case it is called opencv4 and the opencv4_contrib.
```sh
conda_env_path=~/anaconda3/envs/opencv4 
opencv_contrib_path=/opt/opencv4_contrib
```
We will use the option -D in cmake to  specify  a setting that takes priority over the project's default value.

Run the cmake command by specifying `OPENCV_ENABLE_NONFREE=ON` and the conda environment.
```sh
cmake   -D CMAKE_BUILD_TYPE=RELEASE \
        -D OPENCV_EXTRA_MODULES_PATH=$opencv_contrib_path/modules \
        -D PYTHON3_EXECUTABLE=$conda_env_path/bin/python \
        -D PYTHON3_LIBRARY=$conda_env_path/lib/python3.8 \
        -D PYTHON3_INCLUDE_DIR=$conda_env_path/include/python3.8 \
        -D PYTHON3_PACKAGES_PATH=$conda_env_path/lib/python3.8/site-packages \
        -D BUILD_opencv_python2=OFF \
        -D BUILD_opencv_python3=ON \
        -D INSTALL_PYTHON_EXAMPLES=OFF \
        -D INSTALL_C_EXAMPLES=OFF \
        -D OPENCV_ENABLE_NONFREE=ON \
        -D BUILD_EXAMPLES=OFF ..
```
Set the number of cores for the installation and compile.
```sh
make -j9
sudo make install
```
Link the shared library to the conda environment.
```sh
cd $conda_env_path/lib/python3.8
ln -s /opt/opencv/build/lib/python3/cv2.cpython-38-x86_64-linux-gnu.so
```
# Test
To test if it works activate the conda environment.
```sh
conda activate opencv4
```
Open a python shell, import cv2 and test if the modules from xfeature2d are working.
```
$ python          
Python 3.8.5 (default, Sep  4 2020, 07:30:14) 
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.

>>> import cv2
>>> cv2.xfeatures2d.SURF_create()
<xfeatures2d_SURF 0x7fdfbcdf30f0>
```
In case it is not correctly installed we will the following error
```sh
/src/surf.cpp:1029: error: (-213:The function/feature is not implemented) This algorithm is patented and is excluded in this configuration; Set OPENCV_ENABLE_NONFREE CMake option and rebuild the library in function 'create'
```
