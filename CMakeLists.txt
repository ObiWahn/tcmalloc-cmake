project(tcmalloc-cmake LANGUAGES CXX)
cmake_minimum_required(VERSION 3.25)

set(TCMALLOC_SRC ${CMAKE_CURRENT_LIST_DIR}/tcmalloc)
if (EXISTS ${TCMALLOC_SRC})
    # extra code path for maintainer
    # This path allows to test the cmake bindings without downloading the source from github.
    set(TCMALLOC_SRC "${TCMALLOC_SRC}")
    file(COPY ${CMAKE_CURRENT_LIST_DIR}/tcmalloc.cmake DESTINATION ${TCMALLOC_SRC})
    file(RENAME ${TCMALLOC_SRC}/tcmalloc.cmake ${TCMALLOC_SRC}/CMakeLists.txt)
    add_subdirectory(${TCMALLOC_SRC})
else()
    include(FetchContent)
    FetchContent_Declare(
        tcmalloc-git
        GIT_REPOSITORY   https://github.com/google/tcmalloc.git
        GIT_TAG          4e8a9fbbf5338b97c1aeaf9287c085bd939c155d
    )

    FetchContent_GetProperties(tcmalloc-git)
    if(NOT tcmalloc-git_POPULATED)
        FetchContent_Populate(tcmalloc-git)
    endif()

    file(COPY tcmalloc.cmake DESTINATION ${tcmalloc-git_SOURCE_DIR})
    file(RENAME ${tcmalloc-git_SOURCE_DIR}/tcmalloc.cmake ${tcmalloc-git_SOURCE_DIR}/CMakeLists.txt)
    message(STATUS "${tcmalloc-git_SOURCE_DIR} ${tcmalloc-git_BINARY_DIR}")
    add_subdirectory(${tcmalloc-git_SOURCE_DIR} ${tcmalloc-git_BINARY_DIR})
endif()
