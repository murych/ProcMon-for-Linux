cmake_minimum_required(VERSION 3.14)

# Set the project version number.
if(DEFINED ENV{VERSION})
  project(procmon VERSION $ENV{VERSION})
else()
  project(procmon VERSION 0.0.0)
endif()

# Collect all required libraries from system.
find_package(Curses REQUIRED)
find_package(SQLite3 REQUIRED)
find_package(Threads REQUIRED)
pkg_search_module(Bcc REQUIRED libbcc)
pkg_search_module(Bpf REQUIRED libbpf)

# Configure version header file to pass CMake settings to source code.
configure_file("${PROJECT_SOURCE_DIR}/src/version.h.in"
               "${PROJECT_BINARY_DIR}/version/version.h")

# Provide test target if requested.
option(BUILD_TESTING "Build included tests" OFF)
if(${BUILD_TESTING})
  find_package(Catch2 REQUIRED)
  enable_testing()
endif()

# Create a static library target for each subdirectory.
add_subdirectory(src/common)
add_subdirectory(src/configuration)
add_subdirectory(src/storage)
add_subdirectory(src/tracer)
add_subdirectory(src/display)
add_subdirectory(src/logging)

# Add exectable and link all static libraries.
add_executable(procmon src/procmon.cpp)
target_compile_features(procmon PUBLIC cxx_std_17)

# Why does the order which these static libraries are listed matter here??
target_link_libraries(procmon PRIVATE procmon::configuration procmon::logging)

# setup general packing Variables
set(CPACK_STRIP_FILES ON)
set(CPACK_PACKAGE_NAME ${PROJECT_NAME})
set(CPACK_PACKAGE_VENDOR "Microsoft")
set(CPACK_PACKAGE_CONTACT
    "OSS Tooling Dev Team OSSToolingDevTeam@service.microsoft.com")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Procmon for Linux")
set(CPACK_PACKAGE_DESCRIPTION
    "Procmon is a Linux reimagining of the classic Procmon tool from the Sysinternals suite of tools for Windows.  Procmon provides a convenient and efficient way for Linux developers to trace the syscall activity on the system."
)
set(CPACK_PACKAGE_VERSION_MAJOR "${procmon_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${procmon_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${procmon_VERSION_PATCH}")
set(AZDO_BUILD_ID "999999")

# setup license and readme for package
set(CPACK_RESOURCE_FILE_LICENSE ${PROJECT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README ${PROJECT_SOURCE_DIR}/README.md)

# setup CPACK RPM Variables
set(CPACK_RPM_PACKAGE_DESCRIPTION ${CPACK_PACKAGE_DESCRIPTION})
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_URL "https://github.com/Microsoft/Procmon-for-Linux")
set(CPACK_RPM_PACKAGE_GROUP "Development/Tools")

# setup CPACK DEB Variables
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION ${CPACK_PACKAGE_DESCRIPTION})
set(CPACK_DEBIAN_PACKAGE_SECTION "devel")
set(CPACK_DEBIAN_PACKAGE_HOMEPAGE
    "https://github.com/Microsoft/Procmon-for-Linux")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER
    "OSS Tooling Dev Team OSSToolingDevTeam@service.microsoft.com")

# setup install
install(TARGETS procmon DESTINATION /usr/bin)

# are we running on centos or ubuntu build agent?
execute_process(
  COMMAND lsb_release -si
  OUTPUT_VARIABLE distro
  OUTPUT_STRIP_TRAILING_WHITESPACE)

if(distro STREQUAL "CentOS")
  set(CPACK_GENERATOR "RPM")
  execute_process(
    COMMAND uname -m
    OUTPUT_VARIABLE CPACK_RPM_PACKAGE_ARCHITECTURE
    OUTPUT_STRIP_TRAILING_WHITESPACE)
  set(CPACK_PACKAGE_FILE_NAME
      ${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}-${AZDO_BUILD_ID}.${CPACK_RPM_PACKAGE_ARCHITECTURE}
  )

elseif(distro STREQUAL "Ubuntu")
  # get distro name
  execute_process(
    COMMAND lsb_release -sc
    OUTPUT_VARIABLE distro_name
    OUTPUT_STRIP_TRAILING_WHITESPACE)

  # we are building a debian package
  set(CPACK_GENERATOR "DEB")

  # install manpage file
  install(FILES procmon.1 DESTINATION /usr/share/man/man1/)

  # check what version of Ubuntu we are working with
  if(distro_name STREQUAL "bionic") # 18.04
    set(CPACK_DEBIAN_PACKAGE_DEPENDS
        "libc6 (>= 2.26), libstdc++6 (>= 8.4), libzstd1 (>= 1.3), libelf1 (>= 0.170), libncurses5 (>= 5.0), libtinfo5 (>= 6)"
    )

  elseif(distro_name STREQUAL "focal") # 20.04
    set(CPACK_DEBIAN_PACKAGE_DEPENDS
        "libc6 (>= 2.31), libstdc++6 (>= 10.2), libzstd1 (>= 1.4), libelf1 (>= 0.176), libncurses6 (>= 6.2), libtinfo6 (>= 6)"
    )
  endif()

  execute_process(
    COMMAND dpkg --print-architecture
    OUTPUT_VARIABLE CPACK_DEBIAN_PACKAGE_ARCHITECTURE
    OUTPUT_STRIP_TRAILING_WHITESPACE)
  set(CPACK_PACKAGE_FILE_NAME
      ${CPACK_PACKAGE_NAME}_${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}-${AZDO_BUILD_ID}_${CPACK_DEBIAN_PACKAGE_ARCHITECTURE}
  )

endif()

include(CPack)
