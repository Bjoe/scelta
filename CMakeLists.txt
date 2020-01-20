# Copyright (c) 2017 Vittorio Romeo
# MIT License |  https://opensource.org/licenses/MIT
# http://vittorioromeo.info | vittorio.romeo@outlook.com

cmake_minimum_required(VERSION 3.8)

# Basic project setup.
project(scelta VERSION 0.1)

option(SCELTA_ENABLE_TESTING  "Enable unit tests" OFF)
option(SCELTA_ENABLE_EXAMPLES "Enable examples" OFF)

# Other compiler flags.
string(APPEND CMAKE_CXX_FLAGS
    " -Weverything"
    " -Wno-c++98-compat"
    " -Wno-c++98-compat-pedantic"
    " -Wno-missing-prototypes"
    " -Wno-newline-eof"
    " -Wno-reserved-id-macro"
    " -Wno-exit-time-destructors"
    " -Wno-global-constructors"
    " -Wno-missing-variable-declarations"
    " -Wno-header-hygiene"
    " -Wno-conversion"
    " -Wno-float-equal"
    " -Wno-old-style-cast"
    " -Wno-unused-macros"
    " -Wno-class-varargs"
    " -Wno-padded"
    " -Wno-weak-vtables"
    " -Wno-date-time"
    " -Wno-unneeded-member-function"
    " -Wno-covered-switch-default"
    " -Wno-range-loop-analysis"
    " -Wno-unused-member-function"
    " -Wno-switch-enum"
    " -Wno-double-promotion"
    " -Wno-injected-class-name"
    " -Wno-gnu-statement-expression"
    " -Wno-unused-lambda-capture"
    " -Wno-documentation-unknown-command"
    " -Wsuggest-attribute=pure"
    " -Wsuggest-attribute=const"
    " -Wsuggest-attribute=noreturn"
    " -Wsuggest-attribute=format"
)

add_library(headers INTERFACE)

add_subdirectory(include)

target_include_directories(headers INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/>
    $<INSTALL_INTERFACE:include>)

target_compile_features(headers INTERFACE cxx_std_17)

# Introduce variables:
# * CMAKE_INSTALL_LIBDIR
# * CMAKE_INSTALL_BINDIR
# * CMAKE_INSTALL_INCLUDEDIR
include(GNUInstallDirs)

set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

install(TARGETS headers
  EXPORT "${TARGETS_EXPORT_NAME}"
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(FILES include/scelta.hpp DESTINATION include)
install(DIRECTORY include/scelta DESTINATION include)

include(CMakePackageConfigHelpers)

# Use:
# * PROJECT_VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Use:
# * TARGETS_EXPORT_NAME
# * PROJECT_NAME
configure_package_config_file(
    "${CMAKE_SOURCE_DIR}/cmake/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${TARGETS_EXPORT_NAME}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)

if(SCELTA_ENABLE_TESTING)
    enable_testing()
    # Setup subdirectories.
    add_subdirectory(test)
endif()
if(SCELTA_ENABLE_EXAMPLES)
    add_subdirectory(example)
endif()

