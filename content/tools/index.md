---
title: Tools
menu:
  main:
    weight: 50
author: Amos Wenger
copyright: 2009-2014 Amos Wenger
license: CC BY-SA 3.0
---

# Frequently used ooc tools

There are some tools you should know about when you start looking into ooc.

 1. [Rock](rock) is the ooc compiler. In most cases,
   you will use it to generate C sources and executables from your ooc sources.

 2. [Sam](sam) helps you to keep all your libraries together and up-to-date.
   It's also able to resolve dependencies without any hassle.

 3. [Editors](editors) - describes how to get syntax highlighting,
   program checking, indentation in vim, emacs, TextMate?

Tools like autoconf, automake, even CMake, are irrelevant in the context of
ooc, since rock drives the whole compilation process.

ooc libraries have one, standardized way to be built - as such, the ooc ecosystem
is a lot more homogenous than, say, the C ecosystem. Complexity is dealt with
through [use files](rock/usefiles) and the such.

Still, the ooc toolchain is quite flexible, working on major desktop OSes, and
more constrained platform such as Android.
