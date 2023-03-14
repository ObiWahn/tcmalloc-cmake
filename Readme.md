# tcmalloc-cmake

This project adds external CMake build support to the
[TCMalloc](https://github.com/google/tcmalloc) project. TCMalloc itself relies
on the Bazel build system.

tcmalloc-cmake downloads TCMalloc from gitub if not provided with a path to a
source directory.

## Options

    - TCMALLOC_SOURCE_DIR - path to tcmalloc source directory
    - TCMALLOC_SYSTEM - include tcmalloc with (-system instead of -I)
    - TCMALLOC_TEST - build tests
    - TCMALLOC_PROTO - protobuf support (UNIMPLEMENTED)

## Dependencies

Missing dependencies will be downloaded from github. They are Missing if their
target does not exists and they can not be found via `find_package`.

Required dependencies:

    - abseil-cpp

Optional dependencies:

    - protobuf
    - google test / mock
    - google benchmark

## Design

This project strives to have the minimum amount of code. People need
to trust this code. If the code is too long it is too hard to read and
reason about, making it harder to trust.

This choice results in:

    - few options
    - new camke only as `FetchContent` is required
    - few checks (e.g. compatibility)

## PRs are Welcome

If you find that the latest version of TCMalloc can not be build anymore, then
you are welcome to provide a patch.
