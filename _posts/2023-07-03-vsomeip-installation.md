---
layout: post
category: tutorials
---

The compilation of [vsomeip](https://github.com/COVESA/vsomeip) is something really easy to do. However, when it is your first time you could spend a lot time trying to do it. The idea of this post is facilate the compilation of [vsomeip](https://github.com/COVESA/vsomeip) from source.

Right now, there are three branches which are: *maintain/2.6* *maintain/3.1* and *master*. From vsomeip3 is where was first implemented [SOME/IP-TP](https://www.autosar.org/fileadmin/standards/R20-11/CP/AUTOSAR_SWS_SOMEIPTransportProtocol.pdf).


| Branch  | Boost version |
| ------------- | ------------- |
| maintain/2.6  | 1.55  |
| maintain/3.1  | 1.55, 1.66, 1.70, 1.74, 1.76  |
| master  | ~~1.55~~, 1.66 onwards  |

## Table of contents
- [Linux](#linux-installation)
- [Windows](#windows-installation)
- [Docker](#Docker-compilation)

## [Linux Installation](#linux-installation)

Current master branch is not compatible with *boost 1.55*. If you are running Ubuntu distro, you could just run: 

```
apt-get install libboost-all-dev
```

With Ubuntu 20.04 you will get 1.71 which is perfect for your needs. Here follow the readme in the repo I think it is enough for getting everything ready. If you want the documentation you shall install *asciidoc source-highlight doxygen graphviz*.


```
git clone https://github.com/COVESA/vsomeip
cd vsomeip && mkdir build && cd build
cmake .. 
make
make install
```

If you want docs

```
make docs
```

If everything is fine. You will have a library installed in your machine. If you install boost library in unusual path. For instance: /opt/MyBoost or something like that. This error is more common if you compile from source boost library or if you specified differents paths. The error that you could get is: 

**Configuration module could not be loaded!**

This is due to misconfiguration of your path. There are multiple of ways of solving this. One of them, and quickly, add the path where you library is installed to *LD_LIBRARY_PATH*. 

This solution is just in the enviroment that you are using, if you want something permanent. You could add this path to paths where the dynamic linker could look for. 

```bash
/etc/ld.so.conf.d/ # <<== Here you create a file with .conf extension which is the path where your boost libs are installed
```

## [Windows Installation](#windows-installation)

I'm not a windows guy. So, maybe there are some conceptual errors that I did. However, If you follow this, you will end with a working vsomeip in Windows.

If you do not want to compile boost library from source code. You could download from [boost-binaries](https://sourceforge.net/projects/boost/files/boost-binaries/). The only limitation is that you shall use the mvsc version with it was compiled. 

msc has an internal [version numbering](https://en.wikipedia.org/wiki/Microsoft_Visual_C%2B%2B#Internal_version_numbering)

At 03-Jun-2023, the functional branch is maintain/3.1. You could compile master but with certains restriction like use boost 1.66 and thread library. I will cover here the *maintain/3.1*.

| Library  | min version | maximum version |
| ------------- | ------------- |------------- |
| boost  | 1.55  | 1.76 |

From [boost-binaries](https://sourceforge.net/projects/boost/files/boost-binaries/) you could choose base on the table shows below


| Version  | mscv version | Ver |
| ------------- | ------------- |------------- |
| Visual Studio 2017  | 14.1  | 15.0, 15.1 o 15.2 |
| Visual Studio 2019  | 14.2  | 16.0 |


For instance, if I want to use Visual studio 2019 and boost 1.74, and my computer is x64, I shall choose [boost_1_74_0-msvc-14.2-64.exe](https://sourceforge.net/projects/boost/files/boost-binaries/1.74.0/boost_1_74_0-msvc-14.2-64.exe/download)

This is just a normal exe. You could install a normal way. It is importat to remember the path where you install the library. When the installation is finished you will have a folder with C:\path\to\your\boost_1.74.0\lib64_mscv-14.2 

Once you had this, you shall add the cmake directory to your PATH. 

After this, I would recommend you to use cmake-gui for make the configuration. By default, tha path like DEFAULT_CONFIGURATION_FILE are path unix. You could change according to your use. 

Click generate and you will get the Visual Studio solution (vsomeip.sln).

Open it with Visual Studio and righ click in ALL_BUILD and build.

After everything is ready. You could install it with right click on INSTALL and build. **Remember:** If you chose certain path which needs admin you must lauch Visual Studio as Adminsitrator. However, this is not needed as you could install it in local, desktop or where you wish. 

## [Compilation with Docker](#Docker-compilation)

The idea here is shown that you can compile vsomeip in a docker enviroment. With this Dockerfile you could compile the library


```Dockerfile
FROM ubuntu:20.04

RUN export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get install -y \
    git \
    build-essential \
    wget \
    cmake \
    libbz2-dev \
    nano \
    libboost-all-dev
```
You could build the image with: 

```
docker build -t vsomeip-test .
```

After everything is fine, you could instaciate the image and create the container


```
docker run -it --rm vsomeip-test /bin/bash
```

Inside the container you could just clone the [vsomeip repo](https://github.com/COVESA/vsomeip) and do the steps:

```bash
mkdir build && cd build
cmake ..
make
```
