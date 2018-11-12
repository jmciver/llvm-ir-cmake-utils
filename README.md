# LLVM IR cmake utilities

## Introduction

A collection of helper `cmake` functions/macros that eases the generation of `LLVM` IR and the application of various
`LLVM` `opt` passes while obtaining and preserving the separate IR files that are generated by each user-defined step.

## Requirements

-   [`cmake`][1] 3.0.0 or later
-   [`LLVM`][2] tools:
    -   Currently used:
        -   `clang/clang++`
        -   `opt`
        -   `llvm-dis` / `llvm-as`
        -   `llvm-link`
    -   Tested with:
        -   3.7 and later

## Installation

-   Clone this repo (or even add it as a submodule to your project).
-   In your `CMakeLists.txt` file `include(LLVMIRUtil.cmake)`.
-   You are good to go!

## Quick overview

The provided `cmake` commands are expected to work in a parasitic way to targets created via `add_executable()` and
`add_library`. The "gateway" command is `llvmir_attach_bc_target()` which generates the required bitcode files.
Currently, `C/C++` are supported via `clang/clang++`, but in theory any compiler which produces `LLVM` bitcode should be
easily supported (depending how nice it plays with `cmake` too).

The `cmake` calls currently provided are:

-   `llvmir_attach_bc_target()`
  Attaches to an existing target that can be compiled down to `LLVM IR` and does just that, using all the related flags
  and options from the main target. The existing supported targets make use of `clang/clang++`, so currently this means
  that the `C/C++` language is supported. It uses `add_custom_library()` `cmake` command under the hood. This creates a
  target of type `LLVMIR`.

-   `llvmir_attach_opt_pass_target()`
  Attaches to a target of type `LLVMIR` and applies various `opt` passes to its bitcode files, specified as arguments.
  It uses `add_custom_library()` `cmake` command under the hood. This creates a target of type `LLVMIR`.

-   `llvmir_attach_disassemble_target()`
  Attaches to a target of type `LLVMIR` and uses `llvm-dis` to disassemble its bitcode files. It uses
  `add_custom_library()` `cmake` command under the hood. This creates a target of type `LLVMIR`.

-   `llvmir_attach_assemble_target()`
  Attaches to a target of type `LLVMIR` and uses `llvm-as` to assemble its bitcode files. It uses `add_custom_library()`
  `cmake` command under the hood. This creates a target of type `LLVMIR`.

-   `llvmir_attach_link_target()`
  Attaches to a target of type `LLVMIR` and uses `llvm-link` to link its bitcode files to a single bitcode file. The
  output bitcode file is names after the target name. It uses `add_custom_library()` `cmake` command under the hood.
  This creates a target of type `LLVMIR`.

-   `llvmir_attach_library()`
  Attaches to a target of type `LLVMIR` and uses the appropriate compiler to compile its bitcode files to a native
  library. The output library name uses the target name according to platform rules. It uses `add_library()` `cmake`
  command under the hood. This creates a target of type `LLVMIR`.

-   `llvmir_attach_executable()`
  Attaches to a target of type `LLVMIR` and uses the appropriate compiler to compile its bitcode files to a native
  executable. The output library name uses the target name according to platform rules. It uses `add_executable()`
  `cmake` command under the hood. This creates a target of type `LLVMIR`.

### Influential properties

- `LLVMIR_SHORT_NAME`
  This property, if present, controls the output name for the calls that produce a single object (e.g. archive, library,
  etc.):
  - `llvmir_attach_link_target()`
  - `llvmir_attach_library()`
  - `llvmir_attach_executable()`


_CAUTION_

If you require to get raw _unoptimized_ `LLVM` IR, but with the ability to further optimize it later on and you are
compiling  with `LLVM` 5 or later, you need to add the following compile options, either:

```bash
-O1 -Xclang -disable-llvm-passes
```

or

```bash
-O0 -Xclang -disable-O0-optnone
```

This is because, since `LLVM` 5, using `-O0` add the `optnone` attribute to all functions.

## Basic Usage

Have a look and toy around with the included examples in this repo. The easiest way to start is:

1.  `git clone` this repo.
2.  Create a directory for an out-of-source build and `cd` into it.
3.  `CC=clang CXX=clang++ cmake [path to example source dir]`
4.  `cmake --build .`
5.  `cmake --build . --target help` to see available target and use them for bitcode generation.

[1]: https://cmake.org

[2]: www.llvm.org
