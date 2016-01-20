---
title: native
menu:
  main:
    parent: sdk
    weight: 90
author: Amos Wenger
copyright: 2009-2014 Amos Wenger
license: CC BY-SA 3.0
---


# The native package

The `native` package of the SDK contains nothing that should be
directly imported in a cross-platform application. It's all platform-specific
support code for other parts of the SDK.

## Windows

For example, the `native/win32` package contains covers for types defined
in the Win32 API, along with a few error handling functions and other
things that are useful in the Win32 implementation of the SDK.

Note that there exists `native` sub packages elsewhere in the SDK, for example
in the `io` or in the `os` packages.
