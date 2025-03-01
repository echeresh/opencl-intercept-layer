# Copyright (c) 2018-2022 Intel Corporation
#
# SPDX-License-Identifier: MIT

# This uses modules from: https://github.com/rpavlik/cmake-modules
# to get Git revision information and put it in the generated files:
#   git_version.h - version information for cliloader
configure_file(git_version.h.in "${CMAKE_CURRENT_BINARY_DIR}/git_version.h" @ONLY)

set( CLILOADER_SOURCE_FILES
    cliloader.cpp
    printcontrols.h
    "${CMAKE_CURRENT_BINARY_DIR}/git_version.h"
)
source_group( Source FILES
    ${CLILOADER_SOURCE_FILES}
)

add_executable(cliloader
    ${CLILOADER_SOURCE_FILES}
)
add_dependencies(cliloader OpenCL)
target_include_directories(cliloader PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../intercept)
target_include_directories(cliloader PRIVATE ${CMAKE_CURRENT_BINARY_DIR})

if (${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
    foreach( OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES} )
        install(TARGETS cliloader DESTINATION ${OUTPUTCONFIG} CONFIGURATIONS ${OUTPUTCONFIG})
    endforeach( OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES )
else()
    include(GNUInstallDirs)
    target_compile_definitions(cliloader PRIVATE CLILOADER_LIB_DIR="${CMAKE_INSTALL_LIBDIR}")
    install(TARGETS cliloader DESTINATION ${CMAKE_INSTALL_BINDIR})
endif()
