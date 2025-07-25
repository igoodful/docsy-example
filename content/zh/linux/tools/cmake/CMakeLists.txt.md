---
title: CMakeLists.txt
description: CMakeLists.txt
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [cmake 官网](https://cmake.org/download/)
- [cmake doc](https://cmake.org/cmake/help/latest/)

**CmakeLists.txt**：是cmake的正统文件
**xxx.cmake：**：是一个模块文件，可以被include到CMakeLists.txt中
**编译目标**：一般来说，编译目标(target)的类型一般有静态库、动态库和可执行文件。


{{%/pageinfo%}}
这时编写CMakeLists.txt主要包括两步：
1. 编译：确定编译目标所需要的源文件
2. 链接：确定链接的时候需要依赖的额外的库


{{<note>}}
<!---->

{{</note>}}

```bash
yum -y install cmake3
cmake3 --version
```

### cmake 基础

#### install
参数
1. RUNTIME DESTINATION 指定可执行文件
2. LIBRARY DESTINATION 定库文件
3. ARCHIVE DESTINATION 指定归档文件

```cmake
install(TARGETS math demo RUNTIME DESTINATION bin LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)

file(GLOB_RECURSE MATH_LIB_HEADERS src/c/math/*.h)
install(FILES ${MATH_LIB_HEADERS} DESTINATION include/math)
```
如果指定CMAKE_INSTALL_PREFIX为/usr/local，那么math库将会被安装到路径/usr/local/lib/目录下；而demo可执行文件则在/usr/local/bin目录下。CMAKE_INSTALL_PREFIX在不同的系统上有不同的默认值，使用的时候最好显式指定路径。

#### include
> include指令一般用于语句的复用，如果有一些语句需要在很多CMakeLists.txt文件中使用，为避免重复编写，可以将其写在.cmake文件中，然后在需要的CMakeLists.txt文件中进行include操作就行了。

include(<file|module> [OPTIONAL] [RESULT_VARIABLE <var>] [NO_POLICY_SCOPE])

include(file|module)


#### cmake 内置变量







```makefile
[root@k8s-node-70 build]# cmake3 ../cm/
-- The C compiler identification is GNU 4.8.5
-- The CXX compiler identification is GNU 4.8.5
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- CMAKE_INSTALL_PREFIX=/usr/local
-- CMAKE_VERSION=3.17.5
-- CMAKE_MAJOR_VERSION=3
-- CMAKE_MINOR_VERSION=17
-- CMAKE_PATCH_VERSION=5
-- PROJECT_NAME=ProjectName
-- PROJECT_BINARY_DIR=/root/test/build
-- PROJECT_SOURCE_DIR=/root/test/cm
-- CMAKE_CURRENT_SOURCE_DIR=/root/test/cm
-- CMAKE_BUILD_TYPE=
-- BUILD_SHARED_LIBS=
-- EXECUTABLE_OUTPUT_PATH=
-- LIBRARY_OUTPUT_PATH=
-- CMAKE_C_COMPILER=/usr/bin/cc
-- CMAKE_CXX_COMPILER=/usr/bin/c++
-- CMAKE_C_FLAGS=
-- CMAKE_CXX_FLAGS=
-- CMAKE_SYSTEM=Linux-3.10.0-957.el7.x86_64
-- CAMKE_SYSTEM_NAME=
-- CMAKE_SYSTEM_VERSION=3.10.0-957.el7.x86_64
-- CMAKE_SYSTEM_PROCESSOR=x86_64
-- UNIX=1
-- WIN32=
-- CMAKE_INCLUDE_PATH=
-- CMAKE_LIBRARY_PATH=
-- CMAKE_CURRENT_BINARY_DIR=/root/test/build
-- CMAKE_PREFIX_PATH=
-- CMAKE_MODULE_PATH=
-- CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS=
-- Configuring done
-- Generating done
-- Build files have been written to: /root/test/build

```




#### -D定义变量
```makefile
cmake3  -DCMAKE_BUILD_TYPE=VALUE -DYES -DVAR_NAME:PATH=/path/to/dir

```


#### 布尔变量：option
**语法**：`option(<var> "<help_text>" [value])`
- variable 选项名
- help_text 描述、解释、备注
- value 选项初始化值（除显示指定为ON而外全为OFF）

**优先级**：
1. 如果出现同名的变量（普通或缓存变量），则option会被忽略
2. CMake命令中通过-D预设同名变量，则option也会被忽略
3. 修改option选项值，建议通过缓存变量方式修改



```cmake
option(AA "AA" ON)

option(BB "BB")

option(CC "CC" google)

```

**举例说明**：使用option进行预设变量的默认值，如果需要更改就使用命令行方式覆盖，MySQL的cmake文件就是这样处理
1. 命令行设置：`cmake -DWITH_JEMALLOC=on`
2. option设置：`WITH_JEMALLOC`
```cmake
OPTION(WITH_JEMALLOC "Use jemalloc rather than builtin malloc/free etc." OFF)
IF(WITH_JEMALLOC)
  FIND_MALLOC_LIBRARY(jemalloc)
ENDIF()
```




#### 查找可执行程序：find_program

```cmake
find_program(MY_GCC gcc PATH)
if(MY_GCC)
    message(STATUS "gcc FOUND ${MY_GCC}")
else()
    message(STATUS "gcc NOT FOUND")
endif()

```
#### 运行任意命令：execute_process
execute_process(COMMAND <cmd1> [<arguments>]
                [COMMAND <cmd2> [<arguments>]]...
                [WORKING_DIRECTORY <directory>]
                [TIMEOUT <seconds>]
                [RESULT_VARIABLE <variable>]
                [RESULTS_VARIABLE <variable>]
                [OUTPUT_VARIABLE <variable>]
                [ERROR_VARIABLE <variable>]
                [INPUT_FILE <file>]
                [OUTPUT_FILE <file>]
                [ERROR_FILE <file>]
                [OUTPUT_QUIET]
                [ERROR_QUIET]
                [COMMAND_ECHO <where>]
                [OUTPUT_STRIP_TRAILING_WHITESPACE]
                [ERROR_STRIP_TRAILING_WHITESPACE]
                [ENCODING <name>]
                [ECHO_OUTPUT_VARIABLE]
                [ECHO_ERROR_VARIABLE]
                [COMMAND_ERROR_IS_FATAL <ANY|LAST>])




#### macro
macro中定义的变量则在整个CMakeLists.txt文件中有效
- 参数传递：文本替换方式
- 作用域：全局

定义和调用
```cmake
cmake_minimum_required(VERSION 3.5.1)
project(mysqld)

```




#### function
function中定义的变量只在function中有效
- 参数传递：传值方式
- 作用域：局部

定义和调用
```cmake
# 定义
function(add_numbers num1 num2)
  math(EXPR result "${num1} + ${num2}")
  message("The result is ${result}")
endfunction()

# 使用
add_numbers(1 2)

```




```cmake
cmake_minimum_required(VERSION 3.5.1)
project(mysqld)

```



























### 主CMakeLists.txt
```makefile
# Copyright (c) 2006, 2023, Oracle and/or its affiliates.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License, version 2.0,
# as published by the Free Software Foundation.
#
# This program is also distributed with certain software (including
# but not limited to OpenSSL) that is licensed under separate terms,
# as designated in a particular file or component or in included license
# documentation.  The authors of MySQL hereby grant you an additional
# permission to link the program and your derivative works with the
# separately licensed software that they have included with MySQL.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License, version 2.0, for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA 02110-1301  USA

MESSAGE(STATUS "Running cmake version ${CMAKE_VERSION}")

IF(WIN32)
  # Load policies. This is needed in order to parse the wmic version check.
  CMAKE_MINIMUM_REQUIRED(VERSION 3.5.1)
  EXECUTE_PROCESS(COMMAND wmic os get version
    OUTPUT_VARIABLE NT_RELEASE_VERSION
    OUTPUT_STRIP_TRAILING_WHITESPACE
    RESULT_VARIABLE WMIC_RESULT
    )
  IF(WMIC_RESULT EQUAL 0)
    STRING(REPLACE "\r" "" NT_RELEASE_VERSION "${NT_RELEASE_VERSION}")
    STRING(REPLACE "\n" "" NT_RELEASE_VERSION "${NT_RELEASE_VERSION}")
    STRING(REGEX MATCH "Version[ ]+([0-9\.]+)" V_NUM "${NT_RELEASE_VERSION}")
    MESSAGE(STATUS "NT_RELEASE_VERSION is ${NT_RELEASE_VERSION}")
    IF(CMAKE_MATCH_1)
      IF(CMAKE_MATCH_1 VERSION_LESS "10")
        MESSAGE(FATAL_ERROR
          "Need at least Windows Server 2016, or Windows 10, to build")
      ENDIF()
    ENDIF()
  ENDIF()
  IF(CMAKE_GENERATOR MATCHES "Visual Studio" AND CMAKE_GENERATOR MATCHES "2019")
    IF(CMAKE_VERSION MATCHES "MSVC")
      # It is the bundled version, ignore version check,
      # (although this seems to be buggy too).
    ELSE()
      # Bug in msbuild, install the latest in the 3.15 series as a workaround.
      # https://gitlab.kitware.com/cmake/cmake/issues/19303
      # custom commands are re-built every time
      CMAKE_MINIMUM_REQUIRED(VERSION 3.15.3)
    ENDIF()
  ENDIF()
ELSEIF(APPLE)
  # Version 3.12.4 is needed because the new build system of Xcode is not
  # supported by cmake. 3.12.4 will force using the legacy build system.
  # Version 3.9.2 is needed because INCLUDE_DIRECTORIES(SYSTEM ...) wasn't
  # handled properly by the cmake Xcode generator.
  # NOTE:
  # cmake >= 3.19 will use the new build system by default for Xcode >= 12.x
  IF(CMAKE_GENERATOR STREQUAL "Xcode")
    SET(APPLE_XCODE 1)
    CMAKE_MINIMUM_REQUIRED(VERSION 3.12.4)
  ELSE()
    CMAKE_MINIMUM_REQUIRED(VERSION 3.9.2)
  ENDIF()
  # If this is macOS 11, we need cmake 3.18
  # System libraries like
  #    /usr/lib/libresolv.dylib
  # are no longer present in the file system.
  # cmake >= 3.18 will look for .tbd files in the SDK instead
  # So we end up linking with:
  #    /Applications/Xcode.app/.../usr/lib/libresolv.tbd
  # We must postpone the version test until we have called 'uname -r' below.
ELSEIF(UNIX)
  # This is currently minimum version on all supported platforms.
  IF(CMAKE_VERSION VERSION_LESS 3.5.1)
    # Default cmake is 2.8.12.2 on RedHat
    IF(EXISTS "/etc/redhat-release")
      MESSAGE(WARNING "Please use cmake3 rather than cmake on this platform")
      FIND_PROGRAM(MY_CMAKE3 cmake3 /bin /usr/bin /usr/local/bin)
      IF(MY_CMAKE3)
        MESSAGE(STATUS "Found ${MY_CMAKE3}")
      ELSE()
        MESSAGE(STATUS "Please install cmake3 (yum install cmake3)")
      ENDIF()
    ELSE()
      # On SunOS /opt/csw/bin/cmake is (most likely) too old.
      FIND_PROGRAM(MY_UNAME uname /bin /usr/bin /usr/local/bin /sbin)
      IF(MY_UNAME)
        EXEC_PROGRAM(uname ARGS -s OUTPUT_VARIABLE MY_HOST_SYSTEM_NAME)
        IF(MY_HOST_SYSTEM_NAME MATCHES "SunOS")
          FIND_PROGRAM(MY_CMAKE cmake /usr/bin
            NO_CMAKE_ENVIRONMENT_PATH
            NO_SYSTEM_ENVIRONMENT_PATH
            )
          IF(MY_CMAKE)
            MESSAGE(STATUS "Found ${MY_CMAKE}")
            EXEC_PROGRAM(${MY_CMAKE} ARGS --version)
          ELSE()
            MESSAGE(STATUS "Please install /usr/bin/cmake ")
          ENDIF()
        ENDIF()
      ENDIF()
    ENDIF()
  ENDIF()
ENDIF()

# CMake 3.5 is needed for TARGET_SOURCES(... $<TARGET_OBJECTS:${LIB}_objlib>)
CMAKE_MINIMUM_REQUIRED(VERSION 3.5.1)

# Will set GIT_EXECUTABLE and GIT_FOUND
FIND_PACKAGE(Git)

SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

# First, decide about build type (debug or release)
# If cmake is invoked with -DCMAKE_BUILD_TYPE,
# respect user wishes and do not (re)define CMAKE_BUILD_TYPE. If WITH_DEBUG
# is given, set CMAKE_BUILD_TYPE = Debug. Otherwise, use Relwithdebinfo.

IF(DEFINED CMAKE_BUILD_TYPE)
  SET(HAVE_CMAKE_BUILD_TYPE TRUE)
ENDIF()

OPTION(WITH_DEBUG "Use dbug/safemutex" OFF)
OPTION(CHECK_ERRMSG_FORMAT "Check printf format for English error messages" OFF)

# Use a default manufacturer if no manufacturer was identified.
SET(MANUFACTURER_DOCSTRING
  "Set the entity that appears as the manufacturer of packages that support a manufacturer field.")
IF(NOT DEFINED MANUFACTURER)
  SET(MANUFACTURER "Built from Source" CACHE  STRING ${MANUFACTURER_DOCSTRING})
  MARK_AS_ADVANCED(MANUFACTURER)
ENDIF()


# MAX_INDEXES - Set the maximum number of indexes per table, default 64U
IF (NOT MAX_INDEXES)
  SET(MAX_INDEXES 64U)
ELSEIF(MAX_INDEXES MATCHES "^[0-9]+[Uu]?$")
  # MAX_INDEXES should be unsigned, so add the U suffix if it's missing.
  STRING(REGEX REPLACE "^([0-9]+).*$" "\\1U" MAX_INDEXES "${MAX_INDEXES}")
  MESSAGE(STATUS "Configuring with MAX_INDEXES = ${MAX_INDEXES}")
ELSE()
  MESSAGE(FATAL_ERROR "MAX_INDEXES should be an unsigned integer.")
ENDIF(NOT MAX_INDEXES)

IF(MAX_INDEXES GREATER 255)
  MESSAGE(FATAL_ERROR "MAX_INDEXES values greater than 255 is not supported!")
ELSEIF(MAX_INDEXES LESS 64)
  # Per documentation, ignore values less than 64 and use the default instead.
  MESSAGE(WARNING "MAX_INDEXES option ignored because it is less than 64.")
  SET(MAX_INDEXES 64U)
ENDIF()

# We choose to provide WITH_DEBUG as alias to standard CMAKE_BUILD_TYPE=Debug
# which turns out to be not trivial, as this involves synchronization
# between CMAKE_BUILD_TYPE and WITH_DEBUG. Besides, we have to deal with cases
# where WITH_DEBUG is  reset from ON to OFF  and here we need to reset
# CMAKE_BUILD_TYPE to either none or default RelWithDebInfo

SET(BUILDTYPE_DOCSTRING
 "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or
 CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel")

IF(WITH_DEBUG)
  SET(CMAKE_BUILD_TYPE "Debug" CACHE STRING ${BUILDTYPE_DOCSTRING} FORCE)
  SET(OLD_WITH_DEBUG 1 CACHE INTERNAL "" FORCE)
ELSEIF(NOT HAVE_CMAKE_BUILD_TYPE OR OLD_WITH_DEBUG)
  IF(CMAKE_BUILD_TYPE MATCHES "Debug" OR NOT HAVE_CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING
       ${BUILDTYPE_DOCSTRING} FORCE)
  ENDIF()
  SET(OLD_WITH_DEBUG 0 CACHE INTERNAL "" FORCE)
ENDIF()

STRING(TOUPPER "${CMAKE_BUILD_TYPE}" CMAKE_BUILD_TYPE_UPPER)

IF(CMAKE_GENERATOR MATCHES "Visual Studio [1-9][0-9].*" AND
   CMAKE_GENERATOR_TOOLSET STREQUAL "")
  # Switch to 64 bit toolset on Windows (32 bit is default).
  # This is recommended as the 32 bit linker will run into address space issues
  # and not exit for long time.
  SET(CMAKE_GENERATOR_TOOLSET "host=x64")
ENDIF()

# On Linux el6/el7 the default gcc is too old, see if devtoolset is installed.
# Same with SLES 12, look for gcc 7 there.
# We need to look for gcc before calling PROJECT below.
OPTION(FORCE_UNSUPPORTED_COMPILER "Disable compiler version checks" OFF)
MARK_AS_ADVANCED(WITHOUT_SERVER FORCE_UNSUPPORTED_COMPILER)

# Use 'uname -r' and 'rpm -qf /' to figure out host system.
# For Docker images we cannot trust uname, so use rpm instead.
IF(UNIX)
  FIND_PROGRAM(MY_UNAME uname /bin /usr/bin /usr/local/bin /sbin)
  IF(MY_UNAME)
    EXECUTE_PROCESS(COMMAND ${MY_UNAME} -s
      OUTPUT_VARIABLE MY_HOST_SYSTEM_NAME
      OUTPUT_STRIP_TRAILING_WHITESPACE
      RESULT_VARIABLE MY_UNAME_RESULT
      )
    EXECUTE_PROCESS(COMMAND ${MY_UNAME} -m
      OUTPUT_VARIABLE MY_HOST_MACHINE_NAME
      OUTPUT_STRIP_TRAILING_WHITESPACE
      RESULT_VARIABLE MY_UNAME_MACHINE_RESULT
      )
    EXEC_PROGRAM(${MY_UNAME} ARGS -r OUTPUT_VARIABLE MY_HOST_SYSTEM_VERSION)
  ENDIF()
  FIND_PROGRAM(MY_DPKG_BUILDFLAGS dpkg-buildflags /bin /usr/bin)
  FIND_PROGRAM(MY_RPM rpm /bin /usr/bin)
  IF(MY_RPM)
    EXECUTE_PROCESS(COMMAND ${MY_RPM} -qf /
      OUTPUT_VARIABLE MY_HOST_FILESYSTEM_NAME
      OUTPUT_STRIP_TRAILING_WHITESPACE
      RESULT_VARIABLE MY_RPM_RESULT
      )
  ENDIF()
ENDIF()

# See comments above, about CMAKE_MINIMUM_REQUIRED VERSION and macOS 11.
IF(APPLE)
  IF(MY_HOST_SYSTEM_VERSION VERSION_GREATER_EQUAL 20)
    CMAKE_MINIMUM_REQUIRED(VERSION 3.18)
  ENDIF()
ENDIF()

# Add all policies *after* CMAKE_MINIMUM_REQUIRED
# Repeating CMAKE_MINIMUM_REQUIRED here will load defaults for 3.5.1
CMAKE_MINIMUM_REQUIRED(VERSION 3.5.1)
INCLUDE(cmake_policies NO_POLICY_SCOPE)

MACRO(STRING_APPEND STRING_VAR INPUT)
  SET(${STRING_VAR} "${${STRING_VAR}}${INPUT}")
ENDMACRO()

MACRO(STRING_PREPEND STRING_VAR INPUT)
  SET(${STRING_VAR} "${INPUT}${${STRING_VAR}}")
ENDMACRO()

IF(MY_HOST_SYSTEM_NAME MATCHES "SunOS")
  SET(SOLARIS 1)
ENDIF()

IF(MY_HOST_SYSTEM_NAME MATCHES "Linux")
  # Trust 'rpm -qf /' rather than 'uname -s'
  STRING(REGEX MATCH "\\.el([6789])\\." MATCH_FSYS "${MY_HOST_FILESYSTEM_NAME}")

  # Set LINUX_RHEL6, LINUX_RHEL7, LINUX_RHEL8 or LINUX_RHEL9
  IF(CMAKE_MATCH_1)
    SET(LINUX_RHEL 1)
    SET(LINUX_RHEL${CMAKE_MATCH_1} 1)
  ENDIF()
ENDIF()

IF(NOT LINUX_RHEL AND MY_HOST_SYSTEM_NAME MATCHES "Linux")
  IF(EXISTS "/etc/os-release")
    FILE(READ "/etc/os-release" MY_OS_RELEASE)
    IF(MY_OS_RELEASE MATCHES "Debian")
      SET(LINUX_DEBIAN 1)
    ELSEIF(MY_OS_RELEASE MATCHES "Ubuntu")
      SET(LINUX_UBUNTU 1)
      IF(MY_OS_RELEASE MATCHES "16.04")
        SET(LINUX_UBUNTU_16_04 1)
      ELSEIF(MY_OS_RELEASE MATCHES "18.04")
        SET(LINUX_UBUNTU_18_04 1)
      ENDIF()
      # /etc/os-release contains a line like
      # VERSION_ID="20.04"
      # Match the numeric value, including the dot, ignore the rest:
      STRING(REGEX MATCH
        "VERSION_ID=\"([0-9\\.]+).*" UNUSED ${MY_OS_RELEASE})
      IF(CMAKE_MATCH_1)
        SET(LINUX_UBUNTU_VERSION_ID ${CMAKE_MATCH_1})
      ENDIF()
    ENDIF()
  ENDIF()
ENDIF()

IF(EXISTS "/etc/SuSE-release")
  SET(LINUX_SUSE 1)
ENDIF()

IF(LINUX_SUSE)
  FILE(READ "/etc/SuSE-release" MY_OS_RELEASE)
  IF(MY_OS_RELEASE MATCHES "SUSE Linux Enterprise Server 12")
    SET(LINUX_SUSE_12 1)
  ELSEIF(MY_OS_RELEASE MATCHES "SUSE Linux Enterprise Server 15" OR
      MY_OS_RELEASE MATCHES "openSUSE .* 15")
    SET(LINUX_SUSE_15 1)
  ELSE()
    MESSAGE(WARNING "Unknown SUSE version.")
  ENDIF()
ENDIF()

# In case we pick up values form the CACHE or from the command line.
# See invokation of ADD_LINUX_RPM_FLAGS below.
SET(INITIAL_CMAKE_C_FLAGS)
SET(INITIAL_CMAKE_CXX_FLAGS)
IF(DEFINED CMAKE_C_FLAGS)
  SET(INITIAL_CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
ENDIF()
IF(DEFINED CMAKE_CXX_FLAGS)
  SET(INITIAL_CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
ENDIF()

IF(CMAKE_HOST_UNIX AND NOT FORCE_UNSUPPORTED_COMPILER
    AND NOT CMAKE_C_COMPILER AND NOT CMAKE_CXX_COMPILER)
  # Cannot INCLUDE(CMakeDetermineSystem) prior to PROJECT initialization below.
  SET (ENV_CC "$ENV{CC}")
  SET (ENV_CXX "$ENV{CXX}")
  IF (ENV_CC STREQUAL "" AND ENV_CXX STREQUAL "")
    IF(LINUX_RHEL)
      MESSAGE(STATUS "This is ${MATCH_FSYS} as found from 'rpm -qf /'")
    ENDIF()
    IF(LINUX_RHEL)
      MESSAGE(STATUS "Looking for a devtoolset compiler")
      IF(LINUX_RHEL6)
        SET(ALTERNATIVE_PATHS "/opt/rh/devtoolset-8")
      ELSEIF(LINUX_RHEL7)
        # gcc11 not available yet
        IF(MY_HOST_MACHINE_NAME MATCHES "aarch64")
          SET(ALTERNATIVE_PATHS "/opt/rh/devtoolset-10")
        ELSE()
          SET(ALTERNATIVE_PATHS "/opt/rh/devtoolset-11")
        ENDIF()
      ELSEIF(LINUX_RHEL8 OR LINUX_RHEL9)
        SET(ALTERNATIVE_PATHS "/opt/rh/gcc-toolset-12")
      ENDIF()

      FOREACH(OPT_PATH ${ALTERNATIVE_PATHS})
        FIND_PROGRAM(ALTERNATIVE_GCC gcc
          NO_DEFAULT_PATH
          PATHS "${OPT_PATH}/root/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_GPP g++
          NO_DEFAULT_PATH
          PATHS "${OPT_PATH}/root/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_LD ld
          NO_DEFAULT_PATH
          PATHS "${OPT_PATH}/root/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_AR ar
          NO_DEFAULT_PATH
          PATHS "${OPT_PATH}/root/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_RANLIB ranlib
          NO_DEFAULT_PATH
          PATHS "${OPT_PATH}/root/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_ENABLE enable
          NO_DEFAULT_PATH
          PATHS "${OPT_PATH}")
      ENDFOREACH()
      # A missing ALTERNATIVE_LD may generate bad executables.
      IF(ALTERNATIVE_GCC AND ALTERNATIVE_GPP AND ALTERNATIVE_LD)
        # Set correct search path for executables, libraries, and data files.
        GET_FILENAME_COMPONENT(GCC_B_PREFIX ${ALTERNATIVE_GCC} DIRECTORY)
        STRING_PREPEND(CMAKE_C_FLAGS "-B${GCC_B_PREFIX} ")
        GET_FILENAME_COMPONENT(GPP_B_PREFIX ${ALTERNATIVE_GPP} DIRECTORY)
        STRING_PREPEND(CMAKE_CXX_FLAGS "-B${GPP_B_PREFIX} ")

        SET(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
        SET(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
        SET(CMAKE_LINKER ${ALTERNATIVE_LD})
        SET(CMAKE_LINKER ${ALTERNATIVE_LD} CACHE PATH "Alternative ld")
        IF(ALTERNATIVE_AR)
          SET(CMAKE_AR ${ALTERNATIVE_AR})
          SET(CMAKE_AR ${ALTERNATIVE_AR} CACHE PATH "Alternative ar")
        ENDIF()
        IF(ALTERNATIVE_RANLIB)
          SET(CMAKE_RANLIB ${ALTERNATIVE_RANLIB})
          SET(CMAKE_RANLIB ${ALTERNATIVE_RANLIB} CACHE PATH "Alternative ranlib")
        ENDIF()
        MESSAGE(STATUS "Using ${ALTERNATIVE_GCC}")
        MESSAGE(STATUS "Using ${ALTERNATIVE_GPP}")
      ELSE()
        IF(LINUX_RHEL6)
          SET(DEV_PACKAGES
            "devtoolset-8-gcc devtoolset-8-gcc-c++ devtoolset-8-binutils")
        ELSEIF(LINUX_RHEL7)
          IF(MY_HOST_MACHINE_NAME MATCHES "aarch64")
            SET(DEV_PACKAGES
              "devtoolset-10-gcc devtoolset-10-gcc-c++ devtoolset-10-binutils")
          ELSE()
            SET(DEV_PACKAGES
              "devtoolset-11-gcc devtoolset-11-gcc-c++ devtoolset-11-binutils")
          ENDIF()
        ELSEIF(LINUX_RHEL8 OR LINUX_RHEL9)
          SET(DEV_PACKAGES
            "gcc-toolset-12-gcc gcc-toolset-12-gcc-c++ gcc-toolset-12-binutils")
          STRING_APPEND(DEV_PACKAGES " gcc-toolset-12-annobin-annocheck")
          STRING_APPEND(DEV_PACKAGES " gcc-toolset-12-annobin-plugin-gcc")
        ENDIF()
        MESSAGE(WARNING
          "Could not find devtoolset compiler/linker in ${ALTERNATIVE_PATHS}")
        MESSAGE(WARNING "You need to install the required packages:\n"
          " yum install ${DEV_PACKAGES}\n")
        MESSAGE(FATAL_ERROR
          "Or you can set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
      ENDIF()

    ELSEIF(LINUX_UBUNTU_18_04)
      MESSAGE(STATUS "Looking for gcc-8 on Ubuntu 18.04")
      FIND_PROGRAM(ALTERNATIVE_GCC gcc-8
        NO_DEFAULT_PATH
        PATHS "/usr/bin")
      FIND_PROGRAM(ALTERNATIVE_GPP g++-8
        NO_DEFAULT_PATH
        PATHS "/usr/bin")
      FIND_PROGRAM(GCC_AR_EXECUTABLE gcc-ar-8
        NO_DEFAULT_PATH
        PATHS "/usr/bin")
      FIND_PROGRAM(GCC_RANLIB_EXECUTABLE gcc-ranlib-8
        NO_DEFAULT_PATH
        PATHS "/usr/bin")
      IF(GCC_AR_EXECUTABLE)
        SET(CMAKE_AR ${GCC_AR_EXECUTABLE})
        SET(CMAKE_AR ${GCC_AR_EXECUTABLE} CACHE PATH "Alternative ar")
      ENDIF()
      IF(GCC_RANLIB_EXECUTABLE)
        SET(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE})
        SET(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE} CACHE PATH "Alternative ranlib")
      ENDIF()
      IF (ALTERNATIVE_GCC AND ALTERNATIVE_GPP)
        SET(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
        SET(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
        MESSAGE(STATUS "Using ${ALTERNATIVE_GCC}")
        MESSAGE(STATUS "Using ${ALTERNATIVE_GPP}")
      ELSE()
        MESSAGE(WARNING "Could not find gcc-8")
        MESSAGE(FATAL_ERROR
          "Please do 'apt install gcc-8 g++-8'\n"
          "or set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
      ENDIF()

    ELSEIF(LINUX_SUSE_12 OR LINUX_SUSE_15)
      MESSAGE(STATUS "We need to look for a newer GCC on SUSE Linux.")
      IF(LINUX_SUSE_12)
        FIND_PROGRAM(ALTERNATIVE_GCC gcc-10
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_GPP g++-10
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
        FIND_PROGRAM(GCC_AR_EXECUTABLE gcc-ar-10
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
        FIND_PROGRAM(GCC_RANLIB_EXECUTABLE gcc-ranlib-10
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
      ELSEIF(LINUX_SUSE_15)
        FIND_PROGRAM(ALTERNATIVE_GCC gcc-9
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
        FIND_PROGRAM(ALTERNATIVE_GPP g++-9
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
        FIND_PROGRAM(GCC_AR_EXECUTABLE gcc-ar-9
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
        FIND_PROGRAM(GCC_RANLIB_EXECUTABLE gcc-ranlib-9
          NO_DEFAULT_PATH
          PATHS "/usr/bin")
      ENDIF()
      IF(GCC_AR_EXECUTABLE)
        SET(CMAKE_AR ${GCC_AR_EXECUTABLE})
        SET(CMAKE_AR ${GCC_AR_EXECUTABLE} CACHE PATH "Alternative ar")
      ENDIF()
      IF(GCC_RANLIB_EXECUTABLE)
        SET(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE})
        SET(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE} CACHE PATH "Alternative ranlib")
      ENDIF()
      IF (ALTERNATIVE_GCC AND ALTERNATIVE_GPP)
        SET(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
        SET(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
        MESSAGE(STATUS "Using ${ALTERNATIVE_GCC}")
        MESSAGE(STATUS "Using ${ALTERNATIVE_GPP}")
        # Use the new ABI so that std::string can be used with allocators
        # that are not default-constructible (e.g. Memroot_allocator)
        IF(LINUX_SUSE_12)
          ADD_DEFINITIONS(-D_GLIBCXX_USE_CXX11_ABI=1)
        ENDIF()
      ELSE()
        MESSAGE(WARNING "Could not find newer gcc.")
        IF(LINUX_SUSE_12)
          MESSAGE(FATAL_ERROR "Please do zypper install gcc10 gcc10-c++\n"
            "or set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
        ELSE()
          MESSAGE(FATAL_ERROR "Please do zypper install gcc9 gcc9-c++\n"
            "or set CMAKE_C_COMPILER and CMAKE_CXX_COMPILER explicitly.")
        ENDIF()
      ENDIF()
    ELSEIF(SOLARIS)
      MESSAGE(STATUS "Looking for GCC 10 on Solaris.")
      FIND_PROGRAM(ALTERNATIVE_GCC gcc
        NO_DEFAULT_PATH
        PATHS "/usr/gcc/10/bin")
      FIND_PROGRAM(ALTERNATIVE_GPP g++
        NO_DEFAULT_PATH
        PATHS "/usr/gcc/10/bin")
      IF (ALTERNATIVE_GCC AND ALTERNATIVE_GPP)
        SET(CMAKE_C_COMPILER ${ALTERNATIVE_GCC})
        SET(CMAKE_CXX_COMPILER ${ALTERNATIVE_GPP})
        MESSAGE(STATUS "Using ${ALTERNATIVE_GCC}")
        MESSAGE(STATUS "Using ${ALTERNATIVE_GPP}")
      ELSE()
        MESSAGE(WARNING "Could not find /usr/gcc/10/bin/gcc")
      ENDIF()
    ENDIF()
  ENDIF()
ENDIF()

# Optionally set project name, e.g.
# foo.xcodeproj (mac) or foo.sln (windows)
SET(MYSQL_PROJECT_NAME_DOCSTRING "MySQL project name")
IF(DEFINED MYSQL_PROJECT_NAME)
  SET(MYSQL_PROJECT_NAME ${MYSQL_PROJECT_NAME} CACHE STRING
      ${MYSQL_PROJECT_NAME_DOCSTRING} FORCE)
ELSE()
  SET(MYSQL_PROJECT_NAME "MySQL" CACHE STRING
      ${MYSQL_PROJECT_NAME_DOCSTRING} FORCE)
  MARK_AS_ADVANCED(MYSQL_PROJECT_NAME)
ENDIF()

# Handle upgrade of old cmake cache
IF(DEFINED WITH_PLUGIN_NDBCLUSTER)
  IF(NOT WITH_NDBCLUSTER)
    IF(WITH_PLUGIN_NDBCLUSTER OR WITH_NDBCLUSTER_STORAGE_ENGINE)
      MESSAGE(FATAL_ERROR
        "Old broken config, WITH_NDBCLUSTER_STORAGE_ENGINE have changed "
        "meaning, to build MySQL Cluster please add -DWITH_NDB=ON and "
        "-UWITH_PLUGIN_NDBCLUSTER")
    ENDIF()
  ENDIF()
ENDIF()

# Allow WITH_NDBCLUSTER, makes it possible to bisect between
# old and new source code and use cmake -DWITH_NDBCLUSTER=xx for both.
# If both WITH_NDB and WITH_NDBCLUSTER is set they must be equal.
# Else WITH_NDB is set as WITH_NDBCLUSTER.
# WITH_NDBCLUSTER is unset and not cached to not intefere with
# legacy logic in MYSQL_ADD_PLUGIN.
IF(DEFINED WITH_NDBCLUSTER)
  SET(HAVE_WITH_NDBCLUSTER 1)
  IF(DEFINED WITH_NDB)
    # The extra NOTs are needed to normalize the boolean values else for
    # example ON is not treated equal to 1.
    IF(NOT (NOT WITH_NDB) EQUAL (NOT WITH_NDBCLUSTER))
      MESSAGE(FATAL_ERROR
        "WITH_NDB=${WITH_NDB} do not match WITH_NDBCLUSTER=${WITH_NDBCLUSTER}")
    ENDIF()
    SET(WITH_NDB_DEFAULT OFF)
  ELSEIF(WITH_NDBCLUSTER)
    MESSAGE(WARNING "WITH_NDBCLUSTER option is deprecated, please use "
      "WITH_NDB instead. For backward compatibility WITH_NDBCLUSTER=ON sets "
      "WITH_NDB=ON")
    SET(WITH_NDB_DEFAULT ON)
  ELSE()
    SET(WITH_NDB_DEFAULT OFF)
  ENDIF()
  # Unset WITH_NDBCLUSTER to not intefere with MYSQL_ADD_PLUGIN logic.
  # It will be added to cache at end of this file.
  UNSET(WITH_NDBCLUSTER CACHE)
ELSE()
  SET(WITH_NDB_DEFAULT OFF)
ENDIF()

OPTION(WITH_NDB "Build MySQL Cluster" ${WITH_NDB_DEFAULT})
IF(WITH_NDB)
  MESSAGE(STATUS "Building MySQL Cluster")
ENDIF()

MESSAGE(STATUS "CMAKE_MODULE_PATH is ${CMAKE_MODULE_PATH}")
INCLUDE(mysql_version)
PROJECT(${MYSQL_PROJECT_NAME}
  VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION})

GET_FILENAME_COMPONENT(REALPATH_CMAKE_SOURCE_DIR ${CMAKE_SOURCE_DIR} REALPATH)
GET_FILENAME_COMPONENT(REALPATH_CMAKE_BINARY_DIR ${CMAKE_BINARY_DIR} REALPATH)

MESSAGE(STATUS "Source directory ${REALPATH_CMAKE_SOURCE_DIR}")
MESSAGE(STATUS "Binary directory ${REALPATH_CMAKE_BINARY_DIR}")

# IN PB2 we have a few configurations which are built in-source (e.g. doxygen).
IF(DEFINED ENV{PB2WORKDIR})
  OPTION(FORCE_INSOURCE_BUILD "Allow in-source build" ON)
ELSE()
  OPTION(FORCE_INSOURCE_BUILD "Allow in-source build" OFF)
ENDIF()

# https://gitlab.kitware.com/cmake/community/wikis/FAQ
# cmake-does-not-generate-a-make-distclean-target-why
# Why disallow in-source build?
# Basically because 'make clean' or 'make distclean' do not work.
# So if you do a 'git pull' you may end up with a developer sandbox
# that no longer works as expected (CMakeCache.txt and other
# generated/configured files may contain data which is no longer valid)
SET(THIS_IS_AN_IN_SOURCE_BUILD FALSE)
IF(${REALPATH_CMAKE_SOURCE_DIR} STREQUAL ${REALPATH_CMAKE_BINARY_DIR})
  SET(THIS_IS_AN_IN_SOURCE_BUILD TRUE)
  IF(FORCE_INSOURCE_BUILD)
    MESSAGE(WARNING "This is an in-source build")
  ELSE()
    MESSAGE(FATAL_ERROR
      "Please do not build in-source. "
      "Out-of source builds are highly recommended: "
      "you can have multiple builds for the same source, "
      "and there is an easy way to do cleanup, "
      "simply remove the build directory "
      "(note that 'make clean' or 'make distclean' does *not* work) "
      "\nYou *can* force in-source build by invoking cmake with -DFORCE_INSOURCE_BUILD=1")
  ENDIF()
ENDIF()

IF(WIN32)
  STRING(LENGTH "${REALPATH_CMAKE_BINARY_DIR}" CMAKE_BINARY_DIR_LENGTH)
  IF(CMAKE_BINARY_DIR_LENGTH LESS_EQUAL 3)
    MESSAGE(FATAL_ERROR
      "It seems your build directory ${CMAKE_BINARY_DIR} is a filesystem root, "
      "this is not supported. Please build in a subdirectory.")
  ENDIF()
ENDIF()

MACRO(REPORT_CXX_FLAGS)
  MESSAGE(STATUS "CMAKE_C_FLAGS: ${CMAKE_C_FLAGS}")
  FOREACH(BUILD_TYPE "" _DEBUG _RELWITHDEBINFO _RELEASE _MINSIZEREL)
    SET(flag "CMAKE_CXX_FLAGS${BUILD_TYPE}")
    MESSAGE(STATUS "${flag}: ${${flag}}")
  ENDFOREACH()
ENDMACRO()

# Write content to file, using CONFIGURE_FILE
# The advantage compared to FILE(WRITE) is that timestamp
# does not change if file already has the same content
SET(MYSQL_CMAKE_SCRIPT_DIR "${CMAKE_SOURCE_DIR}/cmake")
MACRO(CONFIGURE_FILE_CONTENT content file)
  SET(CMAKE_CONFIGURABLE_FILE_CONTENT
    "${content}\n")
  CONFIGURE_FILE(
    ${MYSQL_CMAKE_SCRIPT_DIR}/configurable_file_content.in
    ${file}
    @ONLY)
ENDMACRO()

SET(BUILD_IS_SINGLE_CONFIG TRUE)
MESSAGE(STATUS "CMAKE_GENERATOR: ${CMAKE_GENERATOR}")
IF(CMAKE_GENERATOR MATCHES "Visual Studio" OR CMAKE_GENERATOR STREQUAL "Xcode")
  SET(BUILD_IS_SINGLE_CONFIG FALSE)
ENDIF()

IF(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  SET(MY_COMPILER_IS_CLANG 1)
ELSEIF(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
  SET(MY_COMPILER_IS_GNU 1)
  IF(CMAKE_CXX_COMPILER_VERSION VERSION_LESS 9 AND
      NOT CMAKE_CXX_COMPILER_VERSION VERSION_LESS 8)
    SET(MY_COMPILER_IS_GNU_8 1)
  ENDIF()
ELSEIF(CMAKE_CXX_COMPILER_ID MATCHES "SunPro")
  SET(MY_COMPILER_IS_SUNPRO 1)
ENDIF()

IF(MY_COMPILER_IS_CLANG OR MY_COMPILER_IS_GNU)
  SET(MY_COMPILER_IS_GNU_OR_CLANG 1)
ENDIF()

# Maintainer mode is default on only for debug builds using GCC/G++
IF(CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG" OR WITH_DEBUG)
  IF(MY_COMPILER_IS_GNU)
    SET(MYSQL_MAINTAINER_MODE ON CACHE BOOL
        "MySQL maintainer-specific development environment")
  ENDIF()
ENDIF()

OPTION(WITH_DEFAULT_COMPILER_OPTIONS
  "Use flags from cmake/build_configurations/compiler_options.cmake"
  ON)
IF(BUILD_CONFIG)
  INCLUDE(
  ${CMAKE_SOURCE_DIR}/cmake/build_configurations/${BUILD_CONFIG}.cmake)
ENDIF()

OPTION(INSTALL_STATIC_LIBRARIES "Install static libraries" ON)

#cmake on 64bit windows/mac/solaris doesn't set CMAKE_SYSTEM_PROCESSOR correctly
SET(MYSQL_MACHINE_TYPE ${CMAKE_SYSTEM_PROCESSOR})

SET(KNOWN_64BIT_ARCHITECTURES
  arm64
  aarch64
  ppc64
  ppc64le
  s390x
  x86_64
)

# Include the platform-specific file. To allow exceptions, this code
# looks for files in order of how specific they are. If there is, for
# example, a generic Linux.cmake and a version-specific
# Linux-2.6.28-11-generic, it will pick Linux-2.6.28-11-generic and
# include it. It is then up to the file writer to include the generic
# version if necessary.
FOREACH(_base
    ${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_VERSION}-${CMAKE_SYSTEM_PROCESSOR}
    ${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_VERSION}
    ${CMAKE_SYSTEM_NAME})
  SET(_file ${CMAKE_SOURCE_DIR}/cmake/os/${_base}.cmake)
  IF(EXISTS ${_file})
    INCLUDE(${_file})
    BREAK()
  ENDIF()
ENDFOREACH()


IF(UNIX)
  OPTION(WITH_INNODB_MEMCACHED "" OFF)
  OPTION(ENABLE_MEMCACHED_SASL "Enable SASL on InnoDB Memcached" OFF)
  OPTION(ENABLE_MEMCACHED_SASL_PWDB "Enable SASL on InnoDB Memcached" OFF)
ELSE()
  OPTION(WITH_INNODB_MEMCACHED "" OFF)
ENDIF()

# Following autotools tradition, add preprocessor definitions
# specified in environment variable CPPFLAGS
IF(DEFINED ENV{CPPFLAGS})
  ADD_DEFINITIONS($ENV{CPPFLAGS})
ENDIF()

INCLUDE(CheckTypeSize)
CHECK_TYPE_SIZE("void *" SIZEOF_VOIDP)
MESSAGE(STATUS "SIZEOF_VOIDP ${SIZEOF_VOIDP}")
# On some platforms, cmake may think that CMAKE_SIZEOF_VOID_P == 4
# even if we have configured for 64bit build....
SET(CMAKE_SIZEOF_VOID_P ${SIZEOF_VOIDP})


INCLUDE(compile_flags)
INCLUDE(install_layout)

IF(WITH_ASAN OR WITH_LSAN OR WITH_MSAN OR WITH_TSAN OR WITH_UBSAN)
  SET(WITH_SOME_SANITIZER ON)
ELSE()
  SET(WITH_SOME_SANITIZER OFF)
ENDIF()


# Add RPM or DEB platform flags for STANDALONE build.
# Not for DEBUG builds, since both platforms use -O2 and _FORTIFY_SOURCE.
# Not for Release or MinSizeRel, it will inflate binary sizes.
IF(LINUX_STANDALONE AND (LINUX_DEB_PLATFORM OR LINUX_RPM_PLATFORM))
  # Do not add FORTIFY_SOURCE to any of the sanitizers
  # https://github.com/google/sanitizers/issues/247
  IF(WITH_SOME_SANITIZER)
    SET(WITH_PACKAGE_FLAGS_DEFAULT OFF)
  ELSEIF(CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG" OR WITH_DEBUG)
    SET(WITH_PACKAGE_FLAGS_DEFAULT OFF)
  ELSEIF(CMAKE_BUILD_TYPE_UPPER STREQUAL "RELWITHDEBINFO")
    SET(WITH_PACKAGE_FLAGS_DEFAULT ON)
  ELSE()
    SET(WITH_PACKAGE_FLAGS_DEFAULT OFF)
  ENDIF()
  OPTION(WITH_PACKAGE_FLAGS
    "Use DEB/RPM compiler flags" ${WITH_PACKAGE_FLAGS_DEFAULT})
ENDIF()

# Do not set package specific flags if language flags already provided
# in environment or on command line.
# And only for gcc (not clang).
IF(MY_COMPILER_IS_GNU AND WITH_PACKAGE_FLAGS AND
    NOT INITIAL_CMAKE_C_FLAGS AND NOT INITIAL_CMAKE_CXX_FLAGS)
  IF(LINUX_RPM_PLATFORM)
    IF(MY_RPM)
      ADD_LINUX_RPM_FLAGS()
    ELSE()
      MESSAGE(WARNING "rpm executable not found")
    ENDIF()
  ELSEIF(LINUX_DEB_PLATFORM)
    IF(MY_DPKG_BUILDFLAGS)
      ADD_LINUX_DEB_FLAGS()
    ELSE()
      MESSAGE(WARNING "dpkg-buildflags executable not found")
      MESSAGE(WARNING "Please do 'apt install dpkg-dev'")
    ENDIF()
  ENDIF()
ENDIF()

IF(WITH_DEFAULT_COMPILER_OPTIONS)
  INCLUDE(${CMAKE_SOURCE_DIR}/cmake/build_configurations/compiler_options.cmake)
ENDIF()

# Assume, for now at least, that we want build-id for all kinds of Linux builds.
IF(LINUX)
  OPTION(WITH_BUILD_ID "Add --build-id=sha1 to all executables." ON)
  IF(WITH_BUILD_ID)
    SET(HAVE_BUILD_ID_SUPPORT 1)
    FIND_PROGRAM(READELF_EXECUTABLE
      NAMES eu-readelf readelf
      )
    IF(NOT READELF_EXECUTABLE)
      MESSAGE(WARNING "Cannot find eu-readelf or readelf")
      MESSAGE(FATAL_ERROR
        "Install the 'elfutils' package, or do cmake -DWITH_BUILD_ID=OFF")
    ENDIF()
  ENDIF()
ENDIF()

INCLUDE(CMakePushCheckState)

# Add macros
INCLUDE(add_custom_target)
INCLUDE(pkg-config)
INCLUDE(character_sets)
INCLUDE(cmake_parse_arguments)
INCLUDE(link_options)
INCLUDE(malloc_utils)
INCLUDE(cpu_info)
INCLUDE(fileutils)
INCLUDE(zlib)
INCLUDE(zstd)
INCLUDE(lz4)
INCLUDE(icu)
INCLUDE(libevent)
INCLUDE(ssl)
INCLUDE(sasl)
INCLUDE(ldap)
INCLUDE(kerberos)
INCLUDE(rpc)
INCLUDE(readline)
INCLUDE(protobuf)
INCLUDE(package_name)
INCLUDE(libutils)
INCLUDE(plugin)
INCLUDE(component)
INCLUDE(install_macros)
INCLUDE(mysql_add_executable)
INCLUDE(curl)
INCLUDE(rapidjson)
INCLUDE(fprofile)
INCLUDE(gloves)
INCLUDE(fido2)
INCLUDE(win_jemalloc)

IF(UNIX)
  OPTION(WITH_VALGRIND "Valgrind instrumentation" OFF)
ENDIF()

OPTION(WITH_TCMALLOC "Use tcmalloc rather than builtin malloc/free etc." OFF)
OPTION(WITH_TCMALLOC_DEBUG
  "Use tcmalloc_debug rather than builtin malloc/free etc." OFF)
IF(WITH_TCMALLOC OR WITH_TCMALLOC_DEBUG)
  # Note that libtcmalloc.so reports lots of Mismatched free()/delete/delete[]
  # So for valgrind builds, link with libtcmalloc_debug.so
  IF(WITH_VALGRIND OR WITH_TCMALLOC_DEBUG)
    FIND_MALLOC_LIBRARY(tcmalloc_debug)
  ELSE()
    FIND_MALLOC_LIBRARY(tcmalloc)
  ENDIF()
ENDIF()

OPTION(WITH_JEMALLOC "Use jemalloc rather than builtin malloc/free etc." OFF)
IF(WITH_JEMALLOC)
  FIND_MALLOC_LIBRARY(jemalloc)
ENDIF()

IF(WITH_JEMALLOC AND (WITH_TCMALLOC OR WITH_TCMALLOC_DEBUG))
  MESSAGE(FATAL_ERROR "Specify only *one* of WITH_TCMALLOC and WITH_JEMALLOC")
ENDIF()

OPTION(ENABLED_PROFILING "Enable profiling" ON)
OPTION(WITHOUT_SERVER OFF)

IF(WIN32)
  OPTION(WITH_MSCRT_DEBUG "MS Visual Studio Debug CRT instrumentation" OFF)
ENDIF()
IF(NOT WITHOUT_SERVER)
  IF(FPROFILE_GENERATE OR FPROFILE_USE)
    # Do not use data from unit testing when optimizing.
    OPTION(WITH_UNIT_TESTS "Compile MySQL with unit tests" OFF)
  ELSE()
    OPTION(WITH_UNIT_TESTS "Compile MySQL with unit tests" ON)
  ENDIF()
  OPTION(WITH_ROUTER "Build MySQL Router" ON)
ENDIF()

IF(EXISTS ${CMAKE_SOURCE_DIR}/internal/CMakeLists.txt)
  SET(WITH_INTERNAL_DEFAULT 1)
ELSE()
  SET(WITH_INTERNAL_DEFAULT 0)
ENDIF()

IF(NOT DEFINED WITH_INTERNAL)
  SET(WITH_INTERNAL ${WITH_INTERNAL_DEFAULT})
ENDIF()

# On windows we need a non-standard package for SASL.
IF(WITH_INTERNAL AND (NOT WIN32 OR WITH_SASL))
  SET(WITH_AUTHENTICATION_LDAP_DEFAULT ON)
ELSE()
  SET(WITH_AUTHENTICATION_LDAP_DEFAULT OFF)
ENDIF()

OPTION(WITH_AUTHENTICATION_LDAP
  "Report error if the LDAP authentication plugin cannot be built."
  ${WITH_AUTHENTICATION_LDAP_DEFAULT})

# Currently only supported on Linux
IF(WITH_INTERNAL AND LINUX)
  SET(WITH_AUTHENTICATION_KERBEROS_DEFAULT ON)
ELSE()
  SET(WITH_AUTHENTICATION_KERBEROS_DEFAULT OFF)
ENDIF()

OPTION(WITH_AUTHENTICATION_KERBEROS
  "Report error if the Kerberos authentication plugin cannot be built."
  ${WITH_AUTHENTICATION_KERBEROS_DEFAULT})

IF(WITH_INTERNAL AND (NOT SOLARIS))
  SET(WITH_AUTHENTICATION_FIDO_DEFAULT ON)
ELSE()
  SET(WITH_AUTHENTICATION_FIDO_DEFAULT OFF)
ENDIF()

OPTION(WITH_AUTHENTICATION_FIDO
  "Report error if the FIDO authentication plugin cannot be built."
  ${WITH_AUTHENTICATION_FIDO_DEFAULT})

# Default ON if we are building server-side plugins.
# Also default ON in pushbuild, for our community builds.
IF(WITH_AUTHENTICATION_KERBEROS OR
    WITH_AUTHENTICATION_LDAP OR
    WITH_AUTHENTICATION_FIDO OR
    DEFINED ENV{PB2WORKDIR})
  SET(WITH_AUTHENTICATION_CLIENT_PLUGINS_DEFAULT ON)
ELSE()
  SET(WITH_AUTHENTICATION_CLIENT_PLUGINS_DEFAULT OFF)
ENDIF()

# Enable building of
# The KERBEROS client authentication plugin
#   authentication_kerberos_client.so
# The LDAP client authentication plugin
#   authentication_ldap_sasl_client.so
# The FIDO client authentication plugin
#   authentication_fido_client.so
OPTION(WITH_AUTHENTICATION_CLIENT_PLUGINS
  "Build client-side authentication plugins, even if server-side are disabled"
  ${WITH_AUTHENTICATION_CLIENT_PLUGINS_DEFAULT})

# On windows we need a non-standard package for CURL.
IF(WITH_INTERNAL AND UNIX)
  SET(WITH_CURL_DEFAULT "system")
ELSE()
  SET(WITH_CURL_DEFAULT "none")
ENDIF()

OPTION(WITH_LOCK_ORDER
  "Build the lock order mutex instrumentation code." OFF)

IF(WITH_LOCK_ORDER)
  EXECUTE_PROCESS(
    COMMAND ${CMAKE_COMMAND} -E make_directory lock_order
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    )
ENDIF()

IF(DEFINED WITH_LTO_DEFAULT)
  SET(WITH_LTO_DEFAULT ${WITH_LTO_DEFAULT})
ELSE()
  SET(WITH_LTO_DEFAULT OFF)
ENDIF()

OPTION(WITH_LTO
  "Enable the link-time optimizer. Currently works for gcc7/gcc8/gcc9/macos only."
  ${WITH_LTO_DEFAULT}
  )

IF(CMAKE_C_FLAGS MATCHES " -flto" OR CMAKE_CXX_FLAGS MATCHES " -flto")
  SET(CMAKE_COMPILER_FLAG_WITH_LTO 1)
ENDIF()

include(CheckCSourceCompiles)
include(CheckCXXSourceCompiles)
# We need some extra FAIL_REGEX patterns
# Note that CHECK_C_SOURCE_COMPILES is a misnomer, it will also link.
MACRO (MY_CHECK_C_COMPILER_FLAG FLAG RESULT)
  CMAKE_PUSH_CHECK_STATE()
  STRING_APPEND(CMAKE_REQUIRED_FLAGS " ${FLAG}")
  CHECK_C_SOURCE_COMPILES("int main(void) { return 0; }" ${RESULT}
    FAIL_REGEX "unknown argument ignored"
    FAIL_REGEX "argument unused during compilation"
    FAIL_REGEX "unsupported .*option"
    FAIL_REGEX "unknown .*option"
    FAIL_REGEX "unrecognized .*option"
    FAIL_REGEX "ignoring unknown option"
    FAIL_REGEX "[Ww]arning: [Oo]ption"
    FAIL_REGEX "error: visibility"
    FAIL_REGEX "warning: visibility"
    )
  CMAKE_POP_CHECK_STATE()
ENDMACRO()

MACRO (MY_CHECK_CXX_COMPILER_FLAG FLAG RESULT)
  CMAKE_PUSH_CHECK_STATE()
  STRING_APPEND(CMAKE_REQUIRED_FLAGS " ${FLAG}")
  CHECK_CXX_SOURCE_COMPILES("int main(void) { return 0; }" ${RESULT}
    FAIL_REGEX "unknown argument ignored"
    FAIL_REGEX "argument unused during compilation"
    FAIL_REGEX "unsupported .*option"
    FAIL_REGEX "unknown .*option"
    FAIL_REGEX "unrecognized .*option"
    FAIL_REGEX "ignoring unknown option"
    FAIL_REGEX "[Ww]arning: [Oo]ption"
    FAIL_REGEX "error: visibility"
    FAIL_REGEX "warning: visibility"
    )
  CMAKE_POP_CHECK_STATE()
ENDMACRO()

# Check whether a specific warning option is supported.
# Intended use is primarily to silence warnings from third-party code.
# If *our* code has warnings with gcc[56789] or clang[345678] the
# code should be fixed, rather than silencing the compiler warnings.
#
# Returns the corresponding "-Wno-<option>" if supported, 0 otherwise.
# WARNING_OPTION should be as found in clang/gcc documentation.
# Note: Sun Studio accepts/ignores anything starting with "-W".
#       Visual Studio has a few /W options, but are totally different.
# Hence we check for gcc/clang only.
# No return value for functions in cmake, so we use an output argument.
#
# To only avoid failing on warning when compiled with -Werror and still show
# the warnings use SHOW_SUPPRESSED_COMPILER_WARNINGS=ON.
OPTION(SHOW_SUPPRESSED_COMPILER_WARNINGS
  "Show suppressed compiler warnings, without failing with -Werror" OFF)
MARK_AS_ADVANCED(SHOW_SUPPRESSED_COMPILER_WARNINGS)

FUNCTION(MY_CHECK_CXX_COMPILER_WARNING WARNING_OPTION RETURN_VALUE)
  # Strip off possible leading -Wno- or -W
  STRING(REGEX REPLACE "^-Wno-" "" WARNING_OPTION ${WARNING_OPTION})
  STRING(REGEX REPLACE "^-W" "" WARNING_OPTION ${WARNING_OPTION})
  STRING(REGEX MATCH "^error=" ERROR_PREFIX ${WARNING_OPTION})
  # Use HAVE_CXX_W_warning_option as cache variable.
  STRING(REPLACE "-" "_" VAR_NAME ${WARNING_OPTION})
  STRING(REPLACE "=" "_" VAR_NAME ${VAR_NAME})
  STRING(REPLACE "c++" "cpp" VAR_NAME ${VAR_NAME})
  SET(VAR_NAME "HAVE_CXX_W_${VAR_NAME}")
  IF(MY_COMPILER_IS_GNU_OR_CLANG)
    IF(SHOW_SUPPRESSED_COMPILER_WARNINGS AND NOT ERROR_PREFIX)
      MY_CHECK_CXX_COMPILER_FLAG("-Werror=${WARNING_OPTION}" ${VAR_NAME})
      IF(${VAR_NAME})
        SET(WARNING_OPTION "error=${WARNING_OPTION}")
      ENDIF()
    ENDIF()
    IF(NOT ${VAR_NAME})
      MY_CHECK_CXX_COMPILER_FLAG("-W${WARNING_OPTION}" ${VAR_NAME})
    ENDIF()
  ELSE()
    SET(${VAR_NAME} 0 CACHE INTERNAL "-W${WARNING_OPTION} not supported")
  ENDIF()
  IF(${VAR_NAME})
    SET(${RETURN_VALUE} "-Wno-${WARNING_OPTION}" PARENT_SCOPE)
  ELSE()
    SET(${RETURN_VALUE} 0 PARENT_SCOPE)
  ENDIF()
ENDFUNCTION()

# When builing with PGO, GCC will report -Wmissing-profile when compiling
# files for which it cannot find profile data. It is valid to disable
# this warning for files we are not currently interested in profiling.
MACRO(DISABLE_MISSING_PROFILE_WARNING)
  IF(FPROFILE_USE)
    MY_CHECK_CXX_COMPILER_WARNING("-Wmissing-profile" HAS_WARN_FLAG)
    IF(HAS_WARN_FLAG)
      STRING_APPEND(CMAKE_C_FLAGS   " ${HAS_WARN_FLAG}")
      STRING_APPEND(CMAKE_CXX_FLAGS " ${HAS_WARN_FLAG}")
    ENDIF()
  ENDIF()
ENDMACRO()

OPTION(OPTIMIZE_SANITIZER_BUILDS
  "Add -O1 -fno-inline to sanitizer builds" ON)

MACRO(MY_SANITIZER_CHECK SAN_OPT ADD_OPTIMIZATION RESULT)
  MY_CHECK_C_COMPILER_FLAG("${SAN_OPT}" C_RESULT)
  MY_CHECK_CXX_COMPILER_FLAG("${SAN_OPT}" CXX_RESULT)
  IF(C_RESULT AND CXX_RESULT)
    STRING_APPEND(CMAKE_C_FLAGS   " ${SAN_OPT}")
    STRING_APPEND(CMAKE_CXX_FLAGS " ${SAN_OPT}")
    # We switch on basic optimization also for debug builds.
    # We disable inlining for stack trace readability.
    # With optimization we may get some warnings, so we switch off -Werror
    IF(${ADD_OPTIMIZATION} AND OPTIMIZE_SANITIZER_BUILDS)
      IF(MSVC)
        STRING_APPEND(CMAKE_C_FLAGS   " -O1 /Ob0")
        STRING_APPEND(CMAKE_CXX_FLAGS " -O1 /Ob0")
      ELSE()
        STRING_APPEND(CMAKE_C_FLAGS   " -O1 -fno-inline")
        STRING_APPEND(CMAKE_CXX_FLAGS " -O1 -fno-inline")
      ENDIF()
      IF(NOT SANITIZE_MAINTAINER_MODE)
        SET(MYSQL_MAINTAINER_MODE OFF CACHE BOOL
          "MySQL maintainer-specific development environment" FORCE)
      ENDIF()
    ENDIF()
    SET(${RESULT} 1)
  ELSE()
    SET(${RESULT} 0)
  ENDIF()
ENDMACRO()

OPTION(WITH_ASAN "Enable address sanitizer" OFF)
IF(WITH_ASAN)
  IF(WIN32_CLANG)
    # NOTE: We do _not_ modify CMAKE_C_FLAGS / CMAKE_CXX_FLAGS to add
    # -fsanitize=address here yet; Clang (at least of 5.0.0) requires /MT,
    # but CMake adds /MDd to all feature checks almost no matter what we do.
    # For the same reason, we have to enable it unconditionalyl without
    # doing a feature test; we know it's working. Modifying the flags happens
    # below, near the end.
    SET(HAVE_ASAN 1)

    # Get the path to the ASAN support libraries.
    GET_FILENAME_COMPONENT(ASAN_LIB_DIR ${CMAKE_CXX_COMPILER} DIRECTORY)
    SET(ASAN_LIB_DIR "${ASAN_LIB_DIR}/../lib/clang/${CMAKE_CXX_COMPILER_VERSION}/lib/windows")
    GET_FILENAME_COMPONENT(ASAN_LIB_DIR ${ASAN_LIB_DIR} REALPATH)
  ELSE()
    MY_SANITIZER_CHECK("-fsanitize=address" TRUE WITH_ASAN_OK)
    IF(WITH_ASAN_OK)
      OPTION(WITH_ASAN_SCOPE "Enable -fsanitize-address-use-after-scope" ON)
      SET(HAVE_ASAN 1)
      # This works with clang, but not gcc it seems.
      MY_CHECK_CXX_COMPILER_FLAG(
        "-fsanitize=address -fsanitize-address-use-after-scope"
        HAVE_SANITIZE_SCOPE)
      IF(WITH_ASAN_SCOPE AND HAVE_SANITIZE_SCOPE)
        STRING_APPEND(CMAKE_C_FLAGS   " -fsanitize-address-use-after-scope")
        STRING_APPEND(CMAKE_CXX_FLAGS " -fsanitize-address-use-after-scope")
      ENDIF()
    ELSE()
      MESSAGE(FATAL_ERROR "Do not know how to enable address sanitizer")
    ENDIF()
  ENDIF()
ENDIF()

OPTION(WITH_LSAN "Enable leak sanitizer" OFF)
IF(WITH_LSAN)
  MY_SANITIZER_CHECK("-fsanitize=leak"
    TRUE WITH_LSAN_OK)
  IF(NOT WITH_LSAN_OK)
    MESSAGE(FATAL_ERROR "Do not know how to enable leak sanitizer")
  ELSE()
    SET(HAVE_LSAN 1)
  ENDIF()
ENDIF()

OPTION(WITH_MSAN "Enable memory sanitizer" OFF)
IF(WITH_MSAN)
  MY_SANITIZER_CHECK("-fsanitize=memory -fsanitize-memory-track-origins"
    TRUE WITH_MSAN_OK)
  IF(NOT WITH_MSAN_OK)
    MESSAGE(FATAL_ERROR "Do not know how to enable memory sanitizer")
  ELSE()
    MESSAGE(WARNING "Memory sanitizer support is currently experimental.")
  ENDIF()
ENDIF()

OPTION(WITH_UBSAN "Enable undefined behavior sanitizer" OFF)
IF(WITH_UBSAN)
  MY_SANITIZER_CHECK("-fsanitize=undefined" TRUE WITH_UBSAN_OK)
  IF(NOT WITH_UBSAN_OK)
    MESSAGE(FATAL_ERROR
            "Do not know how to enable undefined behavior sanitizer")
  ELSE()
    SET(HAVE_UBSAN 1)
  ENDIF()
ENDIF()

OPTION(WITH_TSAN "Enable thread sanitizer" OFF)
IF(WITH_TSAN)
  MY_SANITIZER_CHECK("-fsanitize=thread" TRUE WITH_TSAN_OK)
  IF(NOT WITH_TSAN_OK)
    MESSAGE(FATAL_ERROR "Do not know how to enable thread sanitizer")
  ELSE()
    MESSAGE(WARNING "Thread sanitizer support is currently experimental.")
    SET(HAVE_TSAN 1)
  ENDIF()
ENDIF()

IF(WITH_ASAN AND WITH_MSAN)
  MESSAGE(FATAL_ERROR
          "Cannot use AddressSanitizer and MemorySanitizer together")
ENDIF()

IF((WITH_ASAN OR WITH_LSAN) AND (WITH_TCMALLOC OR WITH_JEMALLOC))
  MESSAGE(FATAL_ERROR
    "Cannot use tcmalloc/jemalloc with leak sanitizer")
ENDIF()

IF(WITH_ASAN OR WITH_LSAN OR WITH_UBSAN)
  SET(HANDLE_FATAL_SIGNALS_DEFAULT 0)
ELSE()
  SET(HANDLE_FATAL_SIGNALS_DEFAULT 1)
ENDIF()
OPTION(HANDLE_FATAL_SIGNALS
  "Handle fatal signals with internal signal handler"
  ${HANDLE_FATAL_SIGNALS_DEFAULT})

IF(CMAKE_C_FLAGS MATCHES "-fsanitize" OR CMAKE_CXX_FLAGS MATCHES "-fsanitize")
  SET(CMAKE_COMPILER_FLAG_WITH_SANITIZE 1)
ENDIF()

# Older versions of ccache must be disabled: export CCACHE_DISABLE=1
# See http://www.cmake.org/Wiki/CTest/Coverage
OPTION(ENABLE_GCOV "Enable gcov (debug, Linux builds only)" OFF)
IF (ENABLE_GCOV AND NOT WIN32 AND NOT APPLE)
  STRING_APPEND(CMAKE_C_FLAGS
  " -fprofile-arcs -ftest-coverage -fprofile-update=prefer-atomic -DHAVE_GCOV")
  STRING_APPEND(CMAKE_CXX_FLAGS
  " -fprofile-arcs -ftest-coverage -fprofile-update=prefer-atomic -DHAVE_GCOV")
  STRING_APPEND(CMAKE_EXE_LINKER_FLAGS
  " -fprofile-arcs -ftest-coverage -fprofile-update=prefer-atomic -lgcov")
ENDIF()

OPTION(ENABLE_GPROF "Enable gprof (optimized, Linux builds only)" OFF)
IF (ENABLE_GPROF AND NOT WIN32 AND NOT APPLE)
  STRING_APPEND(CMAKE_C_FLAGS " -pg")
  STRING_APPEND(CMAKE_CXX_FLAGS " -pg")
  STRING_APPEND(CMAKE_EXE_LINKER_FLAGS " -pg")
ENDIF()

# Use lld on Linux if available and not explicitly disabled.
# You need to install the 'lld' RPM/DEB package.
# LTO build fails with lld, so turn it off by default.
IF(LINUX AND NOT ALTERNATIVE_LD AND
    NOT WITH_LTO AND NOT CMAKE_COMPILER_FLAG_WITH_LTO)
  OPTION(USE_LD_LLD "Use llvm lld linker" ON)
ELSE()
  OPTION(USE_LD_LLD "Use llvm lld linker" OFF)
ENDIF()

# If ALTERNATIVE_LD is set, we must use it.
IF(ALTERNATIVE_LD AND USE_LD_LLD)
  MESSAGE(WARNING
    "The compiler ${ALTERNATIVE_GPP} is not compatible with lld")
  SET(USE_LD_LLD 0)
  SET(USE_LD_LLD 0 CACHE INTERNAL "" FORCE)
ENDIF()

SET(USING_LD_LLD 0)
IF(USE_LD_LLD)
  CHECK_ALTERNATIVE_LINKER("lld" USING_LD_LLD)
ENDIF()
SET(USING_LD_LLD ${USING_LD_LLD} CACHE INTERNAL "")

IF(LINUX_STANDALONE)
  # ON by default in pushbuild, but only for standalone builds.
  # The comression does not play well with some RPM/DEB packaging.
  # Developers can set default in environment.
  # Compressing debug sections will shink binaries significantly, esp
  # for RelWithDebInfo builds, at the cost of some extra link time.
  IF(DEFINED ENV{PB2WORKDIR} OR
      (DEFINED ENV{COMPRESS_DEBUG_SECTIONS} AND "$ENV{COMPRESS_DEBUG_SECTIONS}"
        AND NOT DEFINED(COMPRESS_DEBUG_SECTIONS)))
    SET(COMPRESS_DEBUG_SECTIONS_DEFAULT ON)
  ELSE()
    SET(COMPRESS_DEBUG_SECTIONS_DEFAULT OFF)
  ENDIF()

  OPTION(COMPRESS_DEBUG_SECTIONS
    "Compress debug sections of mysqld and test executables"
    ${COMPRESS_DEBUG_SECTIONS_DEFAULT})
ELSE()
  SET(COMPRESS_DEBUG_SECTIONS OFF)
ENDIF()

# Newer versions of gcc produce huge amounts of debug information
# with the default debug level.
#
# From 'man gcc':
# Level 1 produces minimal information, enough for making backtraces in
# parts of the program that you don't plan to debug.  This includes
# descriptions of functions and external variables, and line number
# tables, but no information about local variables.
#
# clang documentation says: -gline-tables-only, -g1, -gmlt
# Emit debug line number tables only
IF(UNIX AND MY_COMPILER_IS_GNU_OR_CLANG)
  # By default, do this for STANDALONE and tarball builds.
  # Off by default for sanitizer or valgrind builds.
  # Off by default for Apple Clang.
  IF((INSTALL_LAYOUT MATCHES "STANDALONE" OR
        INSTALL_LAYOUT MATCHES "TARGZ" OR
        INSTALL_LAYOUT MATCHES "SVR4"
        )
      AND NOT CMAKE_COMPILER_FLAG_WITH_SANITIZE AND NOT WITH_VALGRIND
      AND NOT APPLE)
    SET(MINIMAL_RELWITHDEBINFO_DEFAULT ON)
  ELSE()
    SET(MINIMAL_RELWITHDEBINFO_DEFAULT OFF)
  ENDIF()
  OPTION(MINIMAL_RELWITHDEBINFO
    "Reduce debug information for gcc RelWithDebInfo builds"
    ${MINIMAL_RELWITHDEBINFO_DEFAULT})
  # Appending here will override any earlier -g options.
  IF(MINIMAL_RELWITHDEBINFO)
    STRING_APPEND(CMAKE_C_FLAGS_RELWITHDEBINFO   " -g1")
    STRING_APPEND(CMAKE_CXX_FLAGS_RELWITHDEBINFO " -g1")
  ENDIF()
ENDIF()

# Enable "-Og -fno-inline" for ordinary Debug builds.
# Note that this may optimize away local stack variables.
IF(UNIX AND MY_COMPILER_IS_GNU_OR_CLANG
    AND NOT CMAKE_COMPILER_FLAG_WITH_SANITIZE AND NOT WITH_VALGRIND)
  # ON by default in pushbuild, to save time running Debug test suites.
  # man clang:
  # -Og Like -O1. In future versions, this option might disable different
  # optimizations in order to improve debuggability.
  # man gcc:
  # -Og Optimize debugging experience. -Og should be the
  #  optimization level of choice for the standard edit-compile-
  #  debug cycle, offering a reasonable level of optimization while
  #  maintaining fast compilation and a good debugging experience.
  #  It is a better choice than -O0 for producing debuggable code
  #  because some compiler passes that collect debug information are
  #  disabled at -O0.
  #
  # OFF by default if CFLAGS or CXXFLAGS are defined in the environment.
  # CFLAGS/CXXFLAGS will be set e.g. for LINUX_DEB_PLATFORM,
  # and may contain other optimization flags, interfering with ours.
  IF((NOT DEFINED ENV{CFLAGS} AND NOT DEFINED ENV{CXXFLAGS}
        AND DEFINED ENV{PB2WORKDIR})
      OR
      (DEFINED ENV{OPTIMIZE_DEBUG_BUILDS} AND "$ENV{OPTIMIZE_DEBUG_BUILDS}"
        AND NOT DEFINED(OPTIMIZE_DEBUG_BUILDS)))
    SET(OPTIMIZE_DEBUG_BUILDS_DEFAULT ON)
  ELSE()
    SET(OPTIMIZE_DEBUG_BUILDS_DEFAULT OFF)
  ENDIF()
  OPTION(OPTIMIZE_DEBUG_BUILDS
    "Add -Og -fno-inline to CMAKE_<lang>_FLAGS_DEBUG"
    ${OPTIMIZE_DEBUG_BUILDS_DEFAULT}
    )
  # Use STRING_PREPEND here, so command-line input can override our defaults.
  IF(OPTIMIZE_DEBUG_BUILDS)
    STRING_PREPEND(CMAKE_C_FLAGS_DEBUG   " -Og -fno-inline ")
    STRING_PREPEND(CMAKE_CXX_FLAGS_DEBUG " -Og -fno-inline ")
  ENDIF()
ENDIF()

# For aarch64 some sub-architectures support LSE atomics and some don't. Thus,
# compiling for the common denominator (-march=armv8-a) means LSE is not used.
# The -moutline-atomics switch enables run-time detection of LSE support.
# There are compilers (gcc 9.3.1 for example) which support this switch, but
# do not enable it by default, even though it seems to help. So, we force it.
IF(CMAKE_SYSTEM_PROCESSOR STREQUAL "aarch64")
  MY_CHECK_CXX_COMPILER_FLAG( "-moutline-atomics" HAVE_OUTLINE_ATOMICS)
  IF(HAVE_OUTLINE_ATOMICS)
    STRING_APPEND(CMAKE_C_FLAGS   " -moutline-atomics")
    STRING_APPEND(CMAKE_CXX_FLAGS " -moutline-atomics")
  ENDIF()
ENDIF()

IF(LINUX)
  OPTION(LINK_RANDOMIZE "Randomize the order of all symbols in the binary" OFF)
  SET(LINK_RANDOMIZE_SEED "mysql"
    CACHE STRING "Seed to use for link randomization")
ELSE()
  SET(LINK_RANDOMIZE OFF)
ENDIF()

IF(CMAKE_C_LINK_FLAGS MATCHES "-fuse-ld=gold" OR
    CMAKE_CXX_LINK_FLAGS MATCHES "-fuse-ld=gold" OR
    CMAKE_EXE_LINKER_FLAGS MATCHES "-fuse-ld=gold")
  MESSAGE(WARNING "-fuse-ld=gold is not supported")
  STRING(REPLACE
    "-fuse-ld=gold" "" CMAKE_C_LINK_FLAGS "${CMAKE_C_LINK_FLAGS}")
  STRING(REPLACE
    "-fuse-ld=gold" "" CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS}")
  STRING(REPLACE
    "-fuse-ld=gold" "" CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS}")
ENDIF()

# Fedora 34, RHEL9: undefined reference to symbol 'crc32_z@@ZLIB_1.2.9'
IF(LINUX_FEDORA_34 OR LINUX_RHEL9)
  IF(NOT USING_LD_LLD)
    STRING_APPEND(CMAKE_CXX_LINK_FLAGS " -Wl,--copy-dt-needed-entries")
  ENDIF()
ENDIF()

IF(LINK_RANDOMIZE)
  STRING_APPEND(CMAKE_C_FLAGS   " -ffunction-sections -fdata-sections")
  STRING_APPEND(CMAKE_CXX_FLAGS " -ffunction-sections -fdata-sections")

  SET(CMAKE_C_LINK_FLAGS "${CMAKE_C_LINK_FLAGS} -Wl,--sort-section=name")
  SET(CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS} -Wl,--sort-section=name")

  SET(CMAKE_CXX_ARCHIVE_CREATE "sh -c '${CMAKE_SOURCE_DIR}/scripts/randomize-order.pl ${LINK_RANDOMIZE_SEED} <OBJECTS> && <CMAKE_AR> qc <TARGET> <LINK_FLAGS> <OBJECTS>'")
  SET(CMAKE_CXX_ARCHIVE_CREATE_APPEND "sh -c '${CMAKE_SOURCE_DIR}/scripts/randomize-order.pl ${LINK_RANDOMIZE_SEED} <OBJECTS> && <CMAKE_AR> q <TARGET> <LINK_FLAGS> <OBJECTS>'")
  SET(CMAKE_C_ARCHIVE_CREATE ${CMAKE_CXX_ARCHIVE_CREATE})
  SET(CMAKE_C_ARCHIVE_CREATE_APPEND ${CMAKE_CXX_ARCHIVE_CREATE_APPEND})
ENDIF()

# Gold and LLD support the --gdb-index option, which adds a .gdb_index
# section to the binaries. It makes loading the binaries in gdb much
# faster. The overhead of generating the index is small in LLD.
OPTION(ADD_GDB_INDEX "Generate a .gdb_index section in the binaries." OFF)

IF(ADD_GDB_INDEX AND CMAKE_EXE_LINKER_FLAGS MATCHES "-fuse-ld=lld")
  # -fuse-ld=lld is not enough to ensure that ld.lld will actually be used.
  # On some platforms we need an extra check for -Wl,--gdb-index
  CMAKE_PUSH_CHECK_STATE()
  SET(CMAKE_REQUIRED_LIBRARIES "-fuse-ld=lld -Wl,--gdb-index")
  CHECK_CXX_SOURCE_COMPILES("int main() {}" CXX_LD_GDB_INDEX_RESULT)
  CMAKE_POP_CHECK_STATE()

  IF(CXX_LD_GDB_INDEX_RESULT)
    STRING_APPEND(CMAKE_EXE_LINKER_FLAGS " -Wl,--gdb-index")
    STRING_APPEND(CMAKE_MODULE_LINKER_FLAGS " -Wl,--gdb-index")
    STRING_APPEND(CMAKE_SHARED_LINKER_FLAGS " -Wl,--gdb-index")
    # gdb sometimes reports "incomplete type" after linking with
    # --gdb-index. This flag makes the index contain enough
    # information to avoid the problem.
    STRING_APPEND(CMAKE_C_FLAGS " -ggnu-pubnames")
    STRING_APPEND(CMAKE_CXX_FLAGS " -ggnu-pubnames")
  ELSE()
    MESSAGE(STATUS "Seems like linker does not support --gdb-index")
  ENDIF()
ENDIF()

IF(LINUX)
  # May be set to ON in cmake/build_configurations/mysql_release.cmake
  IF(DEFINED REPRODUCIBLE_BUILD)
    SET(REPRODUCIBLE_BUILD_DEFAULT ${REPRODUCIBLE_BUILD})
  ELSE()
    SET(REPRODUCIBLE_BUILD_DEFAULT OFF)
  ENDIF()
  OPTION(REPRODUCIBLE_BUILD
    "Take extra pains to make build result independent of build location and time"
    ${REPRODUCIBLE_BUILD_DEFAULT})
ENDIF()
IF(REPRODUCIBLE_BUILD)
  SET(DEBUG_PREFIX_FLAGS
    "-fdebug-prefix-map=${CMAKE_SOURCE_DIR}/=./ -fdebug-prefix-map=${CMAKE_CURRENT_BINARY_DIR}=./obj")

  # See if -fdebug-prefix= commands are included in the debug output,
  # making the build unreproducible with switches recorded.
  # See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=69821.
  EXECUTE_PROCESS(COMMAND
    ${CMAKE_C_COMPILER} -g3 -x c -S -fdebug-prefix-map=foo=bar -o - -
    INPUT_FILE /dev/null OUTPUT_VARIABLE DEBUG_PREFIX_MAP_RESULT)
  IF(DEBUG_PREFIX_MAP_RESULT MATCHES "foo=bar")
    SET(DEBUG_PREFIX_FLAGS "${DEBUG_PREFIX_FLAGS} -gno-record-gcc-switches")
  ENDIF()

  STRING_APPEND(CMAKE_C_FLAGS   " ${DEBUG_PREFIX_FLAGS}")
  STRING_APPEND(CMAKE_CXX_FLAGS " ${DEBUG_PREFIX_FLAGS}")

  SET(BISON_NO_LINE_OPT "--no-lines")

  SET_PROPERTY(GLOBAL PROPERTY RULE_LAUNCH_COMPILE
    "${CMAKE_SOURCE_DIR}/scripts/invoke-with-relative-paths.pl")
ENDIF()

OPTION(ENABLED_LOCAL_INFILE
 "If we should enable LOAD DATA LOCAL by default" OFF)
MARK_AS_ADVANCED(ENABLED_LOCAL_INFILE)

#
# Options related to client-side protocol tracing
#

OPTION(WITH_CLIENT_PROTOCOL_TRACING
  "Support for client-side protocol tracing plugins" ON)
OPTION(WITH_TEST_TRACE_PLUGIN
  "Have a built-in test protocol trace plugin in libmysql (requires WITH_CLIENT_PROTOCOL_TRACING option)" OFF)

# Sanity checks for protocol tracing options

IF(WITH_TEST_TRACE_PLUGIN AND NOT WITH_CLIENT_PROTOCOL_TRACING)
  MESSAGE("WARNING: Test trace plugin was selected but client protocol tracing
           infrastructure is not enabled - ignoring")
  SET(WITH_TEST_TRACE_PLUGIN 0)
ENDIF()

IF(WITH_TEST_TRACE_PLUGIN AND NOT CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG")
  MESSAGE(SEND_ERROR
    "Test trace plugin was selected but it can be included only in
     debug binaries. Set WITH_TEST_TRACE_PLUGIN to OFF or WITH_DEBUG to ON.")
ENDIF()

# Add safemutex for debug configurations
# Enable debug sync for debug builds.
FOREACH(LANG C CXX)
  STRING_PREPEND(CMAKE_${LANG}_FLAGS_DEBUG "-DENABLED_DEBUG_SYNC ")
  STRING_PREPEND(CMAKE_${LANG}_FLAGS_DEBUG "-DSAFE_MUTEX ")
ENDFOREACH()


# Set commonly used variables
IF(WIN32)
  SET(DEFAULT_MYSQL_HOME "C:/Program Files/MySQL/MySQL Server ${MYSQL_BASE_VERSION}" )
  SET(SHAREDIR share)
ELSE()
  SET(DEFAULT_MYSQL_HOME ${CMAKE_INSTALL_PREFIX})
  SET(SHAREDIR ${DEFAULT_MYSQL_HOME}/${INSTALL_MYSQLSHAREDIR})
ENDIF()

SET(DEFAULT_BASEDIR "${DEFAULT_MYSQL_HOME}")

IF(IS_ABSOLUTE ${INSTALL_MYSQLDATADIR})
  SET(MYSQL_DATADIR ${INSTALL_MYSQLDATADIR} CACHE PATH
    "default MySQL data directory")
ELSE()
  SET(MYSQL_DATADIR "${DEFAULT_MYSQL_HOME}/${INSTALL_MYSQLDATADIR}" CACHE PATH
    "default MySQL data directory")
ENDIF()

IF(IS_ABSOLUTE ${INSTALL_MYSQLKEYRINGDIR})
  SET(MYSQL_KEYRINGDIR ${INSTALL_MYSQLKEYRINGDIR} CACHE PATH
    "default MySQL keyring directory")
ELSE()
  SET(MYSQL_KEYRINGDIR "${DEFAULT_MYSQL_HOME}/${INSTALL_MYSQLKEYRINGDIR}"
    CACHE PATH "default MySQL keyring directory")
ENDIF()

SET(DEFAULT_CHARSET_HOME "${DEFAULT_MYSQL_HOME}")
SET(PLUGINDIR "${DEFAULT_MYSQL_HOME}/${INSTALL_PLUGINDIR}")
IF(SYSCONFDIR)
  SET(DEFAULT_SYSCONFDIR "${SYSCONFDIR}")
ENDIF()

IF(WIN32) # P_tmpdir is not defined on Windows as of VS2015.
  SET(TMPDIR "" # So we use empty path as default. In practice TMP/TEMP is used
    CACHE PATH
    "PATH to MySQL TMP dir")
ELSE()
  SET(TMPDIR "P_tmpdir"
    CACHE PATH
    "PATH to MySQL TMP dir. Defaults to the P_tmpdir macro in <stdio.h>")
ENDIF()
IF(TMPDIR STREQUAL "P_tmpdir")
  # Do not quote it, to refer to the P_tmpdir macro.
  SET(DEFAULT_TMPDIR "P_tmpdir")
ELSE()
  # Quote it, to make it a const char string.
  SET(DEFAULT_TMPDIR "\"${TMPDIR}\"")
ENDIF()

INCLUDE(cmake/boost.cmake)

IF (LINUX)
  OPTION(WITH_SYSTEMD "Enable installation of systemd support files" OFF)
  OPTION(WITH_SYSTEMD_DEBUG "Build in systemd debug trace" OFF)
  IF (WITH_SYSTEMD)
    INCLUDE(cmake/systemd.cmake)
  ELSEIF (WITH_SYSTEMD_DEBUG)
    MESSAGE(FATAL_ERROR "systemd-debugging requires systemd, and systemd requires linux")
  ENDIF()
ELSE()
  IF (WITH_SYSTEMD)
    MESSAGE(FATAL_ERROR "Installation of systemd support files not supported")
  ENDIF()
  IF (WITH_SYSTEMD_DEBUG)
    MESSAGE(FATAL_ERROR "systemd-debugging requires systemd, and systemd requires linux")
  ENDIF()
ENDIF()

# Run platform tests
INCLUDE(configure.cmake)

# Common defines and includes
ADD_DEFINITIONS(-DHAVE_CONFIG_H)
ADD_DEFINITIONS(-D__STDC_LIMIT_MACROS)     # Enable C99 limit macros
ADD_DEFINITIONS(-D__STDC_FORMAT_MACROS)    # Enable C99 printf format macros
ADD_DEFINITIONS(-D_USE_MATH_DEFINES)       # Get access to M_PI, M_E, etc. in math.h
ADD_DEFINITIONS(-DLZ4_DISABLE_DEPRECATE_WARNINGS) # C++14 deprecation warnings in LZ4.

OPTION(ENABLE_EXPERIMENT_SYSVARS "Expose ussually hidden system variables to allow experiments" OFF)
IF(ENABLE_EXPERIMENT_SYSVARS)
  ADD_DEFINITIONS(-DENABLE_EXPERIMENT_SYSVARS)
ENDIF()

OPTION(FORCE_COLORED_OUTPUT "Colorize compiler output" OFF)
IF(FORCE_COLORED_OUTPUT)
  IF(MY_COMPILER_IS_GNU)
    ADD_COMPILE_OPTIONS(-fdiagnostics-color=always)
  ELSEIF(MY_COMPILER_IS_CLANG)
    ADD_COMPILE_OPTIONS(-fcolor-diagnostics)
  ENDIF()
ENDIF()

INCLUDE_DIRECTORIES(
  ${CMAKE_CURRENT_BINARY_DIR}
  ${CMAKE_CURRENT_BINARY_DIR}/include
  ${CMAKE_SOURCE_DIR}
  ${CMAKE_SOURCE_DIR}/include
  )

IF(WITH_LTO)
  IF(APPLE)
    # Requires CMake 3.9+
    INCLUDE(CheckIPOSupported)
    # Will terminate cmake if the check fails.
    CHECK_IPO_SUPPORTED()
    # macOS has problems with enabling LTO for shared libs, so we only set it
    # as target property for mysqld later.
  ELSE()
    MY_CHECK_CXX_COMPILER_FLAG("-flto" CXX_LTO_RESULT)
    IF(NOT CXX_LTO_RESULT)
      MESSAGE(FATAL_ERROR "Compiler does not support -flto")
    ENDIF()
    STRING_APPEND(CMAKE_C_FLAGS   " -flto")
    STRING_APPEND(CMAKE_CXX_FLAGS " -flto")

    # Test for parallel linking.
    SET(NUM_PROCESSING_UNITS 4)
    MY_CHECK_CXX_COMPILER_FLAG(
      "-flto=${NUM_PROCESSING_UNITS}" CXX_LTO_PARALLEL_RESULT)
    IF(CXX_LTO_PARALLEL_RESULT)
      FIND_PROGRAM(NPROC_EXECUTABLE nproc)
      IF(NPROC_EXECUTABLE)
        EXECUTE_PROCESS(COMMAND ${NPROC_EXECUTABLE}
          OUTPUT_VARIABLE NPROC_NUM
          RESULT_VARIABLE NPROC_RESULT
          OUTPUT_STRIP_TRAILING_WHITESPACE)
        IF(NOT NPROC_RESULT)
          SET(NUM_PROCESSING_UNITS ${NPROC_NUM})
        ENDIF()
      ENDIF()
      STRING_APPEND(CMAKE_CXX_LINK_FLAGS      " -flto=${NUM_PROCESSING_UNITS}")
      STRING_APPEND(CMAKE_SHARED_LINKER_FLAGS " -flto=${NUM_PROCESSING_UNITS}")
    ELSE()
      STRING_APPEND(CMAKE_CXX_LINK_FLAGS      " -flto")
      STRING_APPEND(CMAKE_SHARED_LINKER_FLAGS " -flto")
    ENDIF()
  ENDIF()

  # On some platforms (Fedora) we *must* use gcc-ar / gcc-ranlib
  # to get the right plugins for supporting -flto output from gcc.
  IF(MY_COMPILER_IS_GNU)
    IF(ALTERNATIVE_ENABLE)
      GET_FILENAME_COMPONENT(ALTERNATIVE_ENABLE_DIR ${ALTERNATIVE_ENABLE} PATH)
      SET(DEVTOOLSET_ROOT "${ALTERNATIVE_ENABLE_DIR}/root")
      FIND_PROGRAM(GCC_AR_EXECUTABLE gcc-ar
        NO_DEFAULT_PATH
        PATHS "${DEVTOOLSET_ROOT}/usr/bin")
      FIND_PROGRAM(GCC_RANLIB_EXECUTABLE gcc-ranlib
        NO_DEFAULT_PATH
        PATHS "${DEVTOOLSET_ROOT}/usr/bin")
    ELSEIF(ALTERNATIVE_GCC)
      STRING(REGEX MATCH  "^([0-9])"
        ALTERNATIVE_GCC_MAJ_VER ${CMAKE_C_COMPILER_VERSION})
      GET_FILENAME_COMPONENT(ALTERNATIVE_GCC_DIR ${ALTERNATIVE_GCC} PATH)
      FIND_PROGRAM(GCC_AR_EXECUTABLE gcc-ar-${ALTERNATIVE_GCC_MAJ_VER}
        NO_DEFAULT_PATH
        PATHS "${ALTERNATIVE_GCC_DIR}")
      FIND_PROGRAM(GCC_RANLIB_EXECUTABLE gcc-ranlib-${ALTERNATIVE_GCC_MAJ_VER}
        NO_DEFAULT_PATH
        PATHS "${ALTERNATIVE_GCC_DIR}")
    ELSE()
      FIND_PROGRAM(GCC_AR_EXECUTABLE gcc-ar)
      FIND_PROGRAM(GCC_RANLIB_EXECUTABLE gcc-ranlib)
    ENDIF()
    IF(GCC_AR_EXECUTABLE AND GCC_RANLIB_EXECUTABLE)
      SET(CMAKE_AR ${GCC_AR_EXECUTABLE})
      SET(CMAKE_RANLIB ${GCC_RANLIB_EXECUTABLE})
    ENDIF()
  ENDIF()
ENDIF(WITH_LTO)

IF(APPLE)
  OPTION(WITH_DEVELOPER_ENTITLEMENTS "Add developer entitlements" OFF)
ENDIF()

# Explicit list of all -DWITH_XXX=system possibilities
# *except* boost, which requires one specific version to be downloaded.
# RapidJSON is also excluded, since the latest released version does not
# fully support the use of std::regex as the regular expression engine. It
# is required by JSON_SCHEMA_VALID, so don't add it automatically until an
# official version with full support for std::regex is released. The bundled
# version has the necessary patches to make it work.
#
# "system" versions of these for macOS are available from Xcode or Homebrew.
# Libraries from Xcode have file extension '.tbd'.
# Libraries from Homebrew have file extension '.dylib'.
# To get all the Homebrew libraries, do 'brew install' <name of lib>.
SET(SYSTEM_LIBRARIES
  CURL      # Xcode
  EDITLINE  # Xcode
  ICU       # Homebrew  icu4c
  LIBEVENT  # Homebrew  libevent
  LZ4       # Homebrew  lz4
  PROTOBUF  # Homebrew  protobuf
  SSL       # Homebrew  openssl@1.1
  ZSTD      # Homebrew  zstd
  FIDO      # Homebrew  libfido2
  )

IF(APPLE)
  # Homebrew default location is
  IF(APPLE_ARM)
    SET(HOMEBREW_HOME "/opt/homebrew/opt")
  ELSE()
    SET(HOMEBREW_HOME "/usr/local/opt")
  ENDIF()
ENDIF()

SET(WITH_SYSTEM_LIBS_DEFAULT OFF)
OPTION(WITH_SYSTEM_LIBS
  "Use -DWITH_XXX=system for ${SYSTEM_LIBRARIES}" ${WITH_SYSTEM_LIBS_DEFAULT})

# undefined symbol: _Z15fido_assert_newv which is fido_assert_new()
# /usr/include/fido.h is missing 'extern "C"'
# but even if we fix that, tests will fail.
IF(LINUX_UBUNTU)
  IF(LINUX_UBUNTU_VERSION_ID VERSION_LESS "21.04")
    LIST(REMOVE_ITEM SYSTEM_LIBRARIES FIDO)
  ENDIF()
ENDIF()

GET_CMAKE_PROPERTY(CACHE_VARS CACHE_VARIABLES)
UNSET(COMMAND_LINE_VARS)
FOREACH(CACHE_VAR ${CACHE_VARS})
  GET_PROPERTY(CACHE_VAR_HELPSTRING CACHE ${CACHE_VAR} PROPERTY HELPSTRING)
  IF(CACHE_VAR_HELPSTRING STREQUAL
      "No help, variable specified on the command line."
      )
    LIST(APPEND COMMAND_LINE_VARS ${CACHE_VAR})
  ENDIF()
ENDFOREACH()

# Default to "system" for all SYSTEM_LIBRARIES
IF(WITH_SYSTEM_LIBS)
  FOREACH(SYSTEM_LIB ${SYSTEM_LIBRARIES})
    LIST(FIND COMMAND_LINE_VARS WITH_${SYSTEM_LIB} FOUNDIT)
    IF(FOUNDIT LESS 0)
      SET(WITH_${SYSTEM_LIB} "system")
      SET(WITH_${SYSTEM_LIB} "system" CACHE STRING
        "Use system rather than bundled" FORCE)
      MESSAGE(STATUS "Adding -DWITH_${SYSTEM_LIB}=${WITH_${SYSTEM_LIB}}")
    ELSE()
      MESSAGE(STATUS
        "Found WITH_${SYSTEM_LIB}=${WITH_${SYSTEM_LIB}} on command line")
    ENDIF()
  ENDFOREACH()
  # Don't redo the system analysis on subsequent runs of cmake
  UNSET(WITH_SYSTEM_LIBS)
  UNSET(WITH_SYSTEM_LIBS CACHE)
ENDIF()

# Add bundled or system zlib.
MYSQL_CHECK_ZLIB()

# Add bundled or system zstd.
MYSQL_CHECK_ZSTD()

IF(LINUX)
  # Look up patchelf(1) here, before doing SSL/KERBEROS/SASL/LDAP config.
  FIND_PROGRAM(PATCHELF_EXECUTABLE patchelf)
  ADD_CUSTOM_TARGET(copy_linux_custom_dlls)
  SET(KNOWN_CUSTOM_LIBRARIES "" CACHE INTERNAL "" FORCE)
ENDIF()

# If all custom packages are in the same directory:
IF(WITH_CUSTOM_LIBRARIES)
  FILE(GLOB WITH_SSL      "${WITH_CUSTOM_LIBRARIES}/openssl*")
  FILE(GLOB WITH_KERBEROS "${WITH_CUSTOM_LIBRARIES}/krb5*")
  FILE(GLOB WITH_SASL     "${WITH_CUSTOM_LIBRARIES}/cyrus-sasl*")
  FILE(GLOB WITH_LDAP     "${WITH_CUSTOM_LIBRARIES}/openldap*")
  FILE(GLOB WITH_CURL     "${WITH_CUSTOM_LIBRARIES}/curl*")
  FOREACH(WITH_OPT WITH_SSL WITH_KERBEROS WITH_SASL WITH_LDAP WITH_CURL)
    IF(IS_DIRECTORY ${${WITH_OPT}})
      SET(${WITH_OPT} ${${WITH_OPT}} CACHE INTERNAL "" FORCE)
      MESSAGE(STATUS "${WITH_OPT} ${${WITH_OPT}}")
    ELSE()
      MESSAGE(WARNING "${WITH_OPT} not found") # This should be FATAL_ERROR
    ENDIF()
  ENDFOREACH()
ENDIF()

# Add system/custom openssl.
MYSQL_CHECK_SSL()
MYSQL_CHECK_SSL_DLLS()

# Other system libs which use OpenSSL must be disabled.
# This includes: kerberos sasl ldap curl.
IF(ALTERNATIVE_SYSTEM_SSL)
  SET(WITH_AUTHENTICATION_LDAP OFF)
  SET(WITH_AUTHENTICATION_LDAP OFF CACHE INTERNAL "" FORCE)
  SET(WITH_AUTHENTICATION_CLIENT_PLUGINS OFF)
  SET(WITH_AUTHENTICATION_CLIENT_PLUGINS OFF CACHE INTERNAL "" FORCE)
  SET(WITH_CURL "bundled")
  SET(WITH_CURL "bundled" CACHE INTERNAL "" FORCE)
  MESSAGE(STATUS "Disabling all authentication plugins.")
ENDIF()

# Add Kerberos library
MYSQL_CHECK_KERBEROS()
MYSQL_CHECK_KERBEROS_DLLS()

# Add SASL library
MYSQL_CHECK_SASL()
MYSQL_CHECK_SASL_DLLS()

# Add LDAP library
MYSQL_CHECK_LDAP()
MYSQL_CHECK_LDAP_DLLS()

# Add Windows specific jemalloc DLL
IF(WIN32)
  MYSQL_CHECK_WIN_JEMALLOC()
ENDIF()

# Custom versions of LDAP / SASL / SSL.
IF(LINUX AND KNOWN_CUSTOM_LIBRARIES)
  # Custom libraries are not targets, we install them as FILES.
  # INSTALL_RPATH must must be set depending on binary type
  # EXECUTABLE MODULE_LIBRARY SHARED_LIBRARY, see ADD_INSTALL_RPATH_FOR_OPENSSL
  SET(LINUX_INSTALL_RPATH_ORIGIN 1)

  # Debug versions of plugins may be installed to <root>/lib/plugin/debug
  FOREACH(LINK_FLAG
      CMAKE_MODULE_LINKER_FLAGS_DEBUG
      CMAKE_SHARED_LINKER_FLAGS_DEBUG
      )
    STRING_APPEND(${LINK_FLAG} " -Wl,-rpath,'\$ORIGIN/../../private'")
    MESSAGE(STATUS "${LINK_FLAG} ${${LINK_FLAG}}")
  ENDFOREACH()
  IF(NOT PATCHELF_EXECUTABLE)
    MESSAGE(FATAL_ERROR "Please install the patchelf(1) utility.")
  ENDIF()
  VERIFY_CUSTOM_LIBRARY_DEPENDENCIES()
ENDIF()

IF(WITH_AUTHENTICATION_LDAP)
  IF(LINUX AND KNOWN_CUSTOM_LIBRARIES)
    # LDAP / SASL / KERBEROS / SSL must all be "system" or "custom", not a mix.
    IF(WITH_LDAP STREQUAL "system" OR
        WITH_SASL STREQUAL "system" OR
#       WITH_KERBEROS STREQUAL "system" OR # enable this later
        WITH_SSL STREQUAL "system")
      MESSAGE(WARNING "-DWITH_AUTHENTICATION_LDAP=ON")
      MESSAGE(FATAL_ERROR "Inconsistent options for LDAP/SASL/KERBEROS/SSL")
    ENDIF()
  ELSEIF(WIN32)
    # system LDAP, but SASL needs to be explicitly added
    IF(NOT SASL_LIBRARY_DLL OR NOT SASL_SCRAM_PLUGIN)
      MESSAGE(WARNING "-DWITH_AUTHENTICATION_LDAP=ON")
      MESSAGE(FATAL_ERROR "Missing SASL and/or SCRAM libraries.")
    ENDIF()
  ELSEIF(APPLE)
    # system LDAP and SASL, but there is no scram plugin.
  ELSEIF(SOLARIS)
    # 11.3 has system LDAP and SASL, but there is no scram plugin.
  ELSE()
    # We must have "system" LDAP, KERBEROS and SASL (SSL is always required)
    SET(LDAP_WARN_GIVEN)
    SET(SASL_WARN_GIVEN)
    SET(KERBEROS_WARN_GIVEN)
    WARN_MISSING_SYSTEM_LDAP(LDAP_WARN_GIVEN)
    WARN_MISSING_SYSTEM_SASL(SASL_WARN_GIVEN)
    WARN_MISSING_SYSTEM_KERBEROS(KERBEROS_WARN_GIVEN)
    IF(LDAP_WARN_GIVEN OR SASL_WARN_GIVEN OR KERBEROS_WARN_GIVEN)
      # SUSE linux: may or may not have SCRAM, do not break the build.
      IF(LINUX_SUSE)
        MESSAGE(WARNING
          "-DWITH_AUTHENTICATION_LDAP=ON, but missing system libraries")
      ELSE()
        MESSAGE(FATAL_ERROR
          "-DWITH_AUTHENTICATION_LDAP=ON, but missing system libraries")
      ENDIF()
    ENDIF()
  ENDIF()
ENDIF()

IF(WITH_AUTHENTICATION_KERBEROS)
  SET(KERBEROS_GSSAPI_WARN_GIVEN)
  WARN_MISSING_SYSTEM_KERBEROS_GSSAPI(KERBEROS_GSSAPI_WARN_GIVEN)
  IF(KERBEROS_GSSAPI_WARN_GIVEN)
    MESSAGE(WARNING
      "-DWITH_AUTHENTICATION_KERBEROS=ON, but missing system libraries")
  ENDIF()
ENDIF()

# Add system/bundled editline.
MYSQL_CHECK_EDITLINE()
SET(EDITLINE_WARN_GIVEN)
WARN_MISSING_SYSTEM_EDITLINE(EDITLINE_WARN_GIVEN)
IF(EDITLINE_WARN_GIVEN)
  MESSAGE(FATAL_ERROR
    "-DWITH_EDITLINE=system, but missing system libraries.\n"
    "You can use -DWITH_EDITLINE=bundled instead."
    )
ENDIF()

# Add libevent
MYSQL_CHECK_LIBEVENT()
UNSET(LIBEVENT_WARN_GIVEN)
WARN_MISSING_SYSTEM_LIBEVENT(LIBEVENT_WARN_GIVEN)
IF(LIBEVENT_WARN_GIVEN)
  MESSAGE(FATAL_ERROR
    "-DWITH_LIBEVENT=system, but missing system libraries.\n"
    "You can use -DWITH_LIBEVENT=bundled instead."
    )
ENDIF()

# Add lz4 library
MYSQL_CHECK_LZ4()
# Add icu library
IF(NOT WITHOUT_SERVER)
  MYSQL_CHECK_ICU()
ENDIF()
# Add protoc and libprotobuf
IF(NOT WITHOUT_SERVER)
  MYSQL_CHECK_PROTOBUF()
  MYSQL_CHECK_PROTOBUF_DLLS()
ENDIF()

IF(NOT WITHOUT_SERVER)
  # Check BISON_VERSION and set BISON_FLAGS_WARNINGS
  # Do it here, before any other cmake file does FIND_PACKAGE(BISON REQUIRED)
  # so that our search for custom bison installations can run first.
  INCLUDE(bison)
ENDIF()

# Try and set CURL_LIBRARY
MYSQL_CHECK_CURL()

SET(CURL_WARN_GIVEN)
WARN_MISSING_SYSTEM_CURL(CURL_WARN_GIVEN)
IF(CURL_WARN_GIVEN)
  MESSAGE(FATAL_ERROR
    "-DWITH_CURL=system, but missing system libraries.\n"
    "You can disable CURL with -DWITH_CURL=0"
    )
ENDIF()
# Add RapidJSON library.
MYSQL_CHECK_RAPIDJSON()

# Look for fido2 library
MYSQL_CHECK_FIDO()
MYSQL_CHECK_FIDO_DLLS()

IF(APPLE)
  GET_FILENAME_COMPONENT(HOMEBREW_BASE ${HOMEBREW_HOME} DIRECTORY)
  IF(EXISTS ${HOMEBREW_BASE}/include/boost)
    FOREACH(SYSTEM_LIB ICU LIBEVENT LZ4 PROTOBUF ZSTD FIDO)
      IF(WITH_${SYSTEM_LIB} STREQUAL "system")
        MESSAGE(FATAL_ERROR
          "WITH_${SYSTEM_LIB}=system is not compatible with Homebrew boost\n"
          "MySQL depends on ${BOOST_PACKAGE_NAME} with a set of patches.\n"
          "Including headers from ${HOMEBREW_BASE}/include "
          "will break the build.\n"
          "Please use WITH_${SYSTEM_LIB}=bundled\n"
          "or do 'brew uninstall boost' or 'brew unlink boost'"
          )
      ENDIF()
    ENDFOREACH()
  ENDIF()
  # Ensure that we look in /usr/local/include or /opt/homebrew/include
  FOREACH(SYSTEM_LIB ICU LIBEVENT LZ4 PROTOBUF ZSTD FIDO)
    IF(WITH_${SYSTEM_LIB} STREQUAL "system")
      INCLUDE_DIRECTORIES(SYSTEM ${HOMEBREW_BASE}/include)
      BREAK()
    ENDIF()
  ENDFOREACH()
ENDIF()

IF(WITH_AUTHENTICATION_FIDO OR WITH_AUTHENTICATION_CLIENT_PLUGINS)
  IF(WITH_FIDO STREQUAL "system" AND
    NOT WITH_SSL STREQUAL "system")
      MESSAGE(WARNING "-DWITH_AUTHENTICATION_FIDO=ON")
      MESSAGE(FATAL_ERROR "Inconsistent options for FIDO/SSL")
  ENDIF()

  # FIDO (or libudev) missing, warn about what is missing, and break the build.
  IF(WITH_AUTHENTICATION_FIDO AND NOT FIDO_FOUND)
    SET(UDEV_WARN_MISSING)
    SET(FIDO_WARN_MISSING)
    WARN_MISSING_SYSTEM_UDEV(UDEV_WARN_MISSING)
    WARN_MISSING_SYSTEM_FIDO(FIDO_WARN_MISSING)
    MESSAGE(FATAL_ERROR
      "-DWITH_AUTHENTICATION_FIDO=ON, but missing required libraries")
  ENDIF()
ENDIF()

MACRO(MY_TARGET_INCLUDE_SYSTEM_DIRECTORIES TARGET LIBRARY)
  IF(${WITH_${LIBRARY}} STREQUAL "bundled")
    TARGET_INCLUDE_DIRECTORIES(${TARGET} SYSTEM BEFORE PRIVATE
      ${${LIBRARY}_INCLUDE_DIR})
  ELSE()
    TARGET_INCLUDE_DIRECTORIES(${TARGET} SYSTEM PRIVATE
      ${${LIBRARY}_INCLUDE_DIR})
  ENDIF()
ENDMACRO()

IF(LINUX)
  # Check for pthread_setname_np
  CHECK_C_SOURCE_COMPILES("
  #include <pthread.h>

  int main(int ac, char **av)
  {
    const char *thread_name= 0;
    int ret = pthread_setname_np(pthread_self(), thread_name);
    return ret;
  }"
  HAVE_PTHREAD_SETNAME_NP)
ENDIF()

# Cannot use system tirpc with custom OpenSSL
IF(WITH_SSL_PATH AND (LINUX_RHEL8 OR LINUX_RHEL9))
  SET(WITH_TIRPC "bundled")
ENDIF()

IF(WITH_TIRPC STREQUAL "bundled")
  ADD_SUBDIRECTORY(extra/tirpc)
ENDIF()

IF(WITH_PROTOBUF STREQUAL "bundled" OR WITH_FIDO STREQUAL "bundled")
  # Protobuf library is a target, installed to <root>/${INSTALL_PRIV_LIBDIR}
  # INSTALL_RPATH must be set for all binaries linking with libprotobuf.
  IF(WITH_PROTOBUF STREQUAL "bundled")
    SET(UNIX_INSTALL_RPATH_ORIGIN_PRIV_LIBDIR 1)
    ADD_SUBDIRECTORY(extra/protobuf)
  ENDIF()

  # The Fido library is a target, installed to <root>/${INSTALL_PRIV_LIBDIR}
  # INSTALL_RPATH must be set for all binaries linking with libfido2.
  IF(WITH_FIDO STREQUAL "bundled")
    SET(INSTALL_RPATH_FOR_FIDO2 1)

    # Do not break the build here in case of missing libudev on Linux.
    SET(UDEV_WARN_MISSING FALSE)
    WARN_MISSING_SYSTEM_UDEV(UDEV_WARN_MISSING)
    IF((WITH_AUTHENTICATION_FIDO OR WITH_AUTHENTICATION_CLIENT_PLUGINS)
        AND NOT UDEV_WARN_MISSING)
      # Silence warning about CMP0075
      CMAKE_PUSH_CHECK_STATE()
      SET(CMAKE_REQUIRED_LIBRARIES)
      ADD_SUBDIRECTORY(${CMAKE_SOURCE_DIR}/${CBOR_BUNDLE_SRC_PATH})
      ADD_SUBDIRECTORY(${CMAKE_SOURCE_DIR}/${FIDO_BUNDLE_SRC_PATH})
      CMAKE_POP_CHECK_STATE()
    ENDIF()
  ENDIF()

  IF(NOT APPLE AND NOT WIN32)
    # Debug versions of plugins may be installed to <root>/lib/plugin/debug
    FOREACH(LINK_FLAG
        CMAKE_MODULE_LINKER_FLAGS_DEBUG
        CMAKE_SHARED_LINKER_FLAGS_DEBUG
        )
      STRING_APPEND(${LINK_FLAG} " -Wl,-rpath,'\$ORIGIN/../../private'")
      MESSAGE(STATUS "${LINK_FLAG} ${${LINK_FLAG}}")
    ENDFOREACH()
  ENDIF()
ENDIF()

#
# Setup maintainer mode options by the end. Platform checks are
# not run with the warning options as to not perturb fragile checks
# (i.e. do not make warnings into errors).
#
# Why don't these flags affect the entire build?
# Because things may already have been included with ADD_SUBDIRECTORY
#
OPTION(MYSQL_MAINTAINER_MODE
       "MySQL maintainer-specific development environment" OFF)

INCLUDE(maintainer)
INCLUDE(msvc_cppcheck)
DO_MSVC_CPPCHECK()

IF(WITH_UNIT_TESTS)
  ENABLE_TESTING()
  INCLUDE(googletest)
ELSE()
  # In case we switch from ON to OFF, we do not want ctest
  # to pick up any obsolete test executables.
  IF(EXISTS "${CMAKE_BINARY_DIR}/CTestTestfile.cmake")
    FILE(GLOB_RECURSE CTEST_TESTFILES "${CMAKE_BINARY_DIR}/CTestTestfile.cmake")
    FILE(REMOVE ${CTEST_TESTFILES})
  ENDIF()
ENDIF()

IF(WITH_ICU STREQUAL "bundled")
  IF(IS_ABSOLUTE "${INSTALL_PRIV_LIBDIR}")
    SET(MYSQL_ICU_DATADIR "${INSTALL_PRIV_LIBDIR}"
      CACHE PATH "default MySQL ICU data directory")
  ELSE()
    SET(MYSQL_ICU_DATADIR "${DEFAULT_MYSQL_HOME}/${INSTALL_PRIV_LIBDIR}"
      CACHE PATH "default MySQL ICU data directory")
  ENDIF()

  # Write an explicit symlink in CMAKE_BINARY_DIR, so we can find
  # the ICU data directory when running in the build sandbox.
  # See setting of mysql_home in calculate_mysql_home_from_my_progname().
  FILE(TO_NATIVE_PATH "${CMAKE_SOURCE_DIR}/extra/icu" ICU_DATA_SOURCE_DIR)
  FILE(WRITE "${CMAKE_BINARY_DIR}/${ICUDT_DIR}.lnk" "${ICU_DATA_SOURCE_DIR}")
ENDIF()

# The hypergraph optimizer is default on only for debug builds.
IF(CMAKE_BUILD_TYPE_UPPER STREQUAL "DEBUG" OR WITH_DEBUG)
  SET(WITH_HYPERGRAPH_OPTIMIZER_DEFAULT ON)
ELSE()
  SET(WITH_HYPERGRAPH_OPTIMIZER_DEFAULT OFF)
ENDIF()
OPTION(WITH_HYPERGRAPH_OPTIMIZER
  "Allow use of the hypergraph join optimizer"
  ${WITH_HYPERGRAPH_OPTIMIZER_DEFAULT}
  )

# Utility target to build every executable tagged with ADD_TEST.
ADD_CUSTOM_TARGET(unittest_all)

# Utility target to build every target added with MYSQL_ADD_PLUGIN.
ADD_CUSTOM_TARGET(plugin_all)

# Utility target to build every target added with MYSQL_ADD_COMPONENT.
ADD_CUSTOM_TARGET(component_all)

# Utility target to generate all sources from .proto files.
ADD_CUSTOM_TARGET(protobuf_generated_all)

# Utility target to generate everything created by MYSQL_ADD_EXECUTABLE().
ADD_CUSTOM_TARGET(executable_all)

# We may run clang-tidy on individual .h files. Tell clang to treat them as C++.
# To generate entries for header files in compile_commands.json:
# make clang_tidy_prerequisites, see below.
# cd CMAKE_SOURCE_DIR
# compdb -p build-clang-tidy list > foo
# mv foo build-clang-tidy/compile_commands.json
# cd build-clang-tidy
# clang-tidy -p . ../include/lex_string.h
IF(MY_COMPILER_IS_CLANG AND CMAKE_EXPORT_COMPILE_COMMANDS)
  # See <cmake source root>/Modules/CMakeCXXInformation.cmake
  SET(CMAKE_CXX_COMPILE_OBJECT
    "<CMAKE_CXX_COMPILER> <DEFINES> <INCLUDES> <FLAGS> ")
  STRING_APPEND(CMAKE_CXX_COMPILE_OBJECT " --language=c++ ")
  STRING_APPEND(CMAKE_CXX_COMPILE_OBJECT " -o <OBJECT> -c <SOURCE>")
  # We cannot do this, since <FLAGS> will be used for linking as well:
  # STRING_APPEND(CMAKE_CXX_FLAGS " --language=c++")
ENDIF()

# Utility target to generate sources needed for clang-tidy.
# Example usage:
#
# export CC=clang
# export CXX=clang++
# mkdir build-clang-tidy; cd build-clang-tidy
# cmake .. -DCMAKE_EXPORT_COMPILE_COMMANDS=1 -DWITH_SYSTEM_LIBS=1
# make clang_tidy_prerequisites
# clang-tidy -p .
#     --checks=modernize-use-nullptr,-clang-analyzer-core.NullDereference
#     ../sql/sys_vars.cc
ADD_CUSTOM_TARGET(clang_tidy_prerequisites)
ADD_DEPENDENCIES(clang_tidy_prerequisites protobuf_generated_all)
ADD_DEPENDENCIES(clang_tidy_prerequisites GenError)

ADD_SUBDIRECTORY(include)
ADD_SUBDIRECTORY(strings)
ADD_SUBDIRECTORY(vio)
ADD_SUBDIRECTORY(mysys)
ADD_SUBDIRECTORY(libmysql)
ADD_SUBDIRECTORY(libbinlogevents)
ADD_SUBDIRECTORY(libbinlogstandalone)
ADD_SUBDIRECTORY(libchangestreams)
ADD_SUBDIRECTORY(sql-common/oci)

IF(NOT WITHOUT_SERVER)
  IF(WITH_UNIT_TESTS)
    IF(WIN32)
      SET(WITH_SHARED_UNITTEST_LIBRARY_DEFAULT OFF)
    ELSE()
      SET(WITH_SHARED_UNITTEST_LIBRARY_DEFAULT ON)
    ENDIF()
    OPTION(WITH_SHARED_UNITTEST_LIBRARY
      "Build a shared library for unit tests which depend on the entire server"
      ${WITH_SHARED_UNITTEST_LIBRARY_DEFAULT}
      )
  ENDIF()

  # Add storage engines and plugins.
  SET (MYSQLD_STATIC_PLUGIN_LIBS "" CACHE INTERNAL "")
  CONFIGURE_PLUGINS()
ELSE()
  IF(EXISTS "${CMAKE_SOURCE_DIR}/storage/ndb")
    # We may still want Cluster client libraries, use -DWITH_NDB=1
    ADD_SUBDIRECTORY(storage/ndb)
  ENDIF()
ENDIF()

IF(WITHOUT_SERVER)
  ADD_SUBDIRECTORY(components/libminchassis)
ELSE()
  CONFIGURE_COMPONENTS()
ENDIF()

IF(WITH_UNIT_TESTS)
  ADD_SUBDIRECTORY(unittest)
  ADD_SUBDIRECTORY(unittest/examples)
  ADD_SUBDIRECTORY(unittest/mytap)
  ADD_SUBDIRECTORY(unittest/mytap/t)
ENDIF()

ADD_SUBDIRECTORY(client)
ADD_SUBDIRECTORY(utilities)
ADD_SUBDIRECTORY(share)
ADD_SUBDIRECTORY(libservices)

IF(NOT WITHOUT_SERVER)
  ADD_SUBDIRECTORY(testclients)
  ADD_SUBDIRECTORY(sql)
ENDIF()

# Some unit tests need to link with the entire server, use this library:
IF(NOT WITHOUT_SERVER AND WITH_UNIT_TESTS)

  IF(WITH_SHARED_UNITTEST_LIBRARY)
    MERGE_LIBRARIES_SHARED(server_unittest_library SKIP_INSTALL LINK_PUBLIC
      sql_main
      ${MYSQLD_STATIC_PLUGIN_LIBS}
      minchassis
      ext::icu
      # Import some core symbols. Other symbols needed by the unit test
      # executables are pulled in transitively by symbol dependencies.
      #
      # Since everything has visibility("default") the library will
      # export every symbol pulled in from the source libraries.
      #
      # If some symbols are still missing, they will be picked up from
      # dependent libraries, since we LINK_PUBLIC.
      # To see what symbols we need to import, remove LINK_PUBLIC above.
      EXPORTS
      builtin_perfschema_plugin            # Pulls in the whole server.
      mysql_service_mysql_rwlock_v1        # Pulls in minchassis
      )
    IF(HAVE_LIBNUMA)
      TARGET_LINK_LIBRARIES(server_unittest_library PUBLIC numa)
    ENDIF()
    IF(WITH_LTO OR CMAKE_COMPILER_FLAG_WITH_LTO)
      # We get ODR violations because of multiple bison parsers.
      MY_CHECK_CXX_COMPILER_WARNING("error=odr" HAS_WARN_FLAG)
      IF(HAS_WARN_FLAG)
        MY_TARGET_LINK_OPTIONS(server_unittest_library "${HAS_WARN_FLAG}")
      ENDIF()
      # We may get errors in boost/geometry/srs/projections/proj/ob_tran.hpp
      # and several other boost files.
      MY_CHECK_CXX_COMPILER_WARNING("error=maybe-uninitialized" HAS_WARN_FLAG)
      IF(HAS_WARN_FLAG)
        MY_TARGET_LINK_OPTIONS(server_unittest_library "${HAS_WARN_FLAG}")
      ENDIF()
      # The mold linker and RelWithDebInfo build complains about
      # the new expression in Rpl_info_values::init()
      IF(MY_COMPILER_IS_GNU)
        MY_TARGET_LINK_OPTIONS(server_unittest_library
          "-Wno-alloc-size-larger-than")
      ENDIF()
    ENDIF()
  ELSE()
    SET(DUMMY_SOURCE_FILE ${CMAKE_BINARY_DIR}/server_unittest_library.c)
    ADD_CUSTOM_COMMAND(
      OUTPUT  ${DUMMY_SOURCE_FILE}
      COMMAND ${CMAKE_COMMAND} -E touch ${DUMMY_SOURCE_FILE}
      DEPENDS sql_main
      )
    ADD_LIBRARY(server_unittest_library STATIC ${DUMMY_SOURCE_FILE})
    TARGET_LINK_LIBRARIES(server_unittest_library perfschema)
    TARGET_LINK_LIBRARIES(server_unittest_library sql_main)
    TARGET_LINK_LIBRARIES(server_unittest_library minchassis)
    TARGET_LINK_LIBRARIES(server_unittest_library ext::icu)
  ENDIF()
ENDIF()

# scripts/mysql_config depends on client and server targets loaded above.
# It is referenced by some of the directories below, so we insert it here.
ADD_SUBDIRECTORY(scripts)

IF(NOT WITHOUT_SERVER)
  ADD_SUBDIRECTORY(mysql-test)
  ADD_SUBDIRECTORY(mysql-test/lib/My/SafeProcess)
  ADD_SUBDIRECTORY(support-files)
  IF(WITH_INTERNAL)
    ADD_SUBDIRECTORY(internal)
  ENDIF()
ENDIF()

INCLUDE(cmake/abi_check.cmake)
INCLUDE(cmake/tags.cmake)

CONFIGURE_FILE(config.h.cmake   ${CMAKE_BINARY_DIR}/include/my_config.h)
CONFIGURE_FILE(config.h.cmake   ${CMAKE_BINARY_DIR}/include/config.h)
CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/include/mysql_version.h.in
               ${CMAKE_BINARY_DIR}/include/mysql_version.h )
CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/sql/sql_builtin.cc.in
    ${CMAKE_BINARY_DIR}/sql/sql_builtin.cc)

# depends on mysql_version.h to exist
IF(NOT WITHOUT_SERVER)
  IF(WITH_ROUTER)
    ADD_SUBDIRECTORY(router)
  ENDIF()
ENDIF()

IF(ENABLE_GCOV)
  INCLUDE(fastcov)
ENDIF()

IF(UNIX)
  ADD_SUBDIRECTORY(man)
ENDIF()

IF(LINUX AND NOT WITHOUT_SERVER)
  ADD_SUBDIRECTORY(packaging/rpm-common)
  IF(LINUX_DEB_PLATFORM)
    ADD_SUBDIRECTORY(packaging/deb-in)
  ENDIF()
  IF(LINUX_FEDORA)
    ADD_SUBDIRECTORY(packaging/rpm-fedora)
  ENDIF()
  IF(LINUX_RHEL)
    ADD_SUBDIRECTORY(packaging/rpm-oel)
    ADD_SUBDIRECTORY(packaging/rpm-docker)
  ENDIF()
  IF(LINUX_SUSE)
    ADD_SUBDIRECTORY(packaging/rpm-sles)
  ENDIF()
ENDIF()

GET_PROPERTY(CWD_DEFINITIONS DIRECTORY PROPERTY COMPILE_DEFINITIONS)
CONFIGURE_FILE(
    ${CMAKE_SOURCE_DIR}/cmake/info_macros.cmake.in
    ${CMAKE_BINARY_DIR}/info_macros.cmake @ONLY)

SET(KNOWN_CONVENIENCE_LIBRARIES "" CACHE INTERNAL "")
# Add to the 'make clean' target.
# With this you need to do 'cmake .' before doing 'make' again.
SET_PROPERTY(DIRECTORY APPEND PROPERTY ADDITIONAL_MAKE_CLEAN_FILES
  "${CMAKE_BINARY_DIR}/archive_output_directory"
  "${CMAKE_BINARY_DIR}/library_output_directory"
  )

# Handle the "INFO_*" files.
INCLUDE(${CMAKE_BINARY_DIR}/info_macros.cmake)
# Source: This can be done during the cmake phase, all information is
# available, but should be repeated on each "make" just in case someone
# does "cmake ; make ; git pull ; make".
CREATE_INFO_SRC(${CMAKE_BINARY_DIR}/Docs)
MY_ADD_CUSTOM_TARGET(INFO_SRC ALL
  COMMAND ${CMAKE_COMMAND} -P ${CMAKE_SOURCE_DIR}/cmake/info_src.cmake
  BYPRODUCTS ${CMAKE_BINARY_DIR}/Docs/INFO_SRC
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
  COMMENT "Generating Docs/INFO_SRC"
)

IF(HAVE_BUILD_ID_SUPPORT AND NOT WITHOUT_SERVER)
  # Show it by default in pushuild, but not for developers.
  IF(DEFINED ENV{PB2WORKDIR})
    OPTION(SHOW_BUILD_ID "Show mysqld build id as final target" ON)
  ELSE()
    OPTION(SHOW_BUILD_ID "Show mysqld build id as final target" OFF)
  ENDIF()

  IF(SHOW_BUILD_ID)
    # Show build id in the build log, after everything else has bee built.
    ADD_CUSTOM_TARGET(show_build_id ALL
      COMMAND $<TARGET_FILE:mysqld> --no-defaults --help | grep BuildID
      COMMENT "Showing mysqld build id"
      VERBATIM
      )
    ADD_DEPENDENCIES(show_build_id mysqld)
    # Build all kinds of binaries first.
    ADD_DEPENDENCIES(show_build_id
      component_all
      executable_all
      plugin_all
      unittest_all
      )
    IF(WITH_ROUTER)
      ADD_DEPENDENCIES(show_build_id mysqlrouter_all)
    ENDIF()
    # Other "ALL" targets.
    ADD_DEPENDENCIES(show_build_id
      GenSysSchemaC
      # INFO_BIN see below
      run_libmysql_api_test
      )
  ENDIF()
ENDIF()

UNSET(MYSQLD_EXECUTABLE_FOR_INFO_BIN)
IF(HAVE_BUILD_ID_SUPPORT AND NOT WITHOUT_SERVER)
  SET(MYSQLD_EXECUTABLE_FOR_INFO_BIN
    "-DMYSQLD_EXECUTABLE=$<TARGET_FILE:mysqld>")
ENDIF()

# Build flags: This must be postponed to the make phase.
ADD_CUSTOM_COMMAND(
  OUTPUT ${CMAKE_BINARY_DIR}/Docs/INFO_BIN
  COMMAND ${CMAKE_COMMAND}
  ${MYSQLD_EXECUTABLE_FOR_INFO_BIN}
  -DCMAKE_BUILD_TYPE=$<CONFIG>
  -P ${CMAKE_SOURCE_DIR}/cmake/info_bin.cmake
  COMMENT "Generating Docs/INFO_BIN"
  DEPENDS ${CMAKE_BINARY_DIR}/CMakeCache.txt
  )
MY_ADD_CUSTOM_TARGET(INFO_BIN ALL
  DEPENDS ${CMAKE_BINARY_DIR}/Docs/INFO_BIN
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)
# Adding $<TARGET_FILE:mysqld> seems to a dependency on 'mysqld'.
# Add an explicit one anyways.
IF(MYSQLD_EXECUTABLE_FOR_INFO_BIN)
  ADD_DEPENDENCIES(INFO_BIN mysqld)
  IF(TARGET show_build_id)
    ADD_DEPENDENCIES(show_build_id INFO_BIN)
  ENDIF()
ENDIF()

# Packaging
IF(WIN32)
  OPTION(BUNDLE_RUNTIME_LIBRARIES "INSTALL runtime libraries" OFF)
  IF(BUNDLE_RUNTIME_LIBRARIES)
    SET(RUNTIME_LIBRARIES
      msvcp140
      ucrtbase
      vcruntime140
      )
    FOREACH(LIB ${RUNTIME_LIBRARIES})
      SET(LIB_PATH "C:/Windows/System32/${LIB}.dll")
      SET(LIB_D_PATH "C:/Windows/System32/${LIB}d.dll")
      FOREACH(LIBRARY_PATH ${LIB_PATH} ${LIB_D_PATH})
        IF(EXISTS "${LIBRARY_PATH}")
          MESSAGE(STATUS "INSTALL ${LIBRARY_PATH} to ${INSTALL_BINDIR}")
          INSTALL(FILES
            "${LIBRARY_PATH}" DESTINATION "${INSTALL_BINDIR}"
            COMPONENT SharedLibraries)
        ELSE()
          MESSAGE(STATUS "Could not find ${LIBRARY_PATH}")
        ENDIF()
      ENDFOREACH()
    ENDFOREACH()
  ENDIF()
ENDIF()

IF(WIN32)
  SET(CPACK_GENERATOR "ZIP")
ELSE()
  SET(CPACK_GENERATOR "TGZ")
ENDIF()
IF(WIN32)
  ADD_SUBDIRECTORY(packaging/WiX)
ENDIF()
IF(SOLARIS)
  ADD_SUBDIRECTORY(packaging/solaris)
ENDIF()

# Produce separate tarballs for each INSTALL COMPONENT
IF(PACK_SEPARATE_COMPONENTS)
  SET(CPACK_ARCHIVE_COMPONENT_INSTALL ON)
  SET(CPACK_COMPONENTS_GROUPING IGNORE)
ENDIF()

#
# RPM installs documentation directly from the source tree
#
IF(NOT INSTALL_LAYOUT MATCHES "RPM")
  INSTALL(FILES
    README
    LICENSE
    DESTINATION ${INSTALL_DOCREADMEDIR} COMPONENT Readme)
  INSTALL(FILES
    ${CMAKE_BINARY_DIR}/Docs/INFO_SRC
    ${CMAKE_BINARY_DIR}/Docs/INFO_BIN
    DESTINATION ${INSTALL_DOCDIR} COMPONENT Info
    )

  # MYSQL_DOCS_LOCATON is used in "make dist",
  # points to the documentation directory
  SET(MYSQL_DOCS_LOCATION "" CACHE PATH
    "Location from where documentation is copied")
  MARK_AS_ADVANCED(MYSQL_DOCS_LOCATION)
  INSTALL(DIRECTORY Docs/ DESTINATION ${INSTALL_DOCDIR}
    COMPONENT Documentation
    PATTERN "Makefile.*" EXCLUDE
    PATTERN "glibc*" EXCLUDE
    PATTERN "linuxthreads.txt" EXCLUDE
    PATTERN "myisam.txt" EXCLUDE
    PATTERN "sp-imp-spec.txt" EXCLUDE
    PATTERN "README.build" EXCLUDE
  )
ENDIF()

# Now that we're done with all ADD_SUBDIRECTORY and thus all feature tests,
# we can safely enable ASan on Clang/Win32 if needed.
IF(HAVE_ASAN AND WIN32_CLANG)
  STRING_APPEND(CMAKE_C_FLAGS
    " -fsanitize=address -fsanitize-address-use-after-scope")
  STRING_APPEND(CMAKE_CXX_FLAGS
    " -fsanitize=address -fsanitize-address-use-after-scope")
ENDIF()

IF(UNIX)
  EXECUTE_PROCESS(
    COMMAND ${CMAKE_COMMAND} -E make_directory library_output_directory
    COMMAND ${CMAKE_COMMAND} -E make_directory plugin_output_directory
    COMMAND ${CMAKE_COMMAND} -E make_directory runtime_output_directory
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    )
  IF(SASL_CUSTOM_LIBRARY)
    EXECUTE_PROCESS(
      COMMAND ${CMAKE_COMMAND} -E make_directory library_output_directory/sasl2
    )
  ENDIF()
ENDIF()

# Pre-create directories for library_output_directory and
# plugin_output_directory, so create_symlink does not fail in ssl.cmake
# Do not pre-create runtime_output_directory/Debug or
# runtime_output_directory/RelWithDebInfo, mtr gets confused, and must be
# run with --vs-config=Debug or --vs-config=RelWithDebInfo in order to work.
IF(APPLE AND NOT BUILD_IS_SINGLE_CONFIG)
  FOREACH(dir
      library_output_directory
      plugin_output_directory
      )
    EXECUTE_PROCESS(
      COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/Debug
      COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/RelWithDebInfo
      COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/Release
      COMMAND ${CMAKE_COMMAND} -E make_directory ${dir}/MinSizeRel
      WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
      )
  ENDFOREACH()
ENDIF()

IF(UNIX)
  # Create convenience symlinks bin/ and lib/
  EXECUTE_PROCESS(
    COMMAND ${CMAKE_COMMAND} -E create_symlink
     ./runtime_output_directory bin
    COMMAND ${CMAKE_COMMAND} -E create_symlink
     ./library_output_directory lib
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
  )
  # See ./mysql-test/suite/innodb_fts/include/mecab.inc
  IF(MECAB_IPADIC_PARENT)
    EXECUTE_PROCESS(
      COMMAND ${CMAKE_COMMAND} -E create_symlink
      "${MECAB_IPADIC_PARENT}" mecab
      WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/library_output_directory
      )
  ENDIF()
ENDIF()

# Without any --plugin-dir=<directory name> the server will look in
# INSTALL_PLUGINDIR, which is "lib/plugin" for standalone builds.
# This means that by default, we create a symlink plugin@ in lib/.
# For other values of INSTALL_LAYOUT we split the INSTALL_PLUGINDIR path,
# create the necessary directories,
# and add a symlink `basename ${INSTALL_PLUGINDIR}`.
IF(UNIX AND BUILD_IS_SINGLE_CONFIG)
  GET_FILENAME_COMPONENT(INSTALL_PLUGINDIR_PATH ${INSTALL_PLUGINDIR} PATH)
  GET_FILENAME_COMPONENT(INSTALL_PLUGINDIR_NAME ${INSTALL_PLUGINDIR} NAME)
  IF(NOT IS_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH})
    # Create the given directories and their parents as needed.
    MESSAGE(STATUS "MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH}")
    FILE(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH})
  ENDIF()

  EXECUTE_PROCESS(
    COMMAND ${CMAKE_COMMAND} -E create_symlink
    ${CMAKE_BINARY_DIR}/plugin_output_directory ${INSTALL_PLUGINDIR_NAME}
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/${INSTALL_PLUGINDIR_PATH}
    )
ENDIF()

SET(DOXYGEN_OUTPUT_DIR "${CMAKE_CURRENT_BINARY_DIR}/doxygen")
FIND_PACKAGE(Doxygen)
IF(DOXYGEN_FOUND)
  # We want output on stdout in pushbuild.
  IF(DEFINED ENV{PB2WORKDIR})
    SET(REDIRECT_DOXYGEN_STDOUT_DEFAULT FALSE)
  ELSE()
    SET(REDIRECT_DOXYGEN_STDOUT_DEFAULT TRUE)
  ENDIF()
  OPTION(REDIRECT_DOXYGEN_STDOUT "Redirect doxygen output to a file"
    ${REDIRECT_DOXYGEN_STDOUT_DEFAULT})

  CONFIGURE_FILE(Doxyfile.in Doxyfile @ONLY)
  ADD_CUSTOM_TARGET(
    doxygen
    COMMAND ${CMAKE_COMMAND}
    -DDOXYGEN_EXECUTABLE=${DOXYGEN_EXECUTABLE}
    -DDOXYGEN_DOT_EXECUTABLE=${DOXYGEN_DOT_EXECUTABLE}
    -DDOXYFILE=${CMAKE_BINARY_DIR}/Doxyfile
    -DERROR_FILE=${CMAKE_BINARY_DIR}/doxyerror.log
    -DOUTPUT_FILE=${CMAKE_BINARY_DIR}/doxyoutput.log
    -DREDIRECT_DOXYGEN_STDOUT=${REDIRECT_DOXYGEN_STDOUT}
    -DSOURCE_DIR=${CMAKE_SOURCE_DIR}
    -DTOFIX_FILE=${CMAKE_BINARY_DIR}/tofix-all.log
    -DREGRESSION_FILE=${CMAKE_BINARY_DIR}/tofix-regressions.log
    -DIGNORE_FILE=${CMAKE_SOURCE_DIR}/Doxyfile-ignored
    -P ${CMAKE_SOURCE_DIR}/run_doxygen.cmake
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    COMMENT "Generating Doxygen documentation" VERBATIM
    )
  SET_PROPERTY(TARGET doxygen PROPERTY EXCLUDE_FROM_ALL TRUE)
  IF(WIN32)
    SET_PROPERTY(TARGET doxygen PROPERTY EXCLUDE_FROM_DEFAULT_BUILD TRUE)
  ENDIF()
ENDIF(DOXYGEN_FOUND)

MYSQL_ADD_EXECUTABLE(stack_direction
  ${CMAKE_SOURCE_DIR}/cmake/stack_direction.c
  SKIP_INSTALL
  )

INCLUDE(CPack)

# C/CXX compiler flags consist of:
# CPPFLAGS        Taken from environment, see above.
# ADD_DEFINITIONS In each individual CMakeLists.txt
# CMAKE_CXX_FLAGS From command line.
#                 We extend these in maintainer.cmake
#                 We extend these in compiler_options.cmake
# ENV{CXXFLAGS}   From environment, but environment is ignored if
#                 CMAKE_CXX_FLAGS is also given on command line
# CMAKE_CXX_FLAGS_${CMAKE_BUILD_TYPE}, typical initial cmake values:
#      DEBUG          -g
#      RELWITHDEBINFO -O2 -g -DNDEBUG
#      RELEASE        -O3 -DNDEBUG
#      MINSIZEREL     -Os -DNDEBUG
#
# The compiler command line is
# g++ DEFINITIONS CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_<configuration>
#
# So, if you want to use '-O3' for a RELWITHDEBINFO build, you should do:
# cmake -DCMAKE_CXX_FLAGS_RELWITHDEBINFO="-O3 -g -DNDEBUG"
#
# Note that CMakeCache.txt contains cmake builtins for these variables,
# *not* the values that will actually be used, which we report here:

IF(BUILD_IS_SINGLE_CONFIG)
  MESSAGE(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")
ENDIF()
GET_PROPERTY(cwd_definitions DIRECTORY PROPERTY COMPILE_DEFINITIONS)
MESSAGE(STATUS "COMPILE_DEFINITIONS: ${cwd_definitions}")
REPORT_CXX_FLAGS()
MESSAGE(STATUS "CMAKE_C_LINK_FLAGS: ${CMAKE_C_LINK_FLAGS}")
MESSAGE(STATUS "CMAKE_CXX_LINK_FLAGS: ${CMAKE_CXX_LINK_FLAGS}")
MESSAGE(STATUS "CMAKE_EXE_LINKER_FLAGS ${CMAKE_EXE_LINKER_FLAGS}")
MESSAGE(STATUS "CMAKE_MODULE_LINKER_FLAGS ${CMAKE_MODULE_LINKER_FLAGS}")
MESSAGE(STATUS "CMAKE_SHARED_LINKER_FLAGS ${CMAKE_SHARED_LINKER_FLAGS}")

# If user provided WITH_NDBCLUSTER keep it in cache
IF(HAVE_WITH_NDBCLUSTER)
  SET(WITH_NDBCLUSTER ${WITH_NDB} CACHE BOOL
    "Deprecated legacy option for MySQL Cluster")
ENDIF()

```








