# Copyright (c) 2019, QuantStack and Mamba Contributors
#
# Distributed under the terms of the BSD 3-Clause License.
#
# The full license is in the file LICENSE, distributed with this software.

cmake_minimum_required (VERSION 3.16)
cmake_policy(SET CMP0025 NEW) # Introduced in cmake 3.0
cmake_policy(SET CMP0077 NEW) # Introduced in cmake 3.13

project(mamba-package)

# Build options
# =============

if (BUILD_STATIC AND NOT BUILD_SHARED)
    set(MAMBA_PACKAGE_LINKAGE "STATIC" CACHE STRING "mamba-package linkage against libraries")
else ()
    set(MAMBA_PACKAGE_LINKAGE "DYNAMIC" CACHE STRING "mamba-package linkage against libraries")
endif ()

string(TOUPPER "${MAMBA_PACKAGE_LINKAGE}" linkage_upper)
message(STATUS "'mamba-package' linkage: ${MAMBA_PACKAGE_LINKAGE}")

# Source files
# ============

set(MAMBA_PACKAGE_SRCS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/main.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/package.cpp
)

set(MAMBA_PACKAGE_HEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/package.hpp
)

# Dependencies
# ============

if(NOT (TARGET libmamba OR TARGET libmamba-static))
    find_package(libmamba REQUIRED)
endif ()

# Build definition
# ================

add_executable(mamba-package ${MAMBA_PACKAGE_SRCS} ${MAMBA_PACKAGE_HEADERS})
mamba_target_add_compile_warnings(mamba-package WARNING_AS_ERROR ${MAMBA_WARNING_AS_ERROR})

if (${MAMBA_PACKAGE_LINKAGE} STREQUAL "STATIC")
    target_link_libraries(mamba-package PRIVATE libmamba-static)
else ()
    target_link_libraries(mamba-package PRIVATE libmamba)
endif ()

set_property(TARGET mamba-package PROPERTY CXX_STANDARD 17)

install(TARGETS mamba-package)
