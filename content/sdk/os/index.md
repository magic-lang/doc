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

## Concurrency

 1. [Process](process), to launch child processes

 2. [JobPool](jobpool), to easily control a pool of child processes

 3. [Coro](coro, which provides a basic coroutine implementation

## I/O

 1. [Terminal](terminal), used to control terminal text output (color, etc.)

 2. [Pipe](pipe), to open, read from, and write to pipes

 3. [Dynlib](dynlib), which deals with dynamic library loading

 4. [mmap](mmap), which exposes memory mapping capabilities

 5. [Channel](channel), which implements channels

## System

 1. [System](system), to get information such as the number of processors,
    the hostname, etc.

 2. [Env](env), to deal with environment variables

 3. [ShellUtils](shellutils), used mostly to find executables

 3. [Time](time), to get the current time and date, and sleep
