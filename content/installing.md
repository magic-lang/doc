---
title: Installing
menu:
  main:
    weight: 20
author: Simon Mika
copyright: 2014 Simon Mika
license: CC BY-SA-4.0
---

# Bootstrapping

The OOC compiler Rock is not currently installed but rather bootstrapped from sources.

Start by creating a folder for all OOC projects and move to that folder.
There you issue the following commands:

    #!/bin/bash
    sudo apt-get -y -qq install curl make libgc-dev binutils-dev libiberty-dev zlib1g-dev
    export PATH=$PATH:$PWD/rock/bin:$PWD/sam
    export OOC_LIBS=$PWD
    git clone --depth=1 -b master git://github.com/cogneco/rock.git
    git clone --depth=1 git://github.com/cogneco/sam.git
    (cd rock && make rescue && make && cd extensions && make)
    (cd sam && rock -v)

# Setting Environment Variables

It is necessary to set the following environments variables for every terminal session used:

    #!/bin/bash
    export PATH=$PATH:$PWD/rock/bin:$PWD/sam
    export OOC_LIBS=$PWD
