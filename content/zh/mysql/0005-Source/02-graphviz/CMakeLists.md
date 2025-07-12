---
title: CMakeLists
description: CMakeLists
date: 2023-11-17
weight: 300000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}



{{<alert>}}



{{</alert>}}


## CMakeLists


```viz-dot
digraph "CMakeLists" {
node [
  fontsize = "12"

];
    "node1" [ label = "检测cmake版本", shape = octagon ];
    "node2" [ label = "WIN32", shape = octagon ];
    "node3" [ label = "APPLE", shape = octagon ];
    "node4" [ label = "UNIX", shape = octagon ];
    "node1" -> "node2"
    "node1" -> "node3"
    "node1" -> "node4"
    "node5" [ label = "版本小于3.5.1，则提示：find_program(MY_CMAKE3 cmake3 /bin /usr/bin /usr/local/bin)", shape = octagon ];
    "node4" -> "node5"
    "node6" [ label = "版本必须大于等于3.5.1", shape = octagon ];

    "node2" -> "node6"
    "node3" -> "node6"
    "node5" -> "node6"

    "node7" [ label = "检测Git", shape = octagon ];
    "node6" -> "node7"
    "node8" [ label = "配置：CMAKE_MODULE_PATH", shape = octagon ];
    "node7" -> "node8"
    "node9" [ label = "检查选项：CMAKE_BUILD_TYPE", shape = octagon ];
    "node8" -> "node9"
    "node10" [ label = "配置默认值：WITH_DEBUG=OFF", shape = octagon ];
    "node9" -> "node10"
    "node11" [ label = "配置默认值：CHECK_ERRMSG_FORMAT=OFF", shape = octagon ];
    "node10" -> "node11"
    "node12" [ label = "配置：MANUFACTURER", shape = octagon ];
    "node11" -> "node12"
    "node13" [ label = "配置：MAX_INDEXES", shape = octagon ];
    "node12" -> "node13"
    "node14" [ label = "配置WITH_DEBUG为RelWithDebInfo", shape = octagon ];
    "node13" -> "node14"
    "node15" [ label = "配置默认值：FORCE_UNSUPPORTED_COMPILER", shape = octagon ];
    "node14" -> "node15"
    "node16" [ label = "MY_HOST_SYSTEM_NAME=uname -s \n MY_HOST_MACHINE_NAME=uname -m \n MY_HOST_FILESYSTEM_NAME=rpm -qf /", shape = octagon ];
    "node15" -> "node16"
    "node17" [ label = "版本必须大于等于3.5.1", shape = octagon ];
    "node16" -> "node17"
    "node18" [ label = "临时保存：\n CMAKE_C_FLAGS \n CMAKE_CXX_FLAGS", shape = octagon ];
    "node17" -> "node18"
    "node19" [ label = "处理：\n MYSQL_PROJECT_NAME \n WITH_PLUGIN_NDBCLUSTER \n WITH_NDBCLUSTER \n WITH_NDB ", shape = octagon ];
    "node18" -> "node19"
    "node20" [ label = "include(mysql_version)", shape = octagon ];
    "node19" -> "node20"
    "node21" [ label = "设置项目信息：project", shape = octagon ];
    "node20" -> "node21"
    "node22" [ label = "处理：\n CMAKE_SOURCE_DIR \n CMAKE_BINARY_DIR \n ENV{PB2WORKDIR \n FORCE_INSOURCE_BUILD \n WITH_DEFAULT_COMPILER_OPTIONS=on \n INSTALL_STATIC_LIBRARIES=on}", shape = octagon ];
    "node21" -> "node22"
    "node23" [ label = "WITH_INNODB_MEMCACHED=OFF \n ENABLE_MEMCACHED_SASL =OFF \n ENABLE_MEMCACHED_SASL_PWDB=OFF \n ENV{CPPFLAGS} ", shape = octagon ];
    "node22" -> "node23"
    "node24" [ label = "include(CheckTypeSize) \n include(compile_flags) \n include(install_layout)", shape = octagon ];
    "node23" -> "node24"
    "node25" [ label = "配置默认值：WITH_BUILD_ID=on \n find_program(readelf)", shape = octagon ];
    "node24" -> "node25"
    "node26" [ label = "include(add_custom_target) \n include(pkg-config) \n include(character_sets) \n include(cmake_parse_arguments) ", shape = octagon ];
    "node25" -> "node26"

    "node27" [ label = "include(link_options)\n include(malloc_utils)\n include(cpu_info)\n include(fileutils)\n include(zlib)\n include(zstd)\n include(lz4)\n include(icu)", shape = octagon ];
    "node26" -> "node27"

    "node28" [ label = "include(libevent)\n include(ssl)\n include(sasl)\n include(ldap)\n include(kerberos)\n include(rpc)\n include(readline) ", shape = octagon ];
    "node27" -> "node28"

    "node29" [ label = "include(protobuf)\n include(package_name)\n include(libutils)\n include(plugin)\n include(component)\n include(install_macros) ", shape = octagon ];
    "node28" -> "node29"

    "node30" [ label = "include(mysql_add_executable)\n include(curl)\n include(rapidjson)\n include(fprofile)\n include(gloves)\n include(fido2)\n include(win_jemalloc)", shape = octagon ];
    "node29" -> "node30"

    "node31" [ label = "WITH_VALGRIND=OFF \n WITH_TCMALLOC=OFF \n WITH_TCMALLOC_DEBUG=OFF ", shape = octagon ];
    "node30" -> "node31"

    "node32" [ label = "WITH_JEMALLOC=OFF \n ENABLED_PROFILING=ON", shape = octagon ];
    "node31" -> "node32"

    "node33" [ label = "WITHOUT_SERVER", shape = octagon ];
    "node32" -> "node33"

    "node34" [ label = "WITH_INTERNAL", shape = octagon ];
    "node33" -> "node34"

    "node35" [ label = "WITH_AUTHENTICATION_LDAP", shape = octagon ];
    "node34" -> "node35"

    "node36" [ label = "WITH_AUTHENTICATION_KERBEROS", shape = octagon ];
    "node35" -> "node36"

    "node37" [ label = "WITH_AUTHENTICATION_CLIENT_PLUGINS", shape = octagon ];
    "node36" -> "node37"








}
```










