---
layout: post
title: "Setting up gccgo using Docker"
date: 2017-10-03
keywords: "Golang,gcc,blog"
categories: [golang]
tags: [golang,gcc]
icon: icon-html
---
golang has an official [website](https://golang.org/doc/install/gccgo) talking about how to setup gccgo. However, the content is a bit out of dated and it doesn't cover some details you might missed while trying for yourself. It mentioned that you can download pre-built gcc release with go support. But from the link, the supported platforms are only AIX, DOS, HP-UX, etc. And I want to build it in ubuntu.

## Start Ubuntu 14 in docker container
Create Dockerfile as follows:
```
FROM ubuntu:14.04

RUN apt-get update
RUN apt-get install -y texinfo xz-utils wget bison build-essential git flex
```

Build the image:
```sh
docker build -t gccgo .
```

Start container:
```sh
docker run -it gccgo
```

## Install gccgo
There are multiple repositories hold gcc source code. I use git hub mirror:
```sh
git clone https://github.com/gcc-mirror/gcc.git gccgo
cd gccgo
git checkout gccgo
```
One difference to golang guide is that I removed `--with-ld=/opt/gold/bin/ld` flag in `./configure` command.

```sh
cd gccgo
./contrib/download_prerequisites
cd ..
make objdir
cd objdir

../gccgo/configure --prefix=/opt/gccgo --enable-languages=c,c++,go 
make
make install
```

**NOTES** : If you run out of disk space during build, use `make bootstrap-lean` which will delete unused compilation artifacts during build process.

After long waiting (4 hour+) ... ☕️

Add PATH and ldconfig
```sh
export PATH=$PATH:/opt/gccgo/bin
echo /opt/gccgo/lib64 > /etc/ld.so.conf.d/gccgo.conf
ldconfig
```

## Install Gold (Optional)
Download latest binutil, my current latest is 2.29:
```sh
wget http://ftp.gnu.org/gnu/binutils/binutils-2.29.tar.xz

tar xf binutils-2.29.tar.xz
mv binutils-2.29.tar.xz binutils
mkdir binutils-build
cd binutils-build

../binutils/configure --enable-gold=default --prefix=/opt/gold
make
make install
```
**Notes**: I find this is optional. In my case, when I set `--with-ld` to be gold, gcc compilation fails. Similar [issue](https://stackoverflow.com/questions/46558696/build-gccgo-got-linker-error). After I remove the option, compilation just works.

