---
title: os
menu:
  main:
    parent: sdk
    weight: 50
author: Amos Wenger
copyright: 2009-2014 Amos Wenger
license: CC BY-SA 3.0
---

# The os package

The os packages contains various modules pertaining to Operating System functionality such as:

## I/O

 1. [Dynlib](dynlib), which deals with dynamic library loading

 2. [mmap](mmap), which exposes memory mapping capabilities

## System

 1. [System](system), to get information such as the number of processors,
    the hostname, etc.

 2. [Env](env), to deal with environment variables

 3. [ShellUtils](shellutils), used mostly to find executables

 4. [Time](time), to get the current time and date, and sleep
