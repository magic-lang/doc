---
title: system
menu:
  main:
    parent: os
    weight: 90
author: Amos Wenger
copyright: 2009-2014 Amos Wenger
license: CC BY-SA 3.0
---

# The os/System module

The `System` module allows to get information about the system, such
as the number of processors, as an `Int`:

    #!ooc
    cores := System numProcessors()

Or the hostname of the machine, as a `String`

    #!ooc
    hostname := System hostname()
