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
mkdir ${topdir}/srcs
mkdir ${topdir}/build
mkdir ${topdir}/install
```

### 2.2 Define a few environment variables

```bash
export srcdir=${topdir}/srcs
export builddir=${topdir}/build
export installdir=${topdir}/install
export targetarch=aarch64-unknown-linux-gnu
```

### 2.3 Unzip the source code

```bash
tar xvf gcc-11.1.0 -C ${srcdir}
tar xvf glibc-2.30.tar.xz -C ${srcdir}
tar xvf binutils-2.37.tar.xz -C ${srcdir}
tar xvf linux-4.19.230.tar.xz -C ${srcdir}
```

## 3 The cross-compiler build process
