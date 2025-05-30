# Copyright (c) 2019, QuantStack and Mamba Contributors
#
# Distributed under the terms of the BSD 3-Clause License.
#
# The full license is in the file LICENSE, distributed with this software.
cmake_minimum_required(VERSION 3.18.2)

cmake_policy(SET CMP0025 NEW)
cmake_policy(SET CMP0077 NEW)
cmake_policy(SET CMP0057 NEW)

project(libmambapy)

if(NOT TARGET libmamba)
    find_package(libmamba REQUIRED)
endif ()

find_package(Python COMPONENTS Interpreter Development)
find_package(pybind11 REQUIRED)

pybind11_add_module(bindings
    src/main.cpp
    longpath.manifest
)

mamba_target_add_compile_warnings(bindings WARNING_AS_ERROR ${MAMBA_WARNING_AS_ERROR})

target_link_libraries(bindings PUBLIC pybind11::pybind11 libmamba)
set_property(TARGET bindings PROPERTY CXX_STANDARD 17)

install(TARGETS bindings
        LIBRARY DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/libmambapy/)
