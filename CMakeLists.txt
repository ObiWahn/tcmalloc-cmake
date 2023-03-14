cmake_minimum_required(VERSION 3.25)
project(tcmalloc-cmake VERSION 0.0.1 LANGUAGES CXX)

if(TARGET tcmalloc OR TARGET tcmalloc::tcmalloc)
    return()
endif()

## configuration

set(TCMALLOC_SYSTEM "suppress warnings from this lib when included" ON)
set(TCMALLOC_PROTO "build with protobuf support" ON)
set(TCMALLOC_TEST "build tests" ON)


## dependency resolution
include(<F2>FetchContent)

FetchContent_Declare(
    abseil
    GIT_REPOSITORY   https://github.com/abseil/abseil-cpp.git
    GIT_TAG          6db185d8e2ba93adf32d689fd702ee0465b2e5ec
    OVERRIDE_FIND_PACKAGE
)

FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        58d77fa8070e8cec2dc1ed015d66b454c8d78850 # v1.12.1
    OVERRIDE_FIND_PACKAGE
)

FetchContent_Declare(
    benchmark
    GIT_REPOSITORY https://github.com/google/benchmark.git
    GIT_TAG        d572f4777349d43653b21d6c2fc63020ab326db2 # v.1.71
    OVERRIDE_FIND_PACKAGE
)

# TODO: fetch protobuf and protoc
set(__tcmalloc_proto OFF)

if(NOT TARGET absl::base)
    set(ABSL_PROPAGATE_CXX_STD ON)
    find_package(abseil)
endif()

# TODO: enable tests
# Are tests dependend on protobuf?
if (TCMALLOC_TEST)
    if(NOT TARGET googletest)
        find_package(googletest)
    endif()

    if(TARGET googletest)
        if(NOT TARGET benchmark)
            find_package(benchmark)
        endif()
    endif()

    if(TARGET googletest) # AND TARGET benchmark AND TARGET gmock -- ಠ_ಠ
        if(TARGET benchmark)
            if(TARGET gmock)
                set(__tcmalloc_test ON)
            endif()
        endif()
    endif()
endif()

## adding tcmalloc sub_directory
if(TCMALLOC_SYSTEM)
    set(__tcmalloc_system SYSTEM)
endif()

if (NOT TCMALLOC_SOURCE_DIR)
    set(TCMALLOC_SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/tcmalloc CACHE FILEPATH "local directory that contains tcmalloc")
endif()

if (EXISTS ${TCMALLOC_SOURCE_DIR})
    file(COPY tcmalloc.cmake DESTINATION ${TCMALLOC_SOURCE_DIR})
    file(COPY tcmalloc_files.cmake DESTINATION ${TCMALLOC_SOURCE_DIR})
    file(RENAME ${TCMALLOC_SOURCE_DIR}/tcmalloc.cmake ${TCMALLOC_SOURCE_DIR}/CMakeLists.txt)

    add_subdirectory(${TCMALLOC_SOURCE_DIR} ${TCMALLOC_BINARY_DIR} ${__tcmalloc_system})
else()
    FetchContent_Declare(
        tcmalloc-git
        GIT_REPOSITORY   https://github.com/google/tcmalloc.git
        GIT_TAG          4e8a9fbbf5338b97c1aeaf9287c085bd939c155d
    )

    FetchContent_GetProperties(absl-git)
    FetchContent_GetProperties(tcmalloc-git)
    if(NOT tcmalloc-git_POPULATED)
        FetchContent_Populate(tcmalloc-git)
    endif()

    file(COPY tcmalloc.cmake DESTINATION ${tcmalloc-git_SOURCE_DIR})
    file(COPY tcmalloc_files.cmake DESTINATION ${tcmalloc-git_SOURCE_DIR})
    file(RENAME ${tcmalloc-git_SOURCE_DIR}/tcmalloc.cmake ${tcmalloc-git_SOURCE_DIR}/CMakeLists.txt)

    add_subdirectory(${tcmalloc-git_SOURCE_DIR} ${tcmalloc-git_BINARY_DIR} ${__tcmalloc_system})
endif()
