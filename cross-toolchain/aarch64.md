# Build a GCC-based cross compiler toolchain for Linux -- AArch64

This tutorial describes how to build a GCC-based cross compiler toolchain
for a AArch64 Linux platform.

## 1 Prerequisites

To build a cross-compiler,
we need the source code of gcc, glibc, binutils, linux-kernel
and some standard packages.

### 1.1 Source code

[https://ftp.gnu.org/gnu/gcc/gcc-11.1.0/gcc-11.1.0.tar.xz](https://ftp.gnu.org/gnu/gcc/gcc-11.1.0/gcc-11.1.0.tar.xz)

[https://ftp.gnu.org/gnu/glibc/glibc-2.30.tar.xz](https://ftp.gnu.org/gnu/glibc/glibc-2.30.tar.xz)

[https://ftp.gnu.org/gnu/binutils/binutils-2.37.tar.xz](https://ftp.gnu.org/gnu/binutils/binutils-2.37.tar.xz)

[https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.19.230.tar.xz](https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.19.230.tar.xz)

Download the source code from the links above.
Building gcc and glibc requires linux kernel headers.
That's why the source code of linux kernel is downloaded.

### 1.2 Standard packages

To install the standard packages, run the following command

```bash
$ sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev
```

## 2 Setup the build environment

Assume "topdir" is the directory we will carry out the build process.
We put all the source code in directory "${topdir}/srcs".
We create a directory "${topdir}/build" for building the software
and a directory "${topdir}/install" for installing the software.

### 2.1 Create the "srcs", "build" and "install" directory

```bash
$ mkdir ${topdir}/srcs
$ mkdir ${topdir}/build
$ mkdir ${topdir}/install
```

### 2.2 Define a few environment variables

```bash
$ export srcdir=${topdir}/srcs
$ export builddir=${topdir}/build
$ export installdir=${topdir}/install
$ export targetarch=aarch64-unknown-linux-gnu
```

### 2.3 Unzip the source code

```bash
$ tar xvf gcc-11.1.0.tar.xz -C ${srcdir}
$ tar xvf glibc-2.30.tar.xz -C ${srcdir}
$ tar xvf binutils-2.37.tar.xz -C ${srcdir}
$ tar xvf linux-4.19.230.tar.xz -C ${srcdir}
```

## 3 The cross-compiler build process

### 3.1 Build binutils

binutils provides assembler (as) and linker (ld).

```bash
$ mkdir ${builddir}/build-binutils
$ cd ${builddir}/build-binutils
$ ${srcdir}/binutils-2.37/configure --enable-gold \
                                    --enable-plugins \
                                    --disable-werror \
                                    --prefix=${installdir} \
                                    --build=x86_64-linux-gnu \
                                    --host=x86_64-linux-gnu \
                                    --target=${targetarch}
$ make
$ make install
```

### 3.2 Build gcc stage1

At this stage,
only "c" is enabled as glibc requires only "aarch64-unknown-linux-gnu-gcc".

"c++" can not be enabled as it causes the following error:

"checking dynamic linker characteristics... configure: error: Link tests are not allowed after GCC_NO_EXECUTABLES."

"--with-newlib --without-headers" tells the configuration not to worry about headers and libc at this stage.

"libsanitizer" is disabled as it causes an error during the building process.

```bash
$ mkdir ${builddir}/build-gcc-stage1
$ cd ${builddir}/build-gcc-stage1
$ ${srcdir}/gcc-11.1.0/configure --prefix=${installdir} \
                                 --build=x86_64-linux-gnu \
                                 --host=x86_64-linux-gnu \
                                 --target=${targetarch} \
                                 --enable-languages=c \
                                 --with-newlib \
                                 --without-headers \
                                 --disable-libsanitizer \
                                 --disable-shared \
                                 --disable-threads \
                                 --enable-tls \
                                 --disable-libatomic \
                                 --disable-libmudflap \
                                 --disable-libssp \
                                 --disable-libquadmath \
                                 --disable-libgomp \
                                 --disable-nls \
                                 --disable-bootstrap
$ make
$ make install
```

### 3.3 Get linux headers

Specify "ARCH=arm64" instead of "ARCH=aarch64" is because
the linux-kernel only contains "arm64".

```bash
$ cd ${srcdir}/linux-4.19.230
$ make ARCH=arm64 INSTALL_HDR_PATH=${installdir}/${targetarch} headers_install
```

### 3.4 Build glibc

Modify glibc-2.30/Makefile and glibc-2.30/support/Makefile.
So "links-dso-program-c.c" is used instead of "links-dso-program.cc",
which requires a c++ compiler.

glibc-2.30/Makefile

```bash
#ifeq (,$(CXX))
LINKS_DSO_PROGRAM = links-dso-program-c
#else
#LINKS_DSO_PROGRAM = links-dso-program
#endif
```

glibc-2.30/support/Makefile

```bash
#ifeq (,$(CXX))
LINKS_DSO_PROGRAM = links-dso-program-c
#else
#LINKS_DSO_PROGRAM = links-dso-program
#LDLIBS-links-dso-program = -lstdc++ -lgcc -gcc_s $(libunwind)
#endif
```

Build glibc with "aarch64-unkonwn-linux-gnu-gcc" built in gcc stage1.

```bash
$ export PATH=${installdir}/bin:$PATH
$ export CC=aarch64-unknown-linux-gnu-gcc
$ mkdir ${builddir}/build-glibc
$ cd ${builddir}/build-glibc
$ ${srcdir}/glibc-2.30/configure --prefix=${installdir}/${targetarch} \
                                 --host=${targetarch} \
                                 --disable-werror \
                                 --disable-multilib \
                                 --with-headers=${installdir}/${targetarch}/include
$ make
$ make install
```

### 3.5 Build gcc stage2

This is the final stage.

```bash
$ mkdir ${builddir}/build-gcc-stage2
$ cd ${builddir}/build-gcc-stage2
$ ${srcdir}/gcc-11.1.0/configure --prefix=${installdir} \
                                 --build=x86_64-linux-gnu \
                                 --host=x86_64-linux-gnu \
                                 --target=${targetarch} \
                                 --enable-languages=c,c++,fortran \
                                 --enable-shared \
                                 --enable-tls \
                                 --disable-libsanitizer \
                                 --disable-libmudflap \
                                 --disable-libssp \
                                 --disable-libquadmath \
                                 --disable-nls \
                                 --disable-bootstrap
$ make
$ make install
```

After this stage is finished, we are ready to use the cross toolchain.

## 4 Use the cross-compiler toolchain

To use the cross-compiler toolchain, set the following environment:

```bash
$ export CROSSGCCDIR=${installdir}
$ export PATH=${CROSSGCCDIR}/bin:$PATH
```

Then we are ready to use
aarch64-unknown-linux-gnu-gcc,
aarch64-unknown-linux-gnu-g++,
and
aarch64-unknown-linux-gnu-gfortran.
