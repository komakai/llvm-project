
# The LLVM Compiler Infrastructure

[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/llvm/llvm-project/badge)](https://securityscorecards.dev/viewer/?uri=github.com/llvm/llvm-project)
[![OpenSSF Best Practices](https://www.bestpractices.dev/projects/8273/badge)](https://www.bestpractices.dev/projects/8273)
[![libc++](https://github.com/llvm/llvm-project/actions/workflows/libcxx-build-and-test.yaml/badge.svg?branch=main&event=schedule)](https://github.com/llvm/llvm-project/actions/workflows/libcxx-build-and-test.yaml?query=event%3Aschedule)

## About this fork

This fork adds support for debugging an application running in a container from the container's host

* build LLDB client
```
mkdir llvm
cd llvm
git clone https://github.com/komakai/llvm-project.git
cd llvm-project
mkdir lldb-build
cd lldb-build
cmake -B . -G Ninja -DLLVM_ENABLE_PROJECTS="lldb" -DLLDB_EXPORT_ALL_SYMBOLS=1 -DCMAKE_BUILD_TYPE=Debug -DLLDB_ENABLE_CURSES=On -DLLDB_ENABLE_LIBEDIT=On ../llvm-project/llvm
ninja lldb
```
* build LLDB server for Android (make sure ANDROID_NDK_ROOT environment variable is set - NDK version 23.1.7779620 is known to work)
```
cd ..
mkdir android-lldb-server
cd android-lldb-server
cmake -B . -G Ninja -DLLVM_ENABLE_PROJECTS="lldb-server" -DLLDB_EXPORT_ALL_SYMBOLS=1 -DCMAKE_BUILD_TYPE=Debug -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK_ROOT/build/cmake/android.toolchain.cmake -DANDROID_ABI=arm64-v8a -DANDROID_PLATFORM=android-29 -DANDROID_ALLOW_UNDEFINED_SYMBOLS=On -DLLVM_HOST_TRIPLE=aarch64-unknown-linux-android -DCROSS_TOOLCHAIN_FLAGS_NATIVE='-DCMAKE_C_COMPILER=cc;-DCMAKE_CXX_COMPILER=c++' ../llvm-project/llvm
ninja lldb-server
```
* Install LLDB server and start listening
```
# from the llvm/lldb-build folder
cd bin
./lldb
(lldb) platform select remote-android
(lldb) platform connect connect://localhost:1234
(lldb) process attach --name {name of process to attach to} --waitfor
```
* Configure LLDB client to find symbolicated binaries and source
```
(lldb) settings append target.exec-search-paths {container source checkout directory}/out/target/product/generic_arm64/symbols/system/bin
(lldb) settings append target.exec-search-paths {container source checkout directory}/out/target/product/generic_arm64/symbols/system/lib64
(lldb) settings set target.source-map "" "{container source checkout directory}"`
```
* Launch Process to debug or start a new container if the process to debug is automatically started by the container

Welcome to the LLVM project!

This repository contains the source code for LLVM, a toolkit for the
construction of highly optimized compilers, optimizers, and run-time
environments.

The LLVM project has multiple components. The core of the project is
itself called "LLVM". This contains all of the tools, libraries, and header
files needed to process intermediate representations and convert them into
object files. Tools include an assembler, disassembler, bitcode analyzer, and
bitcode optimizer.

C-like languages use the [Clang](https://clang.llvm.org/) frontend. This
component compiles C, C++, Objective-C, and Objective-C++ code into LLVM bitcode
-- and from there into object files, using LLVM.

Other components include:
the [libc++ C++ standard library](https://libcxx.llvm.org),
the [LLD linker](https://lld.llvm.org), and more.

## Getting the Source Code and Building LLVM

Consult the
[Getting Started with LLVM](https://llvm.org/docs/GettingStarted.html#getting-the-source-code-and-building-llvm)
page for information on building and running LLVM.

For information on how to contribute to the LLVM project, please take a look at
the [Contributing to LLVM](https://llvm.org/docs/Contributing.html) guide.

## Getting in touch

Join the [LLVM Discourse forums](https://discourse.llvm.org/), [Discord
chat](https://discord.gg/xS7Z362),
[LLVM Office Hours](https://llvm.org/docs/GettingInvolved.html#office-hours) or
[Regular sync-ups](https://llvm.org/docs/GettingInvolved.html#online-sync-ups).

The LLVM project has adopted a [code of conduct](https://llvm.org/docs/CodeOfConduct.html) for
participants to all modes of communication within the project.