## 8.0.32
```cmake
message(STATUS "Running cmake version ${CMAKE_VERSION}")

if(WIN32)
        # Load policies. This is needed in order to parse the wmic version check.
        cmake_minimum_required(VERSION 3.5.1)
        execute_process(
                COMMAND wmic os get version
                OUTPUT_VARIABLE NT_RELEASE_VERSION
                OUTPUT_STRIP_TRAILING_WHITESPACE
                RESULT_VARIABLE WMIC_RESULT)
        if(WMIC_RESULT EQUAL 0)
                string(REPLACE "\r" "" NT_RELEASE_VERSION "${NT_RELEASE_VERSION}")
                string(REPLACE "\n" "" NT_RELEASE_VERSION "${NT_RELEASE_VERSION}")
                string(REGEX MATCH "Version[ ]+([0-9\.]+)" V_NUM "${NT_RELEASE_VERSION}")
                message(STATUS "NT_RELEASE_VERSION is ${NT_RELEASE_VERSION}")
                if(CMAKE_MATCH_1)
                        if(CMAKE_MATCH_1 VERSION_LESS "10")
                                message(FATAL_ERROR "Need at least Windows Server 2016, or Windows 10, to build")
                        endif()
                endif()
        endif()
        if(CMAKE_GENERATOR MATCHES "Visual Studio" AND CMAKE_GENERATOR MATCHES "2019")
                if(CMAKE_VERSION MATCHES "MSVC")
                        # It is the bundled version, ignore version check, (although this seems to be buggy too).
                else()
                        # Bug in msbuild, install the latest in the 3.15 series as a workaround.
                        # https://gitlab.kitware.com/cmake/cmake/issues/19303 custom commands are re-built every time
                        cmake_minimum_required(VERSION 3.15.3)
                endif()
        endif()
elseif(APPLE)
        # Version 3.12.4 is needed because the new build system of Xcode is not supported by cmake. 3.12.4 will force using the
        # legacy build system. Version 3.9.2 is needed because INCLUDE_DIRECTORIES(SYSTEM ...) wasn't handled properly by the
        # cmake Xcode generator. NOTE: cmake >= 3.19 will use the new build system by default for Xcode >= 12.x
        if(CMAKE_GENERATOR STREQUAL "Xcode")
                set(APPLE_XCODE 1)
                cmake_minimum_required(VERSION 3.12.4)
        else()
                cmake_minimum_required(VERSION 3.9.2)
        endif()
        # If this is macOS 11, we need cmake 3.18 System libraries like /usr/lib/libresolv.dylib are no longer present in the
        # file system. cmake >= 3.18 will look for .tbd files in the SDK instead So we end up linking with:
        # /Applications/Xcode.app/.../usr/lib/libresolv.tbd We must postpone the version test until we have called 'uname -r'
        # below.
elseif(UNIX)
        # This is currently minimum version on all supported platforms.
        if(CMAKE_VERSION VERSION_LESS 3.5.1)
                # Default cmake is 2.8.12.2 on RedHat
                if(EXISTS "/etc/redhat-release")
                        message(WARNING "Please use cmake3 rather than cmake on this platform")
                        find_program(MY_CMAKE3 cmake3 /bin /usr/bin /usr/local/bin)
                        if(MY_CMAKE3)
                                message(STATUS "Found ${MY_CMAKE3}")
                        else()
                                message(STATUS "Please install cmake3 (yum install cmake3)")
                        endif()
                else()
                        # On SunOS /opt/csw/bin/cmake is (most likely) too old.
                        find_program(MY_UNAME uname /bin /usr/bin /usr/local/bin /sbin)
                        if(MY_UNAME)
                                exec_program(
                                        uname ARGS
                                        -s
                                        OUTPUT_VARIABLE MY_HOST_SYSTEM_NAME)
                                if(MY_HOST_SYSTEM_NAME MATCHES "SunOS")
                                        find_program(MY_CMAKE cmake /usr/bin NO_CMAKE_ENVIRONMENT_PATH
                                                     NO_SYSTEM_ENVIRONMENT_PATH)
                                        if(MY_CMAKE)
                                                message(STATUS "Found ${MY_CMAKE}")
                                                exec_program(${MY_CMAKE} ARGS --version)
                                        else()
                                                message(STATUS "Please install /usr/bin/cmake ")
                                        endif()
                                endif()
                        endif()
                endif()
        endif()
endif()


# CMake 3.5 is needed for TARGET_SOURCES(... $<TARGET_OBJECTS:${LIB}_objlib>)
cmake_minimum_required(VERSION 3.5.1)

# Will set GIT_EXECUTABLE and GIT_FOUND
find_package(Git)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

# First, decide about build type (debug or release) If cmake is invoked with -DCMAKE_BUILD_TYPE, respect user wishes and do not
# (re)define CMAKE_BUILD_TYPE. If WITH_DEBUG is given, set CMAKE_BUILD_TYPE = Debug. Otherwise, use Relwithdebinfo.

if(DEFINED CMAKE_BUILD_TYPE)
        set(HAVE_CMAKE_BUILD_TYPE TRUE)
endif()

option(WITH_DEBUG "Use dbug/safemutex" OFF)
option(CHECK_ERRMSG_FORMAT "Check printf format for English error messages" OFF)

# Use a default manufacturer if no manufacturer was identified.
set(MANUFACTURER_DOCSTRING "Set the entity that appears as the manufacturer of packages that support a manufacturer field.")
if(NOT DEFINED MANUFACTURER)
        set(MANUFACTURER
            "Built from Source"
            CACHE STRING ${MANUFACTURER_DOCSTRING})
        mark_as_advanced(MANUFACTURER)
endif()

# MAX_INDEXES - Set the maximum number of indexes per table, default 64U
if(NOT MAX_INDEXES)
        set(MAX_INDEXES 64U)
elseif(MAX_INDEXES MATCHES "^[0-9]+[Uu]?$")
        # MAX_INDEXES should be unsigned, so add the U suffix if it's missing.
        string(REGEX REPLACE "^([0-9]+).*$" "\\1U" MAX_INDEXES "${MAX_INDEXES}")
        message(STATUS "Configuring with MAX_INDEXES = ${MAX_INDEXES}")
else()
        message(FATAL_ERROR "MAX_INDEXES should be an unsigned integer.")
endif(NOT MAX_INDEXES)

if(MAX_INDEXES GREATER 255)
        message(FATAL_ERROR "MAX_INDEXES values greater than 255 is not supported!")
elseif(MAX_INDEXES LESS 64)
        # Per documentation, ignore values less than 64 and use the default instead.
        message(WARNING "MAX_INDEXES option ignored because it is less than 64.")
        set(MAX_INDEXES 64U)
endif()

# We choose to provide WITH_DEBUG as alias to standard CMAKE_BUILD_TYPE=Debug which turns out to be not trivial, as this
# involves synchronization between CMAKE_BUILD_TYPE and WITH_DEBUG. Besides, we have to deal with cases where WITH_DEBUG is
# reset from ON to OFF  and here we need to reset CMAKE_BUILD_TYPE to either none or default RelWithDebInfo

set(BUILDTYPE_DOCSTRING "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or
 CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel")

if(WITH_DEBUG)
        set(CMAKE_BUILD_TYPE
            "Debug"
            CACHE STRING ${BUILDTYPE_DOCSTRING} FORCE)
        set(OLD_WITH_DEBUG
            1
            CACHE INTERNAL "" FORCE)
elseif(NOT HAVE_CMAKE_BUILD_TYPE OR OLD_WITH_DEBUG)
        if(CMAKE_BUILD_TYPE MATCHES "Debug" OR NOT HAVE_CMAKE_BUILD_TYPE)
                set(CMAKE_BUILD_TYPE
                    "RelWithDebInfo"
                    CACHE STRING ${BUILDTYPE_DOCSTRING} FORCE)
        endif()
        set(OLD_WITH_DEBUG
            0
            CACHE INTERNAL "" FORCE)
endif()

string(TOUPPER "${CMAKE_BUILD_TYPE}" CMAKE_BUILD_TYPE_UPPER)

if(CMAKE_GENERATOR MATCHES "Visual Studio [1-9][0-9].*" AND CMAKE_GENERATOR_TOOLSET STREQUAL "")
        # Switch to 64 bit toolset on Windows (32 bit is default). This is recommended as the 32 bit linker will run into
        # address space issues and not exit for long time.
        set(CMAKE_GENERATOR_TOOLSET "host=x64")
endif()

# On Linux el6/el7 the default gcc is too old, see if devtoolset is installed. Same with SLES 12, look for gcc 7 there. We need
# to look for gcc before calling PROJECT below.
option(FORCE_UNSUPPORTED_COMPILER "Disable compiler version checks" OFF)
mark_as_advanced(WITHOUT_SERVER FORCE_UNSUPPORTED_COMPILER)

# Use 'uname -r' and 'rpm -qf /' to figure out host system. For Docker images we cannot trust uname, so use rpm instead.
if(UNIX)
        find_program(MY_UNAME uname /bin /usr/bin /usr/local/bin /sbin)
        if(MY_UNAME)
                execute_process(
                        COMMAND ${MY_UNAME} -s
                        OUTPUT_VARIABLE MY_HOST_SYSTEM_NAME
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        RESULT_VARIABLE MY_UNAME_RESULT)
                execute_process(
                        COMMAND ${MY_UNAME} -m
                        OUTPUT_VARIABLE MY_HOST_MACHINE_NAME
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        RESULT_VARIABLE MY_UNAME_MACHINE_RESULT)
                exec_program(
                        ${MY_UNAME} ARGS
                        -r
                        OUTPUT_VARIABLE MY_HOST_SYSTEM_VERSION)
        endif()
        find_program(MY_DPKG_BUILDFLAGS dpkg-buildflags /bin /usr/bin)
        find_program(MY_RPM rpm /bin /usr/bin)
        if(MY_RPM)
                execute_process(
                        COMMAND ${MY_RPM} -qf /
                        OUTPUT_VARIABLE MY_HOST_FILESYSTEM_NAME
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        RESULT_VARIABLE MY_RPM_RESULT)
        endif()
endif()

# See comments above, about CMAKE_MINIMUM_REQUIRED VERSION and macOS 11.
if(APPLE)
        if(MY_HOST_SYSTEM_VERSION VERSION_GREATER_EQUAL 20)
                cmake_minimum_required(VERSION 3.18)
        endif()
endif()

# Add all policies *after* CMAKE_MINIMUM_REQUIRED Repeating CMAKE_MINIMUM_REQUIRED here will load defaults for 3.5.1
cmake_minimum_required(VERSION 3.5.1)
include(cmake_policies NO_POLICY_SCOPE)

macro(STRING_APPEND STRING_VAR INPUT)
        set(${STRING_VAR} "${${STRING_VAR}}${INPUT}")
endmacro()

macro(STRING_PREPEND STRING_VAR INPUT)
        set(${STRING_VAR} "${INPUT}${${STRING_VAR}}")
endmacro()

if(MY_HOST_SYSTEM_NAME MATCHES "SunOS")
        set(SOLARIS 1)
endif()

if(MY_HOST_SYSTEM_NAME MATCHES "Linux")
        # Trust 'rpm -qf /' rather than 'uname -s'
        string(REGEX MATCH "\\.el([6789])\\." MATCH_FSYS "${MY_HOST_FILESYSTEM_NAME}")

        # Set LINUX_RHEL6, LINUX_RHEL7, LINUX_RHEL8 or LINUX_RHEL9
        if(CMAKE_MATCH_1)
                set(LINUX_RHEL 1)
                set(LINUX_RHEL${CMAKE_MATCH_1} 1)
        endif()
endif()

if(NOT LINUX_RHEL AND MY_HOST_SYSTEM_NAME MATCHES "Linux")
        if(EXISTS "/etc/os-release")
                file(READ "/etc/os-release" MY_OS_RELEASE)
                if(MY_OS_RELEASE MATCHES "Debian")
                        set(LINUX_DEBIAN 1)
                elseif(MY_OS_RELEASE MATCHES "Ubuntu")
                        set(LINUX_UBUNTU 1)
                        if(MY_OS_RELEASE MATCHES "16.04")
                                set(LINUX_UBUNTU_16_04 1)
                        elseif(MY_OS_RELEASE MATCHES "18.04")
                                set(LINUX_UBUNTU_18_04 1)
                        endif()
                        # /etc/os-release contains a line like VERSION_ID="20.04" Match the numeric value, including the dot,
                        # ignore the rest:
                        string(REGEX MATCH "VERSION_ID=\"([0-9\\.]+).*" UNUSED ${MY_OS_RELEASE})
                        if(CMAKE_MATCH_1)
                                set(LINUX_UBUNTU_VERSION_ID ${CMAKE_MATCH_1})
                        endif()
                endif()
        endif()
endif()

if(EXISTS "/etc/SuSE-release")
        set(LINUX_SUSE 1)
endif()

if(LINUX_SUSE)
        file(READ "/etc/SuSE-release" MY_OS_RELEASE)
        if(MY_OS_RELEASE MATCHES "SUSE Linux Enterprise Server 12")
                set(LINUX_SUSE_12 1)
        elseif(MY_OS_RELEASE MATCHES "SUSE Linux Enterprise Server 15" OR MY_OS_RELEASE MATCHES "openSUSE .* 15")
                set(LINUX_SUSE_15 1)
        else()
                message(WARNING "Unknown SUSE version.")
        endif()
endif()

# In case we pick up values form the CACHE or from the command line. See invokation of ADD_LINUX_RPM_FLAGS below.
set(INITIAL_CMAKE_C_FLAGS)
set(INITIAL_CMAKE_CXX_FLAGS)
if(DEFINED CMAKE_C_FLAGS)
        set(INITIAL_CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
endif()
if(DEFINED CMAKE_CXX_FLAGS)
        set(INITIAL_CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
endif()

if(CMAKE_HOST_UNIX
   AND NOT FORCE_UNSUPPORTED_COMPILER
   AND NOT CMAKE_C_COMPILER
   AND NOT CMAKE_CXX_COMPILER)
        # Cannot INCLUDE(CMakeDetermineSystem) prior to PROJECT initialization below.
        set(ENV_CC "$ENV{CC}")
        set(ENV_CXX "$ENV{CXX}")
        if(ENV_CC STREQUAL "" AND ENV_CXX STREQUAL "")
                if(LINUX_RHEL)
                        message(STATUS "This is ${MATCH_FSYS} as found from 'rpm -qf /'")
                endif()
                if(LINUX_RHEL AND NOT LINUX_RHEL9)
                        message(STATUS "Looking for a devtoolset compiler")
                        if(LINUX_RHEL6)
                                set(ALTERNATIVE_PATHS "/opt/rh/devtoolset-8")
                        elseif(LINUX_RHEL7)
                                # gcc11 not available yet
                                if(MY_HOST_MACHINE_NAME MATCHES "aarch64")
                                        set(ALTERNATIVE_PATHS "/opt/rh/devtoolset-10")
                                else()
                                        set(ALTERNATIVE_PATHS "/opt/rh/devtoolset-11")
                                endif()
                        elseif(LINUX_RHEL8)
                                set(ALTERNATIVE_PATHS "/opt/rh/gcc-toolset-11")
                        endif()

                        foreach(OPT_PATH ${ALTERNATIVE_PATHS})
                                find_program(ALTERNATIVE_GCC gcc NO_DEFAULT_PATH PATHS "${OPT_PATH}/root/usr/bin")
                                find_program(ALTERNATIVE_GPP g++ NO_DEFAULT_PATH PATHS "${OPT_PATH}/root/usr/bin")
                                find_program(ALTERNATIVE_LD ld NO_DEFAULT_PATH PATHS "${OPT_PATH}/root/usr/bin")
                                find_program(ALTERNATIVE_AR ar NO_DEFAULT_PATH PATHS "${OPT_PATH}/root/usr/bin")
                                find_program(ALTERNATIVE_RANLIB ranlib NO_DEFAULT_PATH PATHS "${OPT_PATH}/root/usr/bin")
                                find_program(ALTERNATIVE_ENABLE enable NO_DEFAULT_PATH PATHS "${OPT_PATH}")
                        endforeach()
                        # A missing ALTERNATIVE_LD may generate bad executables.
                        if(ALTERNATIVE_GCC
                           AND ALTERNATIVE_GPP
                           AND ALTERNATIVE_LD)
                                # Set correct search path for executables, libraries, and data files.
                                get_filename_component(GCC_B_PREFIX ${ALTERNATIVE_GCC} DIRECTORY)
                                string_prepend(CMAKE_C_FLAGS "-B${GCC_B_PREFIX} ")
                                get_filename_component(GPP_B_PREFIX ${ALTERNATIVE_GPP} DIRECTORY)
                                string_prepend(CMAKE_CXX_FLAGS "-B${GPP_B_PREFIX} ")

                                set(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
                                set(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
                                set(CMAKE_LINKER ${ALTERNATIVE_LD})
                                set(CMAKE_LINKER
                                    ${ALTERNATIVE_LD}
                                    CACHE PATH "Alternative ld")
                                if(ALTERNATIVE_AR)
                                        set(CMAKE_AR ${ALTERNATIVE_AR})
                                        set(CMAKE_AR
                                            ${ALTERNATIVE_AR}
                                            CACHE PATH "Alternative ar")
                                endif()
                                if(ALTERNATIVE_RANLIB)
                                        set(CMAKE_RANLIB ${ALTERNATIVE_RANLIB})
                                        set(CMAKE_RANLIB
                                            ${ALTERNATIVE_RANLIB}
                                            CACHE PATH "Alternative ranlib")
                                endif()
                                message(STATUS "Using ${ALTERNATIVE_GCC}")
                                message(STATUS "Using ${ALTERNATIVE_GPP}")
                        else()
                                if(LINUX_RHEL6)
                                        set(DEV_PACKAGES "devtoolset-8-gcc devtoolset-8-gcc-c++ devtoolset-8-binutils")
                                elseif(LINUX_RHEL7)
                                        if(MY_HOST_MACHINE_NAME MATCHES "aarch64")
                                                set(DEV_PACKAGES
                                                    "devtoolset-10-gcc devtoolset-10-gcc-c++ devtoolset-10-binutils")
                                        else()
                                                set(DEV_PACKAGES
                                                    "devtoolset-11-gcc devtoolset-11-gcc-c++ devtoolset-11-binutils")
                                        endif()
                                elseif(LINUX_RHEL8)
                                        set(DEV_PACKAGES "gcc-toolset-11-gcc gcc-toolset-11-gcc-c++ gcc-toolset-11-binutils")
                                endif()
                                message(WARNING "Could not find devtoolset compiler/linker in ${ALTERNATIVE_PATHS}")
                                message(WARNING "You need to install the required packages:\n" " yum install ${DEV_PACKAGES}\n")
                                message(FATAL_ERROR "Or you can set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
                        endif()

                elseif(LINUX_UBUNTU_18_04)
                        message(STATUS "Looking for gcc-8 on Ubuntu 18.04")
                        find_program(ALTERNATIVE_GCC gcc-8 NO_DEFAULT_PATH PATHS "/usr/bin")
                        find_program(ALTERNATIVE_GPP g++-8 NO_DEFAULT_PATH PATHS "/usr/bin")
                        find_program(GCC_AR_EXECUTABLE gcc-ar-8 NO_DEFAULT_PATH PATHS "/usr/bin")
                        find_program(GCC_RANLIB_EXECUTABLE gcc-ranlib-8 NO_DEFAULT_PATH PATHS "/usr/bin")
                        if(GCC_AR_EXECUTABLE)
                                set(CMAKE_AR ${GCC_AR_EXECUTABLE})
                                set(CMAKE_AR
                                    ${GCC_AR_EXECUTABLE}
                                    CACHE PATH "Alternative ar")
                        endif()
                        if(GCC_RANLIB_EXECUTABLE)
                                set(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE})
                                set(CMAKE_RANLIB
                                    ${GCC_RANLIB_EXECUTABLE}
                                    CACHE PATH "Alternative ranlib")
                        endif()
                        if(ALTERNATIVE_GCC AND ALTERNATIVE_GPP)
                                set(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
                                set(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
                                message(STATUS "Using ${ALTERNATIVE_GCC}")
                                message(STATUS "Using ${ALTERNATIVE_GPP}")
                        else()
                                message(WARNING "Could not find gcc-8")
                                message(FATAL_ERROR "Please do 'apt install gcc-8 g++-8'\n"
                                                    "or set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
                        endif()

                elseif(LINUX_SUSE_12 OR LINUX_SUSE_15)
                        message(STATUS "We need to look for a newer GCC on SUSE Linux.")
                        if(LINUX_SUSE_12)
                                find_program(ALTERNATIVE_GCC gcc-10 NO_DEFAULT_PATH PATHS "/usr/bin")
                                find_program(ALTERNATIVE_GPP g++-10 NO_DEFAULT_PATH PATHS "/usr/bin")
                                find_program(GCC_AR_EXECUTABLE gcc-ar-10 NO_DEFAULT_PATH PATHS "/usr/bin")
                                find_program(GCC_RANLIB_EXECUTABLE gcc-ranlib-10 NO_DEFAULT_PATH PATHS "/usr/bin")
                        elseif(LINUX_SUSE_15)
                                find_program(ALTERNATIVE_GCC gcc-9 NO_DEFAULT_PATH PATHS "/usr/bin")
                                find_program(ALTERNATIVE_GPP g++-9 NO_DEFAULT_PATH PATHS "/usr/bin")
                                find_program(GCC_AR_EXECUTABLE gcc-ar-9 NO_DEFAULT_PATH PATHS "/usr/bin")
                                find_program(GCC_RANLIB_EXECUTABLE gcc-ranlib-9 NO_DEFAULT_PATH PATHS "/usr/bin")
                        endif()
                        if(GCC_AR_EXECUTABLE)
                                set(CMAKE_AR ${GCC_AR_EXECUTABLE})
                                set(CMAKE_AR
                                    ${GCC_AR_EXECUTABLE}
                                    CACHE PATH "Alternative ar")
                        endif()
                        if(GCC_RANLIB_EXECUTABLE)
                                set(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE})
                                set(CMAKE_RANLIB
                                    ${GCC_RANLIB_EXECUTABLE}
                                    CACHE PATH "Alternative ranlib")
                        endif()
                        if(ALTERNATIVE_GCC AND ALTERNATIVE_GPP)
                                set(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
                                set(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
                                message(STATUS "Using ${ALTERNATIVE_GCC}")
                                message(STATUS "Using ${ALTERNATIVE_GPP}")
                                # Use the new ABI so that std::string can be used with allocators that are not
                                # default-constructible (e.g. Memroot_allocator)
                                if(LINUX_SUSE_12)
                                        add_definitions(-D_GLIBCXX_USE_CXX11_ABI=1)
                                endif()
                        else()
                                message(WARNING "Could not find newer gcc.")
                                if(LINUX_SUSE_12)
                                        message(FATAL_ERROR "Please do zypper install gcc10 gcc10-c++\n"
                                                            "or set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
                                else()
                                        message(FATAL_ERROR "Please do zypper install gcc9 gcc9-c++\n"
                                                            "or set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
                                endif()
                        endif()
                elseif(SOLARIS)
                        message(STATUS "Looking for GCC 10 on Solaris.")
                        find_program(ALTERNATIVE_GCC gcc NO_DEFAULT_PATH PATHS "/usr/gcc/10/bin")
                        find_program(ALTERNATIVE_GPP g++ NO_DEFAULT_PATH PATHS "/usr/gcc/10/bin")
                        if(ALTERNATIVE_GCC AND ALTERNATIVE_GPP)
                                set(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
                                set(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
                                message(STATUS "Using ${ALTERNATIVE_GCC}")
                                message(STATUS "Using ${ALTERNATIVE_GPP}")
                        else()
                                message(WARNING "Could not find /usr/gcc/10/bin/gcc")
                        endif()
                endif()
        endif()
endif()

# Optionally set project name, e.g. foo.xcodeproj (mac) or foo.sln (windows)
set(MYSQL_PROJECT_NAME_DOCSTRING "MySQL project name")
if(DEFINED MYSQL_PROJECT_NAME)
        set(MYSQL_PROJECT_NAME
            ${MYSQL_PROJECT_NAME}
            CACHE STRING ${MYSQL_PROJECT_NAME_DOCSTRING} FORCE)
else()
        set(MYSQL_PROJECT_NAME
            "MySQL"
            CACHE STRING ${MYSQL_PROJECT_NAME_DOCSTRING} FORCE)
        mark_as_advanced(MYSQL_PROJECT_NAME)
endif()

# Handle upgrade of old cmake cache
if(DEFINED WITH_PLUGIN_NDBCLUSTER)
        if(NOT WITH_NDBCLUSTER)
                if(WITH_PLUGIN_NDBCLUSTER OR WITH_NDBCLUSTER_STORAGE_ENGINE)
                        message(
                                FATAL_ERROR
                                        "Old broken config, WITH_NDBCLUSTER_STORAGE_ENGINE have changed "
                                        "meaning, to build MySQL Cluster please add -DWITH_NDB=ON and "
                                        "-UWITH_PLUGIN_NDBCLUSTER")
                endif()
        endif()
endif()

# Allow WITH_NDBCLUSTER, makes it possible to bisect between old and new source code and use cmake -DWITH_NDBCLUSTER=xx for
# both. If both WITH_NDB and WITH_NDBCLUSTER is set they must be equal. Else WITH_NDB is set as WITH_NDBCLUSTER. WITH_NDBCLUSTER
# is unset and not cached to not intefere with legacy logic in MYSQL_ADD_PLUGIN.
if(DEFINED WITH_NDBCLUSTER)
        set(HAVE_WITH_NDBCLUSTER 1)
        if(DEFINED WITH_NDB)
                # The extra NOTs are needed to normalize the boolean values else for example ON is not treated equal to 1.
                if(NOT (NOT WITH_NDB) EQUAL (NOT WITH_NDBCLUSTER))
                        message(FATAL_ERROR "WITH_NDB=${WITH_NDB} do not match WITH_NDBCLUSTER=${WITH_NDBCLUSTER}")
                endif()
                set(WITH_NDB_DEFAULT OFF)
        elseif(WITH_NDBCLUSTER)
                message(WARNING "WITH_NDBCLUSTER option is deprecated, please use "
                                "WITH_NDB instead. For backward compatibility WITH_NDBCLUSTER=ON sets " "WITH_NDB=ON")
                set(WITH_NDB_DEFAULT ON)
        else()
                set(WITH_NDB_DEFAULT OFF)
        endif()
        # Unset WITH_NDBCLUSTER to not intefere with MYSQL_ADD_PLUGIN logic. It will be added to cache at end of this file.
        unset(WITH_NDBCLUSTER CACHE)
else()
        set(WITH_NDB_DEFAULT OFF)
endif()

option(WITH_NDB "Build MySQL Cluster" ${WITH_NDB_DEFAULT})
if(WITH_NDB)
        message(STATUS "Building MySQL Cluster")
endif()

message(STATUS "CMAKE_MODULE_PATH is ${CMAKE_MODULE_PATH}")
include(mysql_version)
project(${MYSQL_PROJECT_NAME} VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION})

get_filename_component(REALPATH_CMAKE_SOURCE_DIR ${CMAKE_SOURCE_DIR} REALPATH)
get_filename_component(REALPATH_CMAKE_BINARY_DIR ${CMAKE_BINARY_DIR} REALPATH)

message(STATUS "Source directory ${REALPATH_CMAKE_SOURCE_DIR}")
message(STATUS "Binary directory ${REALPATH_CMAKE_BINARY_DIR}")

# IN PB2 we have a few configurations which are built in-source (e.g. doxygen).
if(DEFINED ENV{PB2WORKDIR})
        option(FORCE_INSOURCE_BUILD "Allow in-source build" ON)
else()
        option(FORCE_INSOURCE_BUILD "Allow in-source build" OFF)
endif()

# https://gitlab.kitware.com/cmake/community/wikis/FAQ cmake-does-not-generate-a-make-distclean-target-why Why disallow
# in-source build? Basically because 'make clean' or 'make distclean' do not work. So if you do a 'git pull' you may end up with
# a developer sandbox that no longer works as expected (CMakeCache.txt and other generated/configured files may contain data
# which is no longer valid)
set(THIS_IS_AN_IN_SOURCE_BUILD FALSE)
if(${REALPATH_CMAKE_SOURCE_DIR} STREQUAL ${REALPATH_CMAKE_BINARY_DIR})
        set(THIS_IS_AN_IN_SOURCE_BUILD TRUE)
        if(FORCE_INSOURCE_BUILD)
                message(WARNING "This is an in-source build")
        else()
                message(
                        FATAL_ERROR
                                "Please do not build in-source. "
                                "Out-of source builds are highly recommended: "
                                "you can have multiple builds for the same source, "
                                "and there is an easy way to do cleanup, "
                                "simply remove the build directory "
                                "(note that 'make clean' or 'make distclean' does *not* work) "
                                "\nYou *can* force in-source build by invoking cmake with -DFORCE_INSOURCE_BUILD=1")
        endif()
endif()

if(WIN32)
        string(LENGTH "${REALPATH_CMAKE_BINARY_DIR}" CMAKE_BINARY_DIR_LENGTH)
        if(CMAKE_BINARY_DIR_LENGTH LESS_EQUAL 3)
                message(FATAL_ERROR "It seems your build directory ${CMAKE_BINARY_DIR} is a filesystem root, "
                                    "this is not supported. Please build in a subdirectory.")
        endif()
endif()

macro(REPORT_CXX_FLAGS)
        message(STATUS "CMAKE_C_FLAGS: ${CMAKE_C_FLAGS}")
        foreach(BUILD_TYPE "" _DEBUG _RELWITHDEBINFO _RELEASE _MINSIZEREL)
                set(flag "CMAKE_CXX_FLAGS${BUILD_TYPE}")
                message(STATUS "${flag}: ${${flag}}")
        endforeach()
endmacro()

# Write content to file, using CONFIGURE_FILE The advantage compared to FILE(WRITE) is that timestamp does not change if file
# already has the same content
set(MYSQL_CMAKE_SCRIPT_DIR "${CMAKE_SOURCE_DIR}/cmake")
macro(CONFIGURE_FILE_CONTENT content file)
        set(CMAKE_CONFIGURABLE_FILE_CONTENT "${content}\n")
        configure_file(${MYSQL_CMAKE_SCRIPT_DIR}/configurable_file_content.in ${file} @ONLY)
endmacro()

set(BUILD_IS_SINGLE_CONFIG TRUE)
message(STATUS "CMAKE_GENERATOR: ${CMAKE_GENERATOR}")
if(CMAKE_GENERATOR MATCHES "Visual Studio" OR CMAKE_GENERATOR STREQUAL "Xcode")
        set(BUILD_IS_SINGLE_CONFIG FALSE)
endif()

if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
        set(MY_COMPILER_IS_CLANG 1)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
        set(MY_COMPILER_IS_GNU 1)
        if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS 9 AND NOT CMAKE_CXX_COMPILER_VERSION VERSION_LESS 8)
                set(MY_COMPILER_IS_GNU_8 1)
        endif()
elseif(CMAKE_CXX_COMPILER_ID MATCHES "SunPro")
        set(MY_COMPILER_IS_SUNPRO 1)
endif()

if(MY_COMPILER_IS_CLANG OR MY_COMPILER_IS_GNU)
        set(MY_COMPILER_IS_GNU_OR_CLANG 1)
endif()

# Maintainer mode is default on only for debug builds using GCC/G++
if(CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG" OR WITH_DEBUG)
        if(MY_COMPILER_IS_GNU)
                set(MYSQL_MAINTAINER_MODE
                    ON
                    CACHE BOOL "MySQL maintainer-specific development environment")
        endif()
endif()

option(WITH_DEFAULT_COMPILER_OPTIONS "Use flags from cmake/build_configurations/compiler_options.cmake" ON)
if(BUILD_CONFIG)
        include(${CMAKE_SOURCE_DIR}/cmake/build_configurations/${BUILD_CONFIG}.cmake)
endif()

option(INSTALL_STATIC_LIBRARIES "Install static libraries" ON)

# cmake on 64bit windows/mac/solaris doesn't set CMAKE_SYSTEM_PROCESSOR correctly
set(MYSQL_MACHINE_TYPE ${CMAKE_SYSTEM_PROCESSOR})

set(KNOWN_64BIT_ARCHITECTURES arm64 aarch64 ppc64 ppc64le s390x x86_64)

# Include the platform-specific file. To allow exceptions, this code looks for files in order of how specific they are. If there
# is, for example, a generic Linux.cmake and a version-specific Linux-2.6.28-11-generic, it will pick Linux-2.6.28-11-generic
# and include it. It is then up to the file writer to include the generic version if necessary.
foreach(_base ${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_VERSION}-${CMAKE_SYSTEM_PROCESSOR}
              ${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_VERSION} ${CMAKE_SYSTEM_NAME})
        set(_file ${CMAKE_SOURCE_DIR}/cmake/os/${_base}.cmake)
        if(EXISTS ${_file})
                include(${_file})
                break()
        endif()
endforeach()

if(UNIX)
        option(WITH_INNODB_MEMCACHED "" OFF)
        option(ENABLE_MEMCACHED_SASL "Enable SASL on InnoDB Memcached" OFF)
        option(ENABLE_MEMCACHED_SASL_PWDB "Enable SASL on InnoDB Memcached" OFF)
else()
        option(WITH_INNODB_MEMCACHED "" OFF)
endif()

# Following autotools tradition, add preprocessor definitions specified in environment variable CPPFLAGS
if(DEFINED ENV{CPPFLAGS})
        add_definitions($ENV{CPPFLAGS})
endif()

include(CheckTypeSize)
check_type_size("void *" SIZEOF_VOIDP)
message(STATUS "SIZEOF_VOIDP ${SIZEOF_VOIDP}")
# On some platforms, cmake may think that CMAKE_SIZEOF_VOID_P == 4 even if we have configured for 64bit build....
set(CMAKE_SIZEOF_VOID_P ${SIZEOF_VOIDP})

include(compile_flags)
include(install_layout)

# Add RPM or DEB platform flags for STANDALONE build. Not for DEBUG builds, since both platforms use -O2 and _FORTIFY_SOURCE.
# Not for Release or MinSizeRel, it will inflate binary sizes.
if(LINUX_STANDALONE AND (LINUX_DEB_PLATFORM OR LINUX_RPM_PLATFORM))
        if(CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG" OR WITH_DEBUG)
                set(WITH_PACKAGE_FLAGS_DEFAULT OFF)
        elseif(CMAKE_BUILD_TYPE_UPPER STREQUAL "RELWITHDEBINFO")
                set(WITH_PACKAGE_FLAGS_DEFAULT ON)
        else()
                set(WITH_PACKAGE_FLAGS_DEFAULT OFF)
        endif()
        option(WITH_PACKAGE_FLAGS "Use DEB/RPM compiler flags" ${WITH_PACKAGE_FLAGS_DEFAULT})
endif()

# Do not set package specific flags if language flags already provided in environment or on command line. And only for gcc (not
# clang).
if(MY_COMPILER_IS_GNU
   AND WITH_PACKAGE_FLAGS
   AND NOT INITIAL_CMAKE_C_FLAGS
   AND NOT INITIAL_CMAKE_CXX_FLAGS)
        if(LINUX_RPM_PLATFORM)
                if(MY_RPM)
                        add_linux_rpm_flags()
                else()
                        message(WARNING "rpm executable not found")
                endif()
        elseif(LINUX_DEB_PLATFORM)
                if(MY_DPKG_BUILDFLAGS)
                        add_linux_deb_flags()
                else()
                        message(WARNING "dpkg-buildflags executable not found")
                        message(WARNING "Please do 'apt install dpkg-dev'")
                endif()
        endif()
endif()

if(WITH_DEFAULT_COMPILER_OPTIONS)
        include(${CMAKE_SOURCE_DIR}/cmake/build_configurations/compiler_options.cmake)
endif()

# Assume, for now at least, that we want build-id for all kinds of Linux builds.
if(LINUX)
        option(WITH_BUILD_ID "Add --build-id=sha1 to all executables." ON)
        if(WITH_BUILD_ID)
                set(HAVE_BUILD_ID_SUPPORT 1)
                find_program(READELF_EXECUTABLE NAMES eu-readelf readelf)
                if(NOT READELF_EXECUTABLE)
                        message(WARNING "Cannot find eu-readelf or readelf")
                        message(FATAL_ERROR "Install the 'elfutils' package, or do cmake -DWITH_BUILD_ID=OFF")
                endif()
        endif()
endif()

include(CMakePushCheckState)

# Add macros
include(add_custom_target)
include(pkg-config)
include(character_sets)
include(cmake_parse_arguments)
include(link_options)
include(malloc_utils)
include(cpu_info)
include(fileutils)
include(zlib)
include(zstd)
include(lz4)
include(icu)
include(libevent)
include(ssl)
include(sasl)
include(ldap)
include(kerberos)
include(rpc)
include(readline)
include(protobuf)
include(package_name)
include(libutils)
include(plugin)
include(component)
include(install_macros)
include(mysql_add_executable)
include(curl)
include(rapidjson)
include(fprofile)
include(gloves)
include(fido2)
include(win_jemalloc)

if(UNIX)
        option(WITH_VALGRIND "Valgrind instrumentation" OFF)
endif()

option(WITH_TCMALLOC "Use tcmalloc rather than builtin malloc/free etc." OFF)
option(WITH_TCMALLOC_DEBUG "Use tcmalloc_debug rather than builtin malloc/free etc." OFF)
if(WITH_TCMALLOC OR WITH_TCMALLOC_DEBUG)
        # Note that libtcmalloc.so reports lots of Mismatched free()/delete/delete[] So for valgrind builds, link with
        # libtcmalloc_debug.so
        if(WITH_VALGRIND OR WITH_TCMALLOC_DEBUG)
                find_malloc_library(tcmalloc_debug)
        else()
                find_malloc_library(tcmalloc)
        endif()
endif()

option(WITH_JEMALLOC "Use jemalloc rather than builtin malloc/free etc." OFF)
if(WITH_JEMALLOC)
        find_malloc_library(jemalloc)
endif()

if(WITH_JEMALLOC AND (WITH_TCMALLOC OR WITH_TCMALLOC_DEBUG))
        message(FATAL_ERROR "Specify only *one* of WITH_TCMALLOC and WITH_JEMALLOC")
endif()

option(ENABLED_PROFILING "Enable profiling" ON)
option(WITHOUT_SERVER OFF)

if(WIN32)
        option(WITH_MSCRT_DEBUG "MS Visual Studio Debug CRT instrumentation" OFF)
endif()
if(NOT WITHOUT_SERVER)
        if(FPROFILE_GENERATE OR FPROFILE_USE)
                # Do not use data from unit testing when optimizing.
                option(WITH_UNIT_TESTS "Compile MySQL with unit tests" OFF)
        else()
                option(WITH_UNIT_TESTS "Compile MySQL with unit tests" ON)
        endif()
        option(WITH_ROUTER "Build MySQL Router" ON)
endif()

if(EXISTS ${CMAKE_SOURCE_DIR}/internal/CMakeLists.txt)
        set(WITH_INTERNAL_DEFAULT 1)
else()
        set(WITH_INTERNAL_DEFAULT 0)
endif()

if(NOT DEFINED WITH_INTERNAL)
        set(WITH_INTERNAL ${WITH_INTERNAL_DEFAULT})
endif()

# On windows we need a non-standard package for SASL.
if(WITH_INTERNAL AND (NOT WIN32 OR WITH_SASL))
        set(WITH_AUTHENTICATION_LDAP_DEFAULT ON)
else()
        set(WITH_AUTHENTICATION_LDAP_DEFAULT OFF)
endif()

option(WITH_AUTHENTICATION_LDAP "Report error if the LDAP authentication plugin cannot be built."
       ${WITH_AUTHENTICATION_LDAP_DEFAULT})

# Currently only supported on Linux
if(WITH_INTERNAL AND LINUX)
        set(WITH_AUTHENTICATION_KERBEROS_DEFAULT ON)
else()
        set(WITH_AUTHENTICATION_KERBEROS_DEFAULT OFF)
endif()

option(WITH_AUTHENTICATION_KERBEROS "Report error if the Kerberos authentication plugin cannot be built."
       ${WITH_AUTHENTICATION_KERBEROS_DEFAULT})

if(WITH_INTERNAL AND (NOT SOLARIS))
        set(WITH_AUTHENTICATION_FIDO_DEFAULT ON)
else()
        set(WITH_AUTHENTICATION_FIDO_DEFAULT OFF)
endif()

option(WITH_AUTHENTICATION_FIDO "Report error if the FIDO authentication plugin cannot be built."
       ${WITH_AUTHENTICATION_FIDO_DEFAULT})

# Default ON if we are building server-side plugins. Also default ON in pushbuild, for our community builds.
if(WITH_AUTHENTICATION_KERBEROS
   OR WITH_AUTHENTICATION_LDAP
   OR WITH_AUTHENTICATION_FIDO
   OR DEFINED ENV{PB2WORKDIR})
        set(WITH_AUTHENTICATION_CLIENT_PLUGINS_DEFAULT ON)
else()
        set(WITH_AUTHENTICATION_CLIENT_PLUGINS_DEFAULT OFF)
endif()

# Enable building of The KERBEROS client authentication plugin authentication_kerberos_client.so The LDAP client authentication
# plugin authentication_ldap_sasl_client.so The FIDO client authentication plugin authentication_fido_client.so
option(WITH_AUTHENTICATION_CLIENT_PLUGINS "Build client-side authentication plugins, even if server-side are disabled"
       ${WITH_AUTHENTICATION_CLIENT_PLUGINS_DEFAULT})

# On windows we need a non-standard package for CURL.
if(WITH_INTERNAL AND UNIX)
        set(WITH_CURL_DEFAULT "system")
else()
        set(WITH_CURL_DEFAULT "none")
endif()

option(WITH_LOCK_ORDER "Build the lock order mutex instrumentation code." OFF)

if(WITH_LOCK_ORDER)
        execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory lock_order WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})
endif()

if(DEFINED WITH_LTO_DEFAULT)
        set(WITH_LTO_DEFAULT ${WITH_LTO_DEFAULT})
else()
        set(WITH_LTO_DEFAULT OFF)
endif()

option(WITH_LTO "Enable the link-time optimizer. Currently works for gcc7/gcc8/gcc9/macos only." ${WITH_LTO_DEFAULT})

if(CMAKE_C_FLAGS MATCHES " -flto" OR CMAKE_CXX_FLAGS MATCHES " -flto")
        set(CMAKE_COMPILER_FLAG_WITH_LTO 1)
endif()

include(CheckCSourceCompiles)
include(CheckCXXSourceCompiles)
# We need some extra FAIL_REGEX patterns Note that CHECK_C_SOURCE_COMPILES is a misnomer, it will also link.
macro(MY_CHECK_C_COMPILER_FLAG FLAG RESULT)
        cmake_push_check_state()
        string_append(CMAKE_REQUIRED_FLAGS " ${FLAG}")
        check_c_source_compiles(
                "int main(void) { return 0; }"
                ${RESULT}
                FAIL_REGEX
                "unknown argument ignored"
                FAIL_REGEX
                "argument unused during compilation"
                FAIL_REGEX
                "unsupported .*option"
                FAIL_REGEX
                "unknown .*option"
                FAIL_REGEX
                "unrecognized .*option"
                FAIL_REGEX
                "ignoring unknown option"
                FAIL_REGEX
                "[Ww]arning: [Oo]ption"
                FAIL_REGEX
                "error: visibility"
                FAIL_REGEX
                "warning: visibility")
        cmake_pop_check_state()
endmacro()

macro(MY_CHECK_CXX_COMPILER_FLAG FLAG RESULT)
        cmake_push_check_state()
        string_append(CMAKE_REQUIRED_FLAGS " ${FLAG}")
        check_cxx_source_compiles(
                "int main(void) { return 0; }"
                ${RESULT}
                FAIL_REGEX
                "unknown argument ignored"
                FAIL_REGEX
                "argument unused during compilation"
                FAIL_REGEX
                "unsupported .*option"
                FAIL_REGEX
                "unknown .*option"
                FAIL_REGEX
                "unrecognized .*option"
                FAIL_REGEX
                "ignoring unknown option"
                FAIL_REGEX
                "[Ww]arning: [Oo]ption"
                FAIL_REGEX
                "error: visibility"
                FAIL_REGEX
                "warning: visibility")
        cmake_pop_check_state()
endmacro()

# Check whether a specific warning option is supported. Intended use is primarily to silence warnings from third-party code. If
# *our* code has warnings with gcc[56789] or clang[345678] the code should be fixed, rather than silencing the compiler
# warnings.
#
# Returns the corresponding "-Wno-<option>" if supported, 0 otherwise. WARNING_OPTION should be as found in clang/gcc
# documentation. Note: Sun Studio accepts/ignores anything starting with "-W". Visual Studio has a few /W options, but are
# totally different. Hence we check for gcc/clang only. No return value for functions in cmake, so we use an output argument.
#
# To only avoid failing on warning when compiled with -Werror and still show the warnings use
# SHOW_SUPPRESSED_COMPILER_WARNINGS=ON.
option(SHOW_SUPPRESSED_COMPILER_WARNINGS "Show suppressed compiler warnings, without failing with -Werror" OFF)
mark_as_advanced(SHOW_SUPPRESSED_COMPILER_WARNINGS)

function(MY_CHECK_CXX_COMPILER_WARNING WARNING_OPTION RETURN_VALUE)
        # Strip off possible leading -Wno- or -W
        string(REGEX REPLACE "^-Wno-" "" WARNING_OPTION ${WARNING_OPTION})
        string(REGEX REPLACE "^-W" "" WARNING_OPTION ${WARNING_OPTION})
        string(REGEX MATCH "^error=" ERROR_PREFIX ${WARNING_OPTION})
        # Use HAVE_CXX_W_warning_option as cache variable.
        string(REPLACE "-" "_" VAR_NAME ${WARNING_OPTION})
        string(REPLACE "=" "_" VAR_NAME ${VAR_NAME})
        string(REPLACE "c++" "cpp" VAR_NAME ${VAR_NAME})
        set(VAR_NAME "HAVE_CXX_W_${VAR_NAME}")
        if(MY_COMPILER_IS_GNU_OR_CLANG)
                if(SHOW_SUPPRESSED_COMPILER_WARNINGS AND NOT ERROR_PREFIX)
                        my_check_cxx_compiler_flag("-Werror=${WARNING_OPTION}" ${VAR_NAME})
                        if(${VAR_NAME})
                                set(WARNING_OPTION "error=${WARNING_OPTION}")
                        endif()
                endif()
                if(NOT ${VAR_NAME})
                        my_check_cxx_compiler_flag("-W${WARNING_OPTION}" ${VAR_NAME})
                endif()
        else()
                set(${VAR_NAME}
                    0
                    CACHE INTERNAL "-W${WARNING_OPTION} not supported")
        endif()
        if(${VAR_NAME})
                set(${RETURN_VALUE}
                    "-Wno-${WARNING_OPTION}"
                    PARENT_SCOPE)
        else()
                set(${RETURN_VALUE}
                    0
                    PARENT_SCOPE)
        endif()
endfunction()

# When builing with PGO, GCC will report -Wmissing-profile when compiling files for which it cannot find profile data. It is
# valid to disable this warning for files we are not currently interested in profiling.
macro(DISABLE_MISSING_PROFILE_WARNING)
        if(FPROFILE_USE)
                my_check_cxx_compiler_warning("-Wmissing-profile" HAS_WARN_FLAG)
                if(HAS_WARN_FLAG)
                        string_append(CMAKE_C_FLAGS " ${HAS_WARN_FLAG}")
                        string_append(CMAKE_CXX_FLAGS " ${HAS_WARN_FLAG}")
                endif()
        endif()
endmacro()

macro(MY_SANITIZER_CHECK SAN_OPT ADD_OPTIMIZATION RESULT)
        my_check_c_compiler_flag("${SAN_OPT}" C_RESULT)
        my_check_cxx_compiler_flag("${SAN_OPT}" CXX_RESULT)
        if(C_RESULT AND CXX_RESULT)
                string_append(CMAKE_C_FLAGS " ${SAN_OPT}")
                string_append(CMAKE_CXX_FLAGS " ${SAN_OPT}")
                # We switch on basic optimization also for debug builds. We disable inlining for stack trace readability. With
                # optimization we may get some warnings, so we switch off -Werror
                if(${ADD_OPTIMIZATION})
                        if(MSVC)
                                string_append(CMAKE_C_FLAGS " -O1 /Ob0")
                                string_append(CMAKE_CXX_FLAGS " -O1 /Ob0")
                        else()
                                string_append(CMAKE_C_FLAGS " -O1 -fno-inline")
                                string_append(CMAKE_CXX_FLAGS " -O1 -fno-inline")
                        endif()
                        if(NOT SANITIZE_MAINTAINER_MODE)
                                set(MYSQL_MAINTAINER_MODE
                                    OFF
                                    CACHE BOOL "MySQL maintainer-specific development environment" FORCE)
                        endif()
                endif()
                set(${RESULT} 1)
        else()
                set(${RESULT} 0)
        endif()
endmacro()

option(WITH_ASAN "Enable address sanitizer" OFF)
if(WITH_ASAN)
        if(WIN32_CLANG)
                # NOTE: We do _not_ modify CMAKE_C_FLAGS / CMAKE_CXX_FLAGS to add -fsanitize=address here yet; Clang (at least
                # of 5.0.0) requires /MT, but CMake adds /MDd to all feature checks almost no matter what we do. For the same
                # reason, we have to enable it unconditionalyl without doing a feature test; we know it's working. Modifying the
                # flags happens below, near the end.
                set(HAVE_ASAN 1)

                # Get the path to the ASAN support libraries.
                get_filename_component(ASAN_LIB_DIR ${CMAKE_CXX_COMPILER} DIRECTORY)
                set(ASAN_LIB_DIR "${ASAN_LIB_DIR}/../lib/clang/${CMAKE_CXX_COMPILER_VERSION}/lib/windows")
                get_filename_component(ASAN_LIB_DIR ${ASAN_LIB_DIR} REALPATH)
        else()
                my_sanitizer_check("-fsanitize=address" TRUE WITH_ASAN_OK)
                if(WITH_ASAN_OK)
                        option(WITH_ASAN_SCOPE "Enable -fsanitize-address-use-after-scope" ON)
                        set(HAVE_ASAN 1)
                        # This works with clang, but not gcc it seems.
                        my_check_cxx_compiler_flag("-fsanitize=address -fsanitize-address-use-after-scope" HAVE_SANITIZE_SCOPE)
                        if(WITH_ASAN_SCOPE AND HAVE_SANITIZE_SCOPE)
                                string_append(CMAKE_C_FLAGS " -fsanitize-address-use-after-scope")
                                string_append(CMAKE_CXX_FLAGS " -fsanitize-address-use-after-scope")
                        endif()
                else()
                        message(FATAL_ERROR "Do not know how to enable address sanitizer")
                endif()
        endif()
endif()

option(WITH_LSAN "Enable leak sanitizer" OFF)
if(WITH_LSAN)
        my_sanitizer_check("-fsanitize=leak" TRUE WITH_LSAN_OK)
        if(NOT WITH_LSAN_OK)
                message(FATAL_ERROR "Do not know how to enable leak sanitizer")
        else()
                set(HAVE_LSAN 1)
        endif()
endif()

option(WITH_MSAN "Enable memory sanitizer" OFF)
if(WITH_MSAN)
        my_sanitizer_check("-fsanitize=memory -fsanitize-memory-track-origins" TRUE WITH_MSAN_OK)
        if(NOT WITH_MSAN_OK)
                message(FATAL_ERROR "Do not know how to enable memory sanitizer")
        else()
                message(WARNING "Memory sanitizer support is currently experimental.")
        endif()
endif()

option(WITH_UBSAN "Enable undefined behavior sanitizer" OFF)
if(WITH_UBSAN)
        my_sanitizer_check("-fsanitize=undefined" TRUE WITH_UBSAN_OK)
        if(NOT WITH_UBSAN_OK)
                message(FATAL_ERROR "Do not know how to enable undefined behavior sanitizer")
        else()
                set(HAVE_UBSAN 1)
        endif()
endif()

option(WITH_TSAN "Enable thread sanitizer" OFF)
if(WITH_TSAN)
        my_sanitizer_check("-fsanitize=thread" TRUE WITH_TSAN_OK)
        if(NOT WITH_TSAN_OK)
                message(FATAL_ERROR "Do not know how to enable thread sanitizer")
        else()
                message(WARNING "Thread sanitizer support is currently experimental.")
                set(HAVE_TSAN 1)
        endif()
endif()

if(WITH_ASAN AND WITH_MSAN)
        message(FATAL_ERROR "Cannot use AddressSanitizer and MemorySanitizer together")
endif()

if(WITH_ASAN
   OR WITH_LSAN
   OR WITH_UBSAN)
        set(HANDLE_FATAL_SIGNALS_DEFAULT 0)
else()
        set(HANDLE_FATAL_SIGNALS_DEFAULT 1)
endif()
option(HANDLE_FATAL_SIGNALS "Handle fatal signals with internal signal handler" ${HANDLE_FATAL_SIGNALS_DEFAULT})

if(CMAKE_C_FLAGS MATCHES "-fsanitize" OR CMAKE_CXX_FLAGS MATCHES "-fsanitize")
        set(CMAKE_COMPILER_FLAG_WITH_SANITIZE 1)
endif()

# Older versions of ccache must be disabled: export CCACHE_DISABLE=1 See http://www.cmake.org/Wiki/CTest/Coverage
option(ENABLE_GCOV "Enable gcov (debug, Linux builds only)" OFF)
if(ENABLE_GCOV
   AND NOT WIN32
   AND NOT APPLE)
        string_append(CMAKE_C_FLAGS " -fprofile-arcs -ftest-coverage -DHAVE_GCOV")
        string_append(CMAKE_CXX_FLAGS " -fprofile-arcs -ftest-coverage -DHAVE_GCOV")
        string_append(CMAKE_EXE_LINKER_FLAGS " -fprofile-arcs -ftest-coverage -lgcov")
endif()

option(ENABLE_GPROF "Enable gprof (optimized, Linux builds only)" OFF)
if(ENABLE_GPROF
   AND NOT WIN32
   AND NOT APPLE)
        string_append(CMAKE_C_FLAGS " -pg")
        string_append(CMAKE_CXX_FLAGS " -pg")
        string_append(CMAKE_EXE_LINKER_FLAGS " -pg")
endif()

# Use lld for Clang if available and not explicitly disabled. Also works for gcc on Debian/Ubuntu. Do 'apt install lld'. LTO
# build fails with lld, so turn it off by default.
if(LINUX
   AND NOT WITH_LTO
   AND NOT CMAKE_COMPILER_FLAG_WITH_LTO)
        option(USE_LD_LLD "Use llvm lld linker" ON)
else()
        option(USE_LD_LLD "Use llvm lld linker" OFF)
endif()

if(USE_LD_LLD)
        cmake_push_check_state(RESET)

        # RPM builds on Fedora use -specs files. This may cause the test for -fuse-ld=lld to succeed, when it should fail.
        set(SAVE_CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
        set(SAVE_CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
        string(REGEX REPLACE "-specs=/usr/lib/rpm/redhat/.*" "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
        string(REGEX REPLACE "-specs=/usr/lib/rpm/redhat/.*" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})

        set(CMAKE_REQUIRED_LIBRARIES "-fuse-ld=lld")
        check_c_source_compiles("int main() {}" C_LD_LLD_RESULT)
        check_cxx_source_compiles("int main() {}" CXX_LD_LLD_RESULT)
        if(C_LD_LLD_RESULT AND CXX_LD_LLD_RESULT)
                foreach(flag CMAKE_C_LINK_FLAGS CMAKE_CXX_LINK_FLAGS CMAKE_EXE_LINKER_FLAGS CMAKE_MODULE_LINKER_FLAGS
                             CMAKE_SHARED_LINKER_FLAGS)
                        string_append(${flag} " -fuse-ld=lld")
                endforeach()
        endif()
        set(CMAKE_C_FLAGS ${SAVE_CMAKE_C_FLAGS})
        set(CMAKE_CXX_FLAGS ${SAVE_CMAKE_CXX_FLAGS})
        cmake_pop_check_state()
endif()

if(LINUX_STANDALONE)
        # ON by default in pushbuild, but only for standalone builds. The comression does not play well with some RPM/DEB
        # packaging. Developers can set default in environment. Compressing debug sections will shink binaries significantly,
        # esp for RelWithDebInfo builds, at the cost of some extra link time.
        if(DEFINED ENV{PB2WORKDIR}
           OR (DEFINED ENV{COMPRESS_DEBUG_SECTIONS}
               AND "$ENV{COMPRESS_DEBUG_SECTIONS}"
               AND NOT DEFINED (COMPRESS_DEBUG_SECTIONS)))
                set(COMPRESS_DEBUG_SECTIONS_DEFAULT ON)
        else()
                set(COMPRESS_DEBUG_SECTIONS_DEFAULT OFF)
        endif()

        option(COMPRESS_DEBUG_SECTIONS "Compress debug sections of mysqld and test executables"
               ${COMPRESS_DEBUG_SECTIONS_DEFAULT})
else()
        set(COMPRESS_DEBUG_SECTIONS OFF)
endif()

# Newer versions of gcc produce huge amounts of debug information with the default debug level.
#
# From 'man gcc': Level 1 produces minimal information, enough for making backtraces in parts of the program that you don't plan
# to debug.  This includes descriptions of functions and external variables, and line number tables, but no information about
# local variables.
#
# clang documentation says: -gline-tables-only, -g1, -gmlt Emit debug line number tables only
if(UNIX AND MY_COMPILER_IS_GNU_OR_CLANG)
        # By default, do this for STANDALONE and tarball builds. Off by default for sanitizer or valgrind builds. Off by default
        # for Apple Clang.
        if((INSTALL_LAYOUT MATCHES "STANDALONE"
            OR INSTALL_LAYOUT MATCHES "TARGZ"
            OR INSTALL_LAYOUT MATCHES "SVR4")
           AND NOT CMAKE_COMPILER_FLAG_WITH_SANITIZE
           AND NOT WITH_VALGRIND
           AND NOT APPLE)
                set(MINIMAL_RELWITHDEBINFO_DEFAULT ON)
        else()
                set(MINIMAL_RELWITHDEBINFO_DEFAULT OFF)
        endif()
        option(MINIMAL_RELWITHDEBINFO "Reduce debug information for gcc RelWithDebInfo builds"
               ${MINIMAL_RELWITHDEBINFO_DEFAULT})
        # Appending here will override any earlier -g options.
        if(MINIMAL_RELWITHDEBINFO)
                string_append(CMAKE_C_FLAGS_RELWITHDEBINFO " -g1")
                string_append(CMAKE_CXX_FLAGS_RELWITHDEBINFO " -g1")
        endif()
endif()

# Enable "-Og -fno-inline" for ordinary Debug builds. Note that this may optimize away local stack variables.
if(UNIX
   AND MY_COMPILER_IS_GNU_OR_CLANG
   AND NOT CMAKE_COMPILER_FLAG_WITH_SANITIZE
   AND NOT WITH_VALGRIND)
        # ON by default in pushbuild, to save time running Debug test suites. man clang: -Og Like -O1. In future versions, this
        # option might disable different optimizations in order to improve debuggability. man gcc: -Og Optimize debugging
        # experience. -Og should be the optimization level of choice for the standard edit-compile- debug cycle, offering a
        # reasonable level of optimization while maintaining fast compilation and a good debugging experience. It is a better
        # choice than -O0 for producing debuggable code because some compiler passes that collect debug information are disabled
        # at -O0.
        #
        # OFF by default if CFLAGS or CXXFLAGS are defined in the environment. CFLAGS/CXXFLAGS will be set e.g. for
        # LINUX_DEB_PLATFORM, and may contain other optimization flags, interfering with ours.
        if((NOT DEFINED ENV{CFLAGS}
            AND NOT DEFINED ENV{CXXFLAGS}
            AND DEFINED ENV{PB2WORKDIR})
           OR (DEFINED ENV{OPTIMIZE_DEBUG_BUILDS}
               AND "$ENV{OPTIMIZE_DEBUG_BUILDS}"
               AND NOT DEFINED (OPTIMIZE_DEBUG_BUILDS)))
                set(OPTIMIZE_DEBUG_BUILDS_DEFAULT ON)
        else()
                set(OPTIMIZE_DEBUG_BUILDS_DEFAULT OFF)
        endif()
        option(OPTIMIZE_DEBUG_BUILDS "Add -Og -fno-inline to CMAKE_<lang>_FLAGS_DEBUG" ${OPTIMIZE_DEBUG_BUILDS_DEFAULT})
        # Use STRING_PREPEND here, so command-line input can override our defaults.
        if(OPTIMIZE_DEBUG_BUILDS)
                string_prepend(CMAKE_C_FLAGS_DEBUG " -Og -fno-inline ")
                string_prepend(CMAKE_CXX_FLAGS_DEBUG " -Og -fno-inline ")
        endif()
endif()

# For aarch64 some sub-architectures support LSE atomics and some don't. Thus, compiling for the common denominator
# (-march=armv8-a) means LSE is not used. The -moutline-atomics switch enables run-time detection of LSE support. There are
# compilers (gcc 9.3.1 for example) which support this switch, but do not enable it by default, even though it seems to help.
# So, we force it.
if(CMAKE_SYSTEM_PROCESSOR STREQUAL "aarch64")
        my_check_cxx_compiler_flag("-moutline-atomics" HAVE_OUTLINE_ATOMICS)
        if(HAVE_OUTLINE_ATOMICS)
                string_append(CMAKE_C_FLAGS " -moutline-atomics")
                string_append(CMAKE_CXX_FLAGS " -moutline-atomics")
        endif()
endif()

if(LINUX)
        option(LINK_RANDOMIZE "Randomize the order of all symbols in the binary" OFF)
        set(LINK_RANDOMIZE_SEED
            "mysql"
            CACHE STRING "Seed to use for link randomization")
else()
        set(LINK_RANDOMIZE OFF)
endif()

if(CMAKE_C_LINK_FLAGS MATCHES "-fuse-ld=gold"
   OR CMAKE_CXX_LINK_FLAGS MATCHES "-fuse-ld=gold"
   OR CMAKE_EXE_LINKER_FLAGS MATCHES "-fuse-ld=gold")
        message(WARNING "-fuse-ld=gold is not supported")
        string(REPLACE "-fuse-ld=gold" "" CMAKE_C_LINK_FLAGS "${CMAKE_C_LINK_FLAGS}")
        string(REPLACE "-fuse-ld=gold" "" CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS}")
        string(REPLACE "-fuse-ld=gold" "" CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS}")
endif()

# Fedora 34, RHEL9: undefined reference to symbol 'crc32_z@@ZLIB_1.2.9'
if(LINUX_FEDORA_34 OR LINUX_RHEL9)
        if(NOT CMAKE_CXX_LINK_FLAGS MATCHES "-fuse-ld=lld")
                string_append(CMAKE_CXX_LINK_FLAGS " -Wl,--copy-dt-needed-entries")
        endif()
endif()

if(LINK_RANDOMIZE)
        string_append(CMAKE_C_FLAGS " -ffunction-sections -fdata-sections")
        string_append(CMAKE_CXX_FLAGS " -ffunction-sections -fdata-sections")

        set(CMAKE_C_LINK_FLAGS "${CMAKE_C_LINK_FLAGS} -Wl,--sort-section=name")
        set(CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS} -Wl,--sort-section=name")

        set(CMAKE_CXX_ARCHIVE_CREATE
            "sh -c '${CMAKE_SOURCE_DIR}/scripts/randomize-order.pl ${LINK_RANDOMIZE_SEED} <OBJECTS> && <CMAKE_AR> qc <TARGET> <LINK_FLAGS> <OBJECTS>'"
        )
        set(CMAKE_CXX_ARCHIVE_CREATE_APPEND
            "sh -c '${CMAKE_SOURCE_DIR}/scripts/randomize-order.pl ${LINK_RANDOMIZE_SEED} <OBJECTS> && <CMAKE_AR> q <TARGET> <LINK_FLAGS> <OBJECTS>'"
        )
        set(CMAKE_C_ARCHIVE_CREATE ${CMAKE_CXX_ARCHIVE_CREATE})
        set(CMAKE_C_ARCHIVE_CREATE_APPEND ${CMAKE_CXX_ARCHIVE_CREATE_APPEND})
endif()

# Gold and LLD support the --gdb-index option, which adds a .gdb_index section to the binaries. It makes loading the binaries in
# gdb much faster. The overhead of generating the index is small in LLD.
option(ADD_GDB_INDEX "Generate a .gdb_index section in the binaries." OFF)

if(ADD_GDB_INDEX AND CMAKE_EXE_LINKER_FLAGS MATCHES "-fuse-ld=lld")
        # -fuse-ld=lld is not enough to ensure that ld.lld will actually be used. On some platforms we need an extra check for
        # -Wl,--gdb-index
        cmake_push_check_state()
        set(CMAKE_REQUIRED_LIBRARIES "-fuse-ld=lld -Wl,--gdb-index")
        check_cxx_source_compiles("int main() {}" CXX_LD_GDB_INDEX_RESULT)
        cmake_pop_check_state()

        if(CXX_LD_GDB_INDEX_RESULT)
                string_append(CMAKE_EXE_LINKER_FLAGS " -Wl,--gdb-index")
                string_append(CMAKE_MODULE_LINKER_FLAGS " -Wl,--gdb-index")
                string_append(CMAKE_SHARED_LINKER_FLAGS " -Wl,--gdb-index")
                # gdb sometimes reports "incomplete type" after linking with --gdb-index. This flag makes the index contain
                # enough information to avoid the problem.
                string_append(CMAKE_C_FLAGS " -ggnu-pubnames")
                string_append(CMAKE_CXX_FLAGS " -ggnu-pubnames")
        else()
                message(STATUS "Seems like linker does not support --gdb-index")
        endif()
endif()

if(LINUX)
        # May be set to ON in cmake/build_configurations/mysql_release.cmake
        if(DEFINED REPRODUCIBLE_BUILD)
                set(REPRODUCIBLE_BUILD_DEFAULT ${REPRODUCIBLE_BUILD})
        else()
                set(REPRODUCIBLE_BUILD_DEFAULT OFF)
        endif()
        option(REPRODUCIBLE_BUILD "Take extra pains to make build result independent of build location and time"
               ${REPRODUCIBLE_BUILD_DEFAULT})
endif()
if(REPRODUCIBLE_BUILD)
        set(DEBUG_PREFIX_FLAGS
            "-fdebug-prefix-map=${CMAKE_SOURCE_DIR}/=./ -fdebug-prefix-map=${CMAKE_CURRENT_BINARY_DIR}=./obj")

        # See if -fdebug-prefix= commands are included in the debug output, making the build unreproducible with switches
        # recorded. See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=69821.
        execute_process(
                COMMAND ${CMAKE_C_COMPILER} -g3 -x c -S -fdebug-prefix-map=foo=bar -o - -
                INPUT_FILE /dev/null
                OUTPUT_VARIABLE DEBUG_PREFIX_MAP_RESULT)
        if(DEBUG_PREFIX_MAP_RESULT MATCHES "foo=bar")
                set(DEBUG_PREFIX_FLAGS "${DEBUG_PREFIX_FLAGS} -gno-record-gcc-switches")
        endif()

        string_append(CMAKE_C_FLAGS " ${DEBUG_PREFIX_FLAGS}")
        string_append(CMAKE_CXX_FLAGS " ${DEBUG_PREFIX_FLAGS}")

        set(BISON_NO_LINE_OPT "--no-lines")

        set_property(GLOBAL PROPERTY RULE_LAUNCH_COMPILE "${CMAKE_SOURCE_DIR}/scripts/invoke-with-relative-paths.pl")
endif()

option(ENABLED_LOCAL_INFILE "If we should enable LOAD DATA LOCAL by default" OFF)
mark_as_advanced(ENABLED_LOCAL_INFILE)

#
# Options related to client-side protocol tracing
#

option(WITH_CLIENT_PROTOCOL_TRACING "Support for client-side protocol tracing plugins" ON)
option(WITH_TEST_TRACE_PLUGIN
       "Have a built-in test protocol trace plugin in libmysql (requires WITH_CLIENT_PROTOCOL_TRACING option)" OFF)

# Sanity checks for protocol tracing options

if(WITH_TEST_TRACE_PLUGIN AND NOT WITH_CLIENT_PROTOCOL_TRACING)
        message("WARNING: Test trace plugin was selected but client protocol tracing
           infrastructure is not enabled - ignoring")
        set(WITH_TEST_TRACE_PLUGIN 0)
endif()

if(WITH_TEST_TRACE_PLUGIN AND NOT CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG")
        message(SEND_ERROR "Test trace plugin was selected but it can be included only in
     debug binaries. Set WITH_TEST_TRACE_PLUGIN to OFF or WITH_DEBUG to ON.")
endif()

# Add safemutex for debug configurations Enable debug sync for debug builds.
foreach(LANG C CXX)
        string_prepend(CMAKE_${LANG}_FLAGS_DEBUG "-DENABLED_DEBUG_SYNC ")
        string_prepend(CMAKE_${LANG}_FLAGS_DEBUG "-DSAFE_MUTEX ")
endforeach()

# Set commonly used variables
if(WIN32)
        set(DEFAULT_MYSQL_HOME "C:/Program Files/MySQL/MySQL Server ${MYSQL_BASE_VERSION}")
        set(SHAREDIR share)
else()
        set(DEFAULT_MYSQL_HOME ${CMAKE_INSTALL_PREFIX})
        set(SHAREDIR ${DEFAULT_MYSQL_HOME}/${INSTALL_MYSQLSHAREDIR})
endif()

set(DEFAULT_BASEDIR "${DEFAULT_MYSQL_HOME}")

if(IS_ABSOLUTE ${INSTALL_MYSQLDATADIR})
        set(MYSQL_DATADIR
            ${INSTALL_MYSQLDATADIR}
            CACHE PATH "default MySQL data directory")
else()
        set(MYSQL_DATADIR
            "${DEFAULT_MYSQL_HOME}/${INSTALL_MYSQLDATADIR}"
            CACHE PATH "default MySQL data directory")
endif()

if(IS_ABSOLUTE ${INSTALL_MYSQLKEYRINGDIR})
        set(MYSQL_KEYRINGDIR
            ${INSTALL_MYSQLKEYRINGDIR}
            CACHE PATH "default MySQL keyring directory")
else()
        set(MYSQL_KEYRINGDIR
            "${DEFAULT_MYSQL_HOME}/${INSTALL_MYSQLKEYRINGDIR}"
            CACHE PATH "default MySQL keyring directory")
endif()

set(DEFAULT_CHARSET_HOME "${DEFAULT_MYSQL_HOME}")
set(PLUGINDIR "${DEFAULT_MYSQL_HOME}/${INSTALL_PLUGINDIR}")
if(SYSCONFDIR)
        set(DEFAULT_SYSCONFDIR "${SYSCONFDIR}")
endif()

if(WIN32) # P_tmpdir is not defined on Windows as of VS2015.
        set(TMPDIR
            "" # So we use empty path as default. In practice TMP/TEMP is used
            CACHE PATH "PATH to MySQL TMP dir")
else()
        set(TMPDIR
            "P_tmpdir"
            CACHE PATH "PATH to MySQL TMP dir. Defaults to the P_tmpdir macro in <stdio.h>")
endif()
if(TMPDIR STREQUAL "P_tmpdir")
        # Do not quote it, to refer to the P_tmpdir macro.
        set(DEFAULT_TMPDIR "P_tmpdir")
else()
        # Quote it, to make it a const char string.
        set(DEFAULT_TMPDIR "\"${TMPDIR}\"")
endif()

include(cmake/boost.cmake)

if(LINUX)
        option(WITH_SYSTEMD "Enable installation of systemd support files" OFF)
        option(WITH_SYSTEMD_DEBUG "Build in systemd debug trace" OFF)
        if(WITH_SYSTEMD)
                include(cmake/systemd.cmake)
        elseif(WITH_SYSTEMD_DEBUG)
                message(FATAL_ERROR "systemd-debugging requires systemd, and systemd requires linux")
        endif()
else()
        if(WITH_SYSTEMD)
                message(FATAL_ERROR "Installation of systemd support files not supported")
        endif()
        if(WITH_SYSTEMD_DEBUG)
                message(FATAL_ERROR "systemd-debugging requires systemd, and systemd requires linux")
        endif()
endif()

# Run platform tests
include(configure.cmake)

# Common defines and includes
add_definitions(-DHAVE_CONFIG_H)
add_definitions(-D__STDC_LIMIT_MACROS) # Enable C99 limit macros
add_definitions(-D__STDC_FORMAT_MACROS) # Enable C99 printf format macros
add_definitions(-D_USE_MATH_DEFINES) # Get access to M_PI, M_E, etc. in math.h
add_definitions(-DLZ4_DISABLE_DEPRECATE_WARNINGS) # C++14 deprecation warnings in LZ4.

option(ENABLE_EXPERIMENT_SYSVARS "Expose ussually hidden system variables to allow experiments" OFF)
if(ENABLE_EXPERIMENT_SYSVARS)
        add_definitions(-DENABLE_EXPERIMENT_SYSVARS)
endif()

include_directories(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_BINARY_DIR}/include ${CMAKE_SOURCE_DIR}
                    ${CMAKE_SOURCE_DIR}/include)

if(WITH_LTO)
        if(APPLE)
                # Requires CMake 3.9+
                include(CheckIPOSupported)
                # Will terminate cmake if the check fails.
                check_ipo_supported()
                # macOS has problems with enabling LTO for shared libs, so we only set it as target property for mysqld later.
        else()
                my_check_cxx_compiler_flag("-flto" CXX_LTO_RESULT)
                if(NOT CXX_LTO_RESULT)
                        message(FATAL_ERROR "Compiler does not support -flto")
                endif()
                string_append(CMAKE_C_FLAGS " -flto")
                string_append(CMAKE_CXX_FLAGS " -flto")

                # Test for parallel linking.
                set(NUM_PROCESSING_UNITS 4)
                my_check_cxx_compiler_flag("-flto=${NUM_PROCESSING_UNITS}" CXX_LTO_PARALLEL_RESULT)
                if(CXX_LTO_PARALLEL_RESULT)
                        find_program(NPROC_EXECUTABLE nproc)
                        if(NPROC_EXECUTABLE)
                                execute_process(
                                        COMMAND ${NPROC_EXECUTABLE}
                                        OUTPUT_VARIABLE NPROC_NUM
                                        RESULT_VARIABLE NPROC_RESULT
                                        OUTPUT_STRIP_TRAILING_WHITESPACE)
                                if(NOT NPROC_RESULT)
                                        set(NUM_PROCESSING_UNITS ${NPROC_NUM})
                                endif()
                        endif()
                        string_append(CMAKE_CXX_LINK_FLAGS " -flto=${NUM_PROCESSING_UNITS}")
                        string_append(CMAKE_SHARED_LINKER_FLAGS " -flto=${NUM_PROCESSING_UNITS}")
                else()
                        string_append(CMAKE_CXX_LINK_FLAGS " -flto")
                        string_append(CMAKE_SHARED_LINKER_FLAGS " -flto")
                endif()
        endif()

        # On some platforms (Fedora) we *must* use gcc-ar / gcc-ranlib to get the right plugins for supporting -flto output from
        # gcc.
        if(MY_COMPILER_IS_GNU)
                if(ALTERNATIVE_ENABLE)
                        get_filename_component(ALTERNATIVE_ENABLE_DIR ${ALTERNATIVE_ENABLE} PATH)
                        set(DEVTOOLSET_ROOT "${ALTERNATIVE_ENABLE_DIR}/root")
                        find_program(GCC_AR_EXECUTABLE gcc-ar NO_DEFAULT_PATH PATHS "${DEVTOOLSET_ROOT}/usr/bin")
                        find_program(GCC_RANLIB_EXECUTABLE gcc-ranlib NO_DEFAULT_PATH PATHS "${DEVTOOLSET_ROOT}/usr/bin")
                elseif(ALTERNATIVE_GCC)
                        string(REGEX MATCH "^([0-9])" ALTERNATIVE_GCC_MAJ_VER ${CMAKE_C_COMPILER_VERSION})
                        get_filename_component(ALTERNATIVE_GCC_DIR ${ALTERNATIVE_GCC} PATH)
                        find_program(GCC_AR_EXECUTABLE gcc-ar-${ALTERNATIVE_GCC_MAJ_VER} NO_DEFAULT_PATH
                                     PATHS "${ALTERNATIVE_GCC_DIR}")
                        find_program(GCC_RANLIB_EXECUTABLE gcc-ranlib-${ALTERNATIVE_GCC_MAJ_VER} NO_DEFAULT_PATH
                                     PATHS "${ALTERNATIVE_GCC_DIR}")
                else()
                        find_program(GCC_AR_EXECUTABLE gcc-ar)
                        find_program(GCC_RANLIB_EXECUTABLE gcc-ranlib)
                endif()
                if(GCC_AR_EXECUTABLE AND GCC_RANLIB_EXECUTABLE)
                        set(CMAKE_AR ${GCC_AR_EXECUTABLE})
                        set(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE})
                endif()
        endif()
endif(WITH_LTO)

if(APPLE)
        option(WITH_DEVELOPER_ENTILEMENTS "Add developer entitlements" OFF)
endif()

# Explicit list of all -DWITH_XXX=system possibilities *except* boost, which requires one specific version to be downloaded.
# RapidJSON is also excluded, since the latest released version does not fully support the use of std::regex as the regular
# expression engine. It is required by JSON_SCHEMA_VALID, so don't add it automatically until an official version with full
# support for std::regex is released. The bundled version has the necessary patches to make it work.
#
# "system" versions of these for macOS are available from Xcode or Homebrew. Libraries from Xcode have file extension '.tbd'.
# Libraries from Homebrew have file extension '.dylib'. To get all the Homebrew libraries, do 'brew install' <name of lib>.
set(SYSTEM_LIBRARIES
    CURL # Xcode
    EDITLINE # Xcode
    ICU # Homebrew  icu4c
    LIBEVENT # Homebrew  libevent
    LZ4 # Homebrew  lz4
    PROTOBUF # Homebrew  protobuf
    SSL # Homebrew  openssl@1.1
    ZSTD # Homebrew  zstd
    FIDO # Homebrew  libfido2
)

if(APPLE)
        # Homebrew default location is
        if(APPLE_ARM)
                set(HOMEBREW_HOME "/opt/homebrew/opt")
        else()
                set(HOMEBREW_HOME "/usr/local/opt")
        endif()
endif()

set(WITH_SYSTEM_LIBS_DEFAULT OFF)
option(WITH_SYSTEM_LIBS "Use -DWITH_XXX=system for ${SYSTEM_LIBRARIES}" ${WITH_SYSTEM_LIBS_DEFAULT})

# undefined symbol: _Z15fido_assert_newv which is fido_assert_new() /usr/include/fido.h is missing 'extern "C"' but even if we
# fix that, tests will fail.
if(LINUX_UBUNTU)
        if(LINUX_UBUNTU_VERSION_ID VERSION_LESS "21.04")
                list(REMOVE_ITEM SYSTEM_LIBRARIES FIDO)
        endif()
endif()

get_cmake_property(CACHE_VARS CACHE_VARIABLES)
unset(COMMAND_LINE_VARS)
foreach(CACHE_VAR ${CACHE_VARS})
        get_property(
                CACHE_VAR_HELPSTRING
                CACHE ${CACHE_VAR}
                PROPERTY HELPSTRING)
        if(CACHE_VAR_HELPSTRING STREQUAL "No help, variable specified on the command line.")
                list(APPEND COMMAND_LINE_VARS ${CACHE_VAR})
        endif()
endforeach()

# Default to "system" for all SYSTEM_LIBRARIES
if(WITH_SYSTEM_LIBS)
        foreach(SYSTEM_LIB ${SYSTEM_LIBRARIES})
                list(FIND COMMAND_LINE_VARS WITH_${SYSTEM_LIB} FOUNDIT)
                if(FOUNDIT LESS 0)
                        set(WITH_${SYSTEM_LIB} "system")
                        set(WITH_${SYSTEM_LIB}
                            "system"
                            CACHE STRING "Use system rather than bundled" FORCE)
                        message(STATUS "Adding -DWITH_${SYSTEM_LIB}=${WITH_${SYSTEM_LIB}}")
                else()
                        message(STATUS "Found WITH_${SYSTEM_LIB}=${WITH_${SYSTEM_LIB}} on command line")
                endif()
        endforeach()
        # Don't redo the system analysis on subsequent runs of cmake
        unset(WITH_SYSTEM_LIBS)
        unset(WITH_SYSTEM_LIBS CACHE)
endif()

# Add bundled or system zlib.
mysql_check_zlib()

# Add bundled or system zstd.
mysql_check_zstd()

if(LINUX)
        # Look up patchelf(1) here, before doing SSL/KERBEROS/SASL/LDAP config.
        find_program(PATCHELF_EXECUTABLE patchelf)
        add_custom_target(copy_linux_custom_dlls)
        set(KNOWN_CUSTOM_LIBRARIES
            ""
            CACHE INTERNAL "" FORCE)
endif()

# If all custom packages are in the same directory:
if(WITH_CUSTOM_LIBRARIES)
        file(GLOB WITH_SSL "${WITH_CUSTOM_LIBRARIES}/openssl*")
        file(GLOB WITH_KERBEROS "${WITH_CUSTOM_LIBRARIES}/krb5*")
        file(GLOB WITH_SASL "${WITH_CUSTOM_LIBRARIES}/cyrus-sasl*")
        file(GLOB WITH_LDAP "${WITH_CUSTOM_LIBRARIES}/openldap*")
        foreach(WITH_OPT WITH_SSL WITH_KERBEROS WITH_SASL WITH_LDAP)
                if(IS_DIRECTORY ${${WITH_OPT}})
                        set(${WITH_OPT}
                            ${${WITH_OPT}}
                            CACHE INTERNAL "" FORCE)
                        message(STATUS "${WITH_OPT} ${${WITH_SSL}}")
                else()
                        message(WARNING "${WITH_OPT} not found") # This should be FATAL_ERROR
                endif()
        endforeach()
endif()

# Add system/custom openssl.
mysql_check_ssl()
mysql_check_ssl_dlls()

# Other system libs which use OpenSSL must be disabled. This includes: kerberos sasl ldap curl.
if(ALTERNATIVE_SYSTEM_SSL)
        set(WITH_AUTHENTICATION_LDAP OFF)
        set(WITH_AUTHENTICATION_LDAP
            OFF
            CACHE INTERNAL "" FORCE)
        set(WITH_AUTHENTICATION_CLIENT_PLUGINS OFF)
        set(WITH_AUTHENTICATION_CLIENT_PLUGINS
            OFF
            CACHE INTERNAL "" FORCE)
        set(WITH_CURL "bundled")
        set(WITH_CURL
            "bundled"
            CACHE INTERNAL "" FORCE)
        message(STATUS "Disabling all authentication plugins.")
endif()

# Add Kerberos library
mysql_check_kerberos()
mysql_check_kerberos_dlls()

# Add SASL library
mysql_check_sasl()
mysql_check_sasl_dlls()

# Add LDAP library
mysql_check_ldap()
mysql_check_ldap_dlls()

# Add Windows specific jemalloc DLL
if(WIN32)
        mysql_check_win_jemalloc()
endif()

# Custom versions of LDAP / SASL / SSL.
if(LINUX AND KNOWN_CUSTOM_LIBRARIES)
        # Custom libraries are not targets, we install them as FILES. INSTALL_RPATH must must be set depending on binary type
        # EXECUTABLE MODULE_LIBRARY SHARED_LIBRARY, see ADD_INSTALL_RPATH_FOR_OPENSSL
        set(LINUX_INSTALL_RPATH_ORIGIN 1)

        # Debug versions of plugins may be installed to <root>/lib/plugin/debug
        foreach(LINK_FLAG CMAKE_MODULE_LINKER_FLAGS_DEBUG CMAKE_SHARED_LINKER_FLAGS_DEBUG)
                string_append(${LINK_FLAG} " -Wl,-rpath,'\$ORIGIN/../../private'")
                message(STATUS "${LINK_FLAG} ${${LINK_FLAG}}")
        endforeach()
        if(NOT PATCHELF_EXECUTABLE)
                message(FATAL_ERROR "Please install the patchelf(1) utility.")
        endif()
        verify_custom_library_dependencies()
endif()

if(WITH_AUTHENTICATION_LDAP)
        if(LINUX AND KNOWN_CUSTOM_LIBRARIES)
                # LDAP / SASL / KERBEROS / SSL must all be "system" or "custom", not a mix.
                if(WITH_LDAP STREQUAL "system"
                   OR WITH_SASL STREQUAL "system"
                   OR # WITH_KERBEROS STREQUAL "system" OR # enable this later
                      WITH_SSL STREQUAL "system")
                        message(WARNING "-DWITH_AUTHENTICATION_LDAP=ON")
                        message(FATAL_ERROR "Inconsistent options for LDAP/SASL/KERBEROS/SSL")
                endif()
        elseif(WIN32)
                # system LDAP, but SASL needs to be explicitly added
                if(NOT SASL_LIBRARY_DLL OR NOT SASL_SCRAM_PLUGIN)
                        message(WARNING "-DWITH_AUTHENTICATION_LDAP=ON")
                        message(FATAL_ERROR "Missing SASL and/or SCRAM libraries.")
                endif()
        elseif(APPLE)
                # system LDAP and SASL, but there is no scram plugin.
        elseif(SOLARIS)
                # 11.3 has system LDAP and SASL, but there is no scram plugin.
        else()
                # We must have "system" LDAP, KERBEROS and SASL (SSL is always required)
                set(LDAP_WARN_GIVEN)
                set(SASL_WARN_GIVEN)
                set(KERBEROS_WARN_GIVEN)
                warn_missing_system_ldap(LDAP_WARN_GIVEN)
                warn_missing_system_sasl(SASL_WARN_GIVEN)
                warn_missing_system_kerberos(KERBEROS_WARN_GIVEN)
                if(LDAP_WARN_GIVEN
                   OR SASL_WARN_GIVEN
                   OR KERBEROS_WARN_GIVEN)
                        # SUSE linux: may or may not have SCRAM, do not break the build.
                        if(LINUX_SUSE)
                                message(WARNING "-DWITH_AUTHENTICATION_LDAP=ON, but missing system libraries")
                        else()
                                message(FATAL_ERROR "-DWITH_AUTHENTICATION_LDAP=ON, but missing system libraries")
                        endif()
                endif()
        endif()
endif()

if(WITH_AUTHENTICATION_KERBEROS)
        set(KERBEROS_GSSAPI_WARN_GIVEN)
        warn_missing_system_kerberos_gssapi(KERBEROS_GSSAPI_WARN_GIVEN)
        if(KERBEROS_GSSAPI_WARN_GIVEN)
                message(WARNING "-DWITH_AUTHENTICATION_KERBEROS=ON, but missing system libraries")
        endif()
endif()

# Add system/bundled editline.
mysql_check_editline()
set(EDITLINE_WARN_GIVEN)
warn_missing_system_editline(EDITLINE_WARN_GIVEN)
if(EDITLINE_WARN_GIVEN)
        message(FATAL_ERROR "-DWITH_EDITLINE=system, but missing system libraries.\n"
                            "You can use -DWITH_EDITLINE=bundled instead.")
endif()

# Add libevent
mysql_check_libevent()
set(LIBEVENT_WARN_GIVEN)
warn_missing_system_libevent(LIBEVENT_WARN_GIVEN)
if(LIBEVENT_WARN_GIVEN)
        message(FATAL_ERROR "-DWITH_LIBEVENT=system, but missing system libraries.\n"
                            "You can use -DWITH_LIBEVENT=bundled instead.")
endif()

# Add lz4 library
mysql_check_lz4()
# Add icu library
if(NOT WITHOUT_SERVER)
        mysql_check_icu()
endif()
# Add protoc and libprotobuf
if(NOT WITHOUT_SERVER)
        mysql_check_protobuf()
        mysql_check_protobuf_dlls()
endif()

if(NOT WITHOUT_SERVER)
        # Check BISON_VERSION and set BISON_FLAGS_WARNINGS Do it here, before any other cmake file does FIND_PACKAGE(BISON
        # REQUIRED) so that our search for custom bison installations can run first.
        include(bison)
endif()

# Try and set CURL_LIBRARY
mysql_check_curl()
mysql_check_curl_dlls()
set(CURL_WARN_GIVEN)
warn_missing_system_curl(CURL_WARN_GIVEN)
if(CURL_WARN_GIVEN)
        message(FATAL_ERROR "-DWITH_CURL=system, but missing system libraries.\n" "You can disable CURL with -DWITH_CURL=0")
endif()
# Add RapidJSON library.
mysql_check_rapidjson()

# Look for fido2 library
mysql_check_fido()
mysql_check_fido_dlls()

if(APPLE)
        get_filename_component(HOMEBREW_BASE ${HOMEBREW_HOME} DIRECTORY)
        if(EXISTS ${HOMEBREW_BASE}/include/boost)
                foreach(SYSTEM_LIB ICU LIBEVENT LZ4 PROTOBUF ZSTD FIDO)
                        if(WITH_${SYSTEM_LIB} STREQUAL "system")
                                message(
                                        FATAL_ERROR
                                                "WITH_${SYSTEM_LIB}=system is not compatible with Homebrew boost\n"
                                                "MySQL depends on ${BOOST_PACKAGE_NAME} with a set of patches.\n"
                                                "Including headers from ${HOMEBREW_BASE}/include "
                                                "will break the build.\n"
                                                "Please use WITH_${SYSTEM_LIB}=bundled\n"
                                                "or do 'brew uninstall boost' or 'brew unlink boost'")
                        endif()
                endforeach()
        endif()
        # Ensure that we look in /usr/local/include or /opt/homebrew/include
        foreach(SYSTEM_LIB ICU LIBEVENT LZ4 PROTOBUF ZSTD FIDO)
                if(WITH_${SYSTEM_LIB} STREQUAL "system")
                        include_directories(SYSTEM ${HOMEBREW_BASE}/include)
                        break()
                endif()
        endforeach()
endif()

if(WITH_AUTHENTICATION_FIDO OR WITH_AUTHENTICATION_CLIENT_PLUGINS)
        if(WITH_FIDO STREQUAL "system" AND NOT WITH_SSL STREQUAL "system")
                message(WARNING "-DWITH_AUTHENTICATION_FIDO=ON")
                message(FATAL_ERROR "Inconsistent options for FIDO/SSL")
        endif()

        # FIDO (or libudev) missing, warn about what is missing, and break the build.
        if(WITH_AUTHENTICATION_FIDO AND NOT FIDO_FOUND)
                set(UDEV_WARN_MISSING)
                set(FIDO_WARN_MISSING)
                warn_missing_system_udev(UDEV_WARN_MISSING)
                warn_missing_system_fido(FIDO_WARN_MISSING)
                message(FATAL_ERROR "-DWITH_AUTHENTICATION_FIDO=ON, but missing required libraries")
        endif()
endif()

macro(MY_INCLUDE_SYSTEM_DIRECTORIES LIBRARY)
        if(${WITH_${LIBRARY}} STREQUAL "bundled")
                set(BEFORE_OR_AFTER "BEFORE")
        else()
                set(BEFORE_OR_AFTER "AFTER")
        endif()

        # ICU headers for WIN32 need extra info about their libs state
        if(WIN32 AND ${LIBRARY} STREQUAL "ICU")
                add_definitions(-DU_STATIC_IMPLEMENTATION)
        endif()

        if(DEFINED ${LIBRARY}_INCLUDE_DIRS)
                include_directories(${BEFORE_OR_AFTER} SYSTEM ${${LIBRARY}_INCLUDE_DIRS})
        elseif(DEFINED ${LIBRARY}_INCLUDE_DIR)
                include_directories(${BEFORE_OR_AFTER} SYSTEM ${${LIBRARY}_INCLUDE_DIR})
        endif()
        if("${${LIBRARY}_INCLUDE_DIR}" STREQUAL "")
                if("${${LIBRARY}_INCLUDE_DIRS}" STREQUAL "")
                        message(WARNING "${LIBRARY} is unknown")
                endif()
        endif()
        if(DEFINED ${LIBRARY}_INCLUDE_DIRS AND DEFINED ${LIBRARY}_INCLUDE_DIR)
                if(NOT "${${LIBRARY}_INCLUDE_DIR}" STREQUAL "${${LIBRARY}_INCLUDE_DIRS}")
                        message(WARNING "NOT ${${LIBRARY}_INCLUDE_DIR} STREQUAL ${${LIBRARY}_INCLUDE_DIRS}")
                endif()
        endif()
endmacro()

macro(MY_TARGET_INCLUDE_SYSTEM_DIRECTORIES TARGET LIBRARY)
        if(${WITH_${LIBRARY}} STREQUAL "bundled")
                target_include_directories(${TARGET} SYSTEM BEFORE PRIVATE ${${LIBRARY}_INCLUDE_DIR})
        else()
                target_include_directories(${TARGET} SYSTEM PRIVATE ${${LIBRARY}_INCLUDE_DIR})
        endif()
endmacro()

if(LINUX)
        # Check for pthread_setname_np
        check_c_source_compiles(
                "
  #include <pthread.h>

  int main(int ac, char **av)
  {
    const char *thread_name= 0;
    int ret = pthread_setname_np(pthread_self(), thread_name);
    return ret;
  }"
                HAVE_PTHREAD_SETNAME_NP)
endif()

if(WITH_PROTOBUF STREQUAL "bundled" OR WITH_FIDO STREQUAL "bundled")
        # Protobuf library is a target, installed to <root>/${INSTALL_PRIV_LIBDIR} INSTALL_RPATH must be set for all binaries
        # linking with libprotobuf.
        if(WITH_PROTOBUF STREQUAL "bundled")
                set(UNIX_INSTALL_RPATH_ORIGIN_PRIV_LIBDIR 1)
                add_subdirectory(extra/protobuf)
        endif()

        # The Fido library is a target, installed to <root>/${INSTALL_PRIV_LIBDIR} INSTALL_RPATH must be set for all binaries
        # linking with libfido2.
        if(WITH_FIDO STREQUAL "bundled")
                set(INSTALL_RPATH_FOR_FIDO2 1)

                # Do not break the build here in case of missing libudev on Linux.
                set(UDEV_WARN_MISSING FALSE)
                warn_missing_system_udev(UDEV_WARN_MISSING)
                if((WITH_AUTHENTICATION_FIDO OR WITH_AUTHENTICATION_CLIENT_PLUGINS) AND NOT UDEV_WARN_MISSING)
                        # Silence warning about CMP0075
                        cmake_push_check_state()
                        set(CMAKE_REQUIRED_LIBRARIES)
                        add_subdirectory(${CMAKE_SOURCE_DIR}/${CBOR_BUNDLE_SRC_PATH})
                        add_subdirectory(${CMAKE_SOURCE_DIR}/${FIDO_BUNDLE_SRC_PATH})
                        cmake_pop_check_state()
                endif()
        endif()

        if(NOT APPLE AND NOT WIN32)
                # Debug versions of plugins may be installed to <root>/lib/plugin/debug
                foreach(LINK_FLAG CMAKE_MODULE_LINKER_FLAGS_DEBUG CMAKE_SHARED_LINKER_FLAGS_DEBUG)
                        string_append(${LINK_FLAG} " -Wl,-rpath,'\$ORIGIN/../../private'")
                        message(STATUS "${LINK_FLAG} ${${LINK_FLAG}}")
                endforeach()
        endif()
endif()

#
# Setup maintainer mode options by the end. Platform checks are not run with the warning options as to not perturb fragile
# checks (i.e. do not make warnings into errors).
#
# Why don't these flags affect the entire build? Because things may already have been included with ADD_SUBDIRECTORY
#
option(MYSQL_MAINTAINER_MODE "MySQL maintainer-specific development environment" OFF)

include(maintainer)

if(WITH_UNIT_TESTS)
        enable_testing()
        include(googletest)
else()
        # In case we switch from ON to OFF, we do not want ctest to pick up any obsolete test executables.
        if(EXISTS "${CMAKE_BINARY_DIR}/CTestTestfile.cmake")
                file(GLOB_RECURSE CTEST_TESTFILES "${CMAKE_BINARY_DIR}/CTestTestfile.cmake")
                file(REMOVE ${CTEST_TESTFILES})
        endif()
endif()

if(WITH_ICU STREQUAL "bundled")
        if(IS_ABSOLUTE "${INSTALL_PRIV_LIBDIR}")
                set(MYSQL_ICU_DATADIR
                    "${INSTALL_PRIV_LIBDIR}"
                    CACHE PATH "default MySQL ICU data directory")
        else()
                set(MYSQL_ICU_DATADIR
                    "${DEFAULT_MYSQL_HOME}/${INSTALL_PRIV_LIBDIR}"
                    CACHE PATH "default MySQL ICU data directory")
        endif()

        # Write an explicit symlink in CMAKE_BINARY_DIR, so we can find the ICU data directory when running in the build
        # sandbox. See setting of mysql_home in calculate_mysql_home_from_my_progname().
        file(TO_NATIVE_PATH "${CMAKE_SOURCE_DIR}/extra/icu" ICU_DATA_SOURCE_DIR)
        file(WRITE "${CMAKE_BINARY_DIR}/${ICUDT_DIR}.lnk" "${ICU_DATA_SOURCE_DIR}")
endif()

# The hypergraph optimizer is default on only for debug builds.
if(CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG" OR WITH_DEBUG)
        set(WITH_HYPERGRAPH_OPTIMIZER_DEFAULT ON)
else()
        set(WITH_HYPERGRAPH_OPTIMIZER_DEFAULT OFF)
endif()
option(WITH_HYPERGRAPH_OPTIMIZER "Allow use of the hypergraph join optimizer" ${WITH_HYPERGRAPH_OPTIMIZER_DEFAULT})

# Utility target to build every executable tagged with ADD_TEST.
add_custom_target(unittest_all)

# Utility target to build every target added with MYSQL_ADD_PLUGIN.
add_custom_target(plugin_all)

# Utility target to build every target added with MYSQL_ADD_COMPONENT.
add_custom_target(component_all)

# Utility target to generate all sources from .proto files.
add_custom_target(protobuf_generated_all)

# Utility target to generate everything created by MYSQL_ADD_EXECUTABLE().
add_custom_target(executable_all)

# Utility target to generate sources needed for clang-tidy. Example usage:
#
# export CC=clang export CXX=clang++ mkdir build-clang-tidy; cd build-clang-tidy cmake .. -DCMAKE_EXPORT_COMPILE_COMMANDS=1
# -DWITH_SYSTEM_LIBS=1 make clang_tidy_prerequisites clang-tidy -p .
# --checks=modernize-use-nullptr,-clang-analyzer-core.NullDereference ../sql/sys_vars.cc
add_custom_target(clang_tidy_prerequisites)
add_dependencies(clang_tidy_prerequisites protobuf_generated_all)
add_dependencies(clang_tidy_prerequisites GenError)

add_subdirectory(include)
add_subdirectory(strings)
add_subdirectory(vio)
add_subdirectory(mysys)
add_subdirectory(libmysql)
add_subdirectory(libbinlogevents)
add_subdirectory(libbinlogstandalone)
add_subdirectory(libchangestreams)

if(NOT WITHOUT_SERVER)
        if(WITH_UNIT_TESTS)
                if(WIN32)
                        set(WITH_SHARED_UNITTEST_LIBRARY_DEFAULT OFF)
                else()
                        set(WITH_SHARED_UNITTEST_LIBRARY_DEFAULT ON)
                endif()
                option(WITH_SHARED_UNITTEST_LIBRARY "Build a shared library for unit tests which depend on the entire server"
                       ${WITH_SHARED_UNITTEST_LIBRARY_DEFAULT})
        endif()

        # Add storage engines and plugins.
        set(MYSQLD_STATIC_PLUGIN_LIBS
            ""
            CACHE INTERNAL "")
        configure_plugins()
else()
        if(EXISTS "${CMAKE_SOURCE_DIR}/storage/ndb")
                # We may still want Cluster client libraries, use -DWITH_NDB=1
                add_subdirectory(storage/ndb)
        endif()
endif()

if(WITHOUT_SERVER)
        add_subdirectory(components/libminchassis)
else()
        configure_components()
endif()

if(WITH_UNIT_TESTS)
        add_subdirectory(unittest)
        add_subdirectory(unittest/examples)
        add_subdirectory(unittest/mytap)
        add_subdirectory(unittest/mytap/t)
endif()

add_subdirectory(client)
add_subdirectory(utilities)
add_subdirectory(share)
add_subdirectory(libservices)

if(NOT WITHOUT_SERVER)
        add_subdirectory(testclients)
        add_subdirectory(sql)
endif()

# Some unit tests need to link with the entire server, use this library:
if(NOT WITHOUT_SERVER AND WITH_UNIT_TESTS)

        if(WITH_SHARED_UNITTEST_LIBRARY)
                merge_libraries_shared(
                        server_unittest_library
                        SKIP_INSTALL
                        LINK_PUBLIC
                        sql_main
                        ${MYSQLD_STATIC_PLUGIN_LIBS}
                        minchassis
                        ${ICU_LIBRARIES}
                        # Import some core symbols. Other symbols needed by the unit test executables are pulled in transitively
                        # by symbol dependencies.
                        #
                        # Since everything has visibility("default") the library will export every symbol pulled in from the
                        # source libraries.
                        #
                        # If some symbols are still missing, they will be picked up from dependent libraries, since we
                        # LINK_PUBLIC. To see what symbols we need to import, remove LINK_PUBLIC above.
                        EXPORTS
                        builtin_perfschema_plugin # Pulls in the whole server.
                        mysql_service_mysql_rwlock_v1 # Pulls in minchassis
                )
                if(HAVE_LIBNUMA)
                        target_link_libraries(server_unittest_library PUBLIC numa)
                endif()
                if(WITH_LTO OR CMAKE_COMPILER_FLAG_WITH_LTO)
                        # We get ODR violations because of multiple bison parsers.
                        my_check_cxx_compiler_warning("error=odr" HAS_WARN_FLAG)
                        if(HAS_WARN_FLAG)
                                my_target_link_options(server_unittest_library "${HAS_WARN_FLAG}")
                        endif()
                        # We may get errors in boost/geometry/srs/projections/proj/ob_tran.hpp and several other boost files.
                        my_check_cxx_compiler_warning("error=maybe-uninitialized" HAS_WARN_FLAG)
                        if(HAS_WARN_FLAG)
                                my_target_link_options(server_unittest_library "${HAS_WARN_FLAG}")
                        endif()
                endif()
        else()
                set(DUMMY_SOURCE_FILE ${CMAKE_BINARY_DIR}/server_unittest_library.c)
                add_custom_command(
                        OUTPUT ${DUMMY_SOURCE_FILE}
                        COMMAND ${CMAKE_COMMAND} -E touch ${DUMMY_SOURCE_FILE}
                        DEPENDS sql_main)
                add_library(server_unittest_library STATIC ${DUMMY_SOURCE_FILE})
                target_link_libraries(server_unittest_library perfschema)
                target_link_libraries(server_unittest_library sql_main)
                target_link_libraries(server_unittest_library minchassis)
                target_link_libraries(server_unittest_library ${ICU_LIBRARIES})
        endif()
endif()

# scripts/mysql_config depends on client and server targets loaded above. It is referenced by some of the directories below, so
# we insert it here.
add_subdirectory(scripts)

if(NOT WITHOUT_SERVER)
        add_subdirectory(mysql-test)
        add_subdirectory(mysql-test/lib/My/SafeProcess)
        add_subdirectory(support-files)
        if(WITH_INTERNAL)
                add_subdirectory(internal)
        endif()
endif()

include(cmake/abi_check.cmake)
include(cmake/tags.cmake)

configure_file(config.h.cmake ${CMAKE_BINARY_DIR}/include/my_config.h)
configure_file(config.h.cmake ${CMAKE_BINARY_DIR}/include/config.h)
configure_file(${CMAKE_SOURCE_DIR}/include/mysql_version.h.in ${CMAKE_BINARY_DIR}/include/mysql_version.h)
configure_file(${CMAKE_SOURCE_DIR}/sql/sql_builtin.cc.in ${CMAKE_BINARY_DIR}/sql/sql_builtin.cc)

# depends on mysql_version.h to exist
if(NOT WITHOUT_SERVER)
        if(WITH_ROUTER)
                add_subdirectory(router)
        endif()
endif()

if(ENABLE_GCOV)
        include(fastcov)
endif()

if(UNIX)
        add_subdirectory(man)
endif()

if(NOT WITHOUT_SERVER)
        add_subdirectory(packaging/rpm-common)
        add_subdirectory(packaging/rpm-oel)
        add_subdirectory(packaging/rpm-fedora)
        add_subdirectory(packaging/rpm-sles)
        add_subdirectory(packaging/rpm-docker)
        add_subdirectory(packaging/deb-in)
endif()

get_property(
        CWD_DEFINITIONS
        DIRECTORY
        PROPERTY COMPILE_DEFINITIONS)
configure_file(${CMAKE_SOURCE_DIR}/cmake/info_macros.cmake.in ${CMAKE_BINARY_DIR}/info_macros.cmake @ONLY)

set(KNOWN_CONVENIENCE_LIBRARIES
    ""
    CACHE INTERNAL "")
# Add to the 'make clean' target. With this you need to do 'cmake .' before doing 'make' again.
set_property(
        DIRECTORY
        APPEND
        PROPERTY ADDITIONAL_MAKE_CLEAN_FILES "${CMAKE_BINARY_DIR}/archive_output_directory"
                 "${CMAKE_BINARY_DIR}/library_output_directory")

# Handle the "INFO_*" files.
include(${CMAKE_BINARY_DIR}/info_macros.cmake)
# Source: This can be done during the cmake phase, all information is available, but should be repeated on each "make" just in
# case someone does "cmake ; make ; git pull ; make".
create_info_src(${CMAKE_BINARY_DIR}/Docs)
my_add_custom_target(
        INFO_SRC
        ALL
        COMMAND
        ${CMAKE_COMMAND}
        -P
        ${CMAKE_SOURCE_DIR}/cmake/info_src.cmake
        BYPRODUCTS
        ${CMAKE_BINARY_DIR}/Docs/INFO_SRC
        WORKING_DIRECTORY
        ${CMAKE_BINARY_DIR}
        COMMENT
        "Generating Docs/INFO_SRC")

if(HAVE_BUILD_ID_SUPPORT AND NOT WITHOUT_SERVER)
        # Show it by default in pushuild, but not for developers.
        if(DEFINED ENV{PB2WORKDIR})
                option(SHOW_BUILD_ID "Show mysqld build id as final target" ON)
        else()
                option(SHOW_BUILD_ID "Show mysqld build id as final target" OFF)
        endif()

        if(SHOW_BUILD_ID)
                # Show build id in the build log, after everything else has bee built.
                add_custom_target(
                        show_build_id ALL
                        COMMAND $<TARGET_FILE:mysqld> --no-defaults --help | grep BuildID
                        COMMENT "Showing mysqld build id"
                        VERBATIM)
                add_dependencies(show_build_id mysqld)
                # Build all kinds of binaries first.
                add_dependencies(show_build_id component_all executable_all plugin_all unittest_all)
                if(WITH_ROUTER)
                        add_dependencies(show_build_id mysqlrouter_all)
                endif()
                # Other "ALL" targets.
                add_dependencies(
                        show_build_id GenSysSchemaC
                        # INFO_BIN see below
                        run_libmysql_api_test)
        endif()
endif()

unset(MYSQLD_EXECUTABLE_FOR_INFO_BIN)
if(HAVE_BUILD_ID_SUPPORT AND NOT WITHOUT_SERVER)
        set(MYSQLD_EXECUTABLE_FOR_INFO_BIN "-DMYSQLD_EXECUTABLE=$<TARGET_FILE:mysqld>")
endif()

# Build flags: This must be postponed to the make phase.
my_add_custom_target(
        INFO_BIN
        ALL
        COMMAND
        ${CMAKE_COMMAND}
        ${MYSQLD_EXECUTABLE_FOR_INFO_BIN}
        -DCMAKE_BUILD_TYPE=$<CONFIG>
        -P
        ${CMAKE_SOURCE_DIR}/cmake/info_bin.cmake
        BYPRODUCTS
        ${CMAKE_BINARY_DIR}/Docs/INFO_BIN
        WORKING_DIRECTORY
        ${CMAKE_BINARY_DIR}
        COMMENT
        "Generating Docs/INFO_BIN")
# Adding $<TARGET_FILE:mysqld> seems to a dependency on 'mysqld'. Add an explicit one anyways.
if(MYSQLD_EXECUTABLE_FOR_INFO_BIN)
        add_dependencies(INFO_BIN mysqld)
        if(TARGET show_build_id)
                add_dependencies(show_build_id INFO_BIN)
        endif()
endif()

# Packaging
if(WIN32)
        option(BUNDLE_RUNTIME_LIBRARIES "INSTALL runtime libraries" OFF)
        if(BUNDLE_RUNTIME_LIBRARIES)
                set(RUNTIME_LIBRARIES msvcp140 ucrtbase vcruntime140)
                foreach(LIB ${RUNTIME_LIBRARIES})
                        set(LIB_PATH "C:/Windows/System32/${LIB}.dll")
                        set(LIB_D_PATH "C:/Windows/System32/${LIB}d.dll")
                        foreach(LIBRARY_PATH ${LIB_PATH} ${LIB_D_PATH})
                                if(EXISTS "${LIBRARY_PATH}")
                                        message(STATUS "INSTALL ${LIBRARY_PATH} to ${INSTALL_BINDIR}")
                                        install(
                                                FILES "${LIBRARY_PATH}"
                                                DESTINATION "${INSTALL_BINDIR}"
                                                COMPONENT SharedLibraries)
                                else()
                                        message(STATUS "Could not find ${LIBRARY_PATH}")
                                endif()
                        endforeach()
                endforeach()
        endif()
endif()

if(WIN32)
        set(CPACK_GENERATOR "ZIP")
else()
        set(CPACK_GENERATOR "TGZ")
endif()
add_subdirectory(packaging/WiX)
add_subdirectory(packaging/solaris)

# Produce separate tarballs for each INSTALL COMPONENT
if(PACK_SEPARATE_COMPONENTS)
        set(CPACK_ARCHIVE_COMPONENT_INSTALL ON)
        set(CPACK_COMPONENTS_GROUPING IGNORE)
endif()

if(UNIX)
        install(
                FILES Docs/mysql.info
                DESTINATION ${INSTALL_INFODIR}
                OPTIONAL
                COMPONENT Info)
endif()
#
# RPM installs documentation directly from the source tree
#
if(NOT INSTALL_LAYOUT MATCHES "RPM")
        install(
                FILES README LICENSE
                DESTINATION ${INSTALL_DOCREADMEDIR}
                COMPONENT Readme)
        install(
                FILES ${CMAKE_BINARY_DIR}/Docs/INFO_SRC ${CMAKE_BINARY_DIR}/Docs/INFO_BIN
                DESTINATION ${INSTALL_DOCDIR}
                COMPONENT Info)

        # MYSQL_DOCS_LOCATON is used in "make dist", points to the documentation directory
        set(MYSQL_DOCS_LOCATION
            ""
            CACHE PATH "Location from where documentation is copied")
        mark_as_advanced(MYSQL_DOCS_LOCATION)
        install(
                DIRECTORY Docs/
                DESTINATION ${INSTALL_DOCDIR}
                COMPONENT Documentation
                PATTERN "Makefile.*" EXCLUDE
                PATTERN "glibc*" EXCLUDE
                PATTERN "linuxthreads.txt" EXCLUDE
                PATTERN "myisam.txt" EXCLUDE
                PATTERN "mysql.info" EXCLUDE
                PATTERN "sp-imp-spec.txt" EXCLUDE
                PATTERN "README.build" EXCLUDE)
endif()

# Now that we're done with all ADD_SUBDIRECTORY and thus all feature tests, we can safely enable ASan on Clang/Win32 if needed.
if(HAVE_ASAN AND WIN32_CLANG)
        string_append(CMAKE_C_FLAGS " -fsanitize=address -fsanitize-address-use-after-scope")
        string_append(CMAKE_CXX_FLAGS " -fsanitize=address -fsanitize-address-use-after-scope")
endif()

if(UNIX)
        execute_process(
                COMMAND ${CMAKE_COMMAND} -E make_directory library_output_directory
                COMMAND ${CMAKE_COMMAND} -E make_directory plugin_output_directory
                COMMAND ${CMAKE_COMMAND} -E make_directory runtime_output_directory
                WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
        if(SASL_CUSTOM_LIBRARY)
                execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory library_output_directory/sasl2)
        endif()
endif()

# Pre-create directories for library_output_directory and plugin_output_directory, so create_symlink does not fail in ssl.cmake
# Do not pre-create runtime_output_directory/Debug or runtime_output_directory/RelWithDebInfo, mtr gets confused, and must be
# run with --vs-config=Debug or --vs-config=RelWithDebInfo in order to work.
if(APPLE AND NOT BUILD_IS_SINGLE_CONFIG)
        foreach(dir library_output_directory plugin_output_directory)
                execute_process(
                        COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/Debug
                        COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/RelWithDebInfo
                        COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/Release
                        COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/MinSizeRel
                        WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
        endforeach()
endif()

if(UNIX)
        # Create convenience symlinks bin/ and lib/
        execute_process(
                COMMAND ${CMAKE_COMMAND} -E create_symlink ./runtime_output_directory bin
                COMMAND ${CMAKE_COMMAND} -E create_symlink ./library_output_directory lib
                WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
        # See ./mysql-test/suite/innodb_fts/include/mecab.inc
        if(MECAB_IPADIC_PARENT)
                execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink "${MECAB_IPADIC_PARENT}" mecab
                                WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/library_output_directory)
        endif()
endif()

# Without any --plugin-dir=<directory name> the server will look in INSTALL_PLUGINDIR, which is "lib/plugin" for standalone
# builds. This means that by default, we create a symlink plugin@ in lib/. For other values of INSTALL_LAYOUT we split the
# INSTALL_PLUGINDIR path, create the necessary directories, and add a symlink `basename ${INSTALL_PLUGINDIR}`.
if(UNIX AND BUILD_IS_SINGLE_CONFIG)
        get_filename_component(INSTALL_PLUGINDIR_PATH ${INSTALL_PLUGINDIR} PATH)
        get_filename_component(INSTALL_PLUGINDIR_NAME ${INSTALL_PLUGINDIR} NAME)
        if(NOT IS_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH})
                # Create the given directories and their parents as needed.
                message(STATUS "MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH}")
                file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH})
        endif()

        execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink ${CMAKE_BINARY_DIR}/plugin_output_directory
                                ${INSTALL_PLUGINDIR_NAME} WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH})
endif()

set(DOXYGEN_OUTPUT_DIR "${CMAKE_CURRENT_BINARY_DIR}/doxygen")
find_package(Doxygen)
if(DOXYGEN_FOUND)
        # We want output on stdout in pushbuild.
        if(DEFINED ENV{PB2WORKDIR})
                set(REDIRECT_DOXYGEN_STDOUT_DEFAULT FALSE)
        else()
                set(REDIRECT_DOXYGEN_STDOUT_DEFAULT TRUE)
        endif()
        option(REDIRECT_DOXYGEN_STDOUT "Redirect doxygen output to a file" ${REDIRECT_DOXYGEN_STDOUT_DEFAULT})

        configure_file(Doxyfile.in Doxyfile @ONLY)
        add_custom_target(
                doxygen
                COMMAND
                        ${CMAKE_COMMAND} -DDOXYGEN_EXECUTABLE=${DOXYGEN_EXECUTABLE}
                        -DDOXYGEN_DOT_EXECUTABLE=${DOXYGEN_DOT_EXECUTABLE} -DDOXYFILE=${CMAKE_BINARY_DIR}/Doxyfile
                        -DERROR_FILE=${CMAKE_BINARY_DIR}/doxyerror.log -DOUTPUT_FILE=${CMAKE_BINARY_DIR}/doxyoutput.log
                        -DREDIRECT_DOXYGEN_STDOUT=${REDIRECT_DOXYGEN_STDOUT} -DSOURCE_DIR=${CMAKE_SOURCE_DIR}
                        -DTOFIX_FILE=${CMAKE_BINARY_DIR}/tofix-all.log
                        -DREGRESSION_FILE=${CMAKE_BINARY_DIR}/tofix-regressions.log
                        -DIGNORE_FILE=${CMAKE_SOURCE_DIR}/Doxyfile-ignored -P ${CMAKE_SOURCE_DIR}/run_doxygen.cmake
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
                COMMENT "Generating Doxygen documentation"
                VERBATIM)
        set_property(TARGET doxygen PROPERTY EXCLUDE_FROM_ALL TRUE)
        if(WIN32)
                set_property(TARGET doxygen PROPERTY EXCLUDE_FROM_DEFAULT_BUILD TRUE)
        endif()
endif(DOXYGEN_FOUND)

include(CPack)

# C/CXX compiler flags consist of: CPPFLAGS        Taken from environment, see above. ADD_DEFINITIONS In each individual
# CMakeLists.txt CMAKE_CXX_FLAGS From command line. We extend these in maintainer.cmake We extend these in
# compiler_options.cmake ENV{CXXFLAGS}   From environment, but environment is ignored if CMAKE_CXX_FLAGS is also given on
# command line CMAKE_CXX_FLAGS_${CMAKE_BUILD_TYPE}, typical initial cmake values: DEBUG          -g RELWITHDEBINFO -O2 -g
# -DNDEBUG RELEASE        -O3 -DNDEBUG MINSIZEREL     -Os -DNDEBUG
#
# The compiler command line is g++ DEFINITIONS CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_<configuration>
#
# So, if you want to use '-O3' for a RELWITHDEBINFO build, you should do: cmake -DCMAKE_CXX_FLAGS_RELWITHDEBINFO="-O3 -g
# -DNDEBUG"
#
# Note that CMakeCache.txt contains cmake builtins for these variables, *not* the values that will actually be used, which we
# report here:

if(BUILD_IS_SINGLE_CONFIG)
        message(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")
endif()
get_property(
        cwd_definitions
        DIRECTORY
        PROPERTY COMPILE_DEFINITIONS)
message(STATUS "COMPILE_DEFINITIONS: ${cwd_definitions}")
report_cxx_flags()
message(STATUS "CMAKE_C_LINK_FLAGS: ${CMAKE_C_LINK_FLAGS}")
message(STATUS "CMAKE_CXX_LINK_FLAGS: ${CMAKE_CXX_LINK_FLAGS}")
message(STATUS "CMAKE_EXE_LINKER_FLAGS ${CMAKE_EXE_LINKER_FLAGS}")
message(STATUS "CMAKE_MODULE_LINKER_FLAGS ${CMAKE_MODULE_LINKER_FLAGS}")
message(STATUS "CMAKE_SHARED_LINKER_FLAGS ${CMAKE_SHARED_LINKER_FLAGS}")

# If user provided WITH_NDBCLUSTER keep it in cache
if(HAVE_WITH_NDBCLUSTER)
        set(WITH_NDBCLUSTER
            ${WITH_NDB}
            CACHE BOOL "Deprecated legacy option for MySQL Cluster")
endif()

```








