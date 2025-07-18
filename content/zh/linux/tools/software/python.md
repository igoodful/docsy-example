---
title: python
description: python
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


- [https://repo.huaweicloud.com/python/3.12.0/](https://repo.huaweicloud.com/python/3.12.0/)

```bash
yum -y install bzip2 bzip2-devel openssl-devel openssl11 openssl11-devel gcc zlib zlib-devel libffi libffi-devel readline-devel libffi-devel


```



```bash
2024-08-24T21:20:13 [root@dev /root/local/Python-3.12.0] $ ./configure --help
`configure' configures python 3.12 to adapt to many kinds of systems.

Usage: ./configure [OPTION]... [VAR=VALUE]...

To assign environment variables (e.g., CC, CFLAGS...), specify them as
VAR=VALUE.  See below for descriptions of some of the useful variables.

Defaults for the options are specified in brackets.

Configuration:
  -h, --help              display this help and exit
      --help=short        display options specific to this package
      --help=recursive    display the short help of all the included packages
  -V, --version           display version information and exit
  -q, --quiet, --silent   do not print `checking ...' messages
      --cache-file=FILE   cache test results in FILE [disabled]
  -C, --config-cache      alias for `--cache-file=config.cache'
  -n, --no-create         do not create output files
      --srcdir=DIR        find the sources in DIR [configure dir or `..']

Installation directories:
  --prefix=PREFIX         install architecture-independent files in PREFIX
                          [/usr/local]
  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
                          [PREFIX]

By default, `make install' will install all the files in
`/usr/local/bin', `/usr/local/lib' etc.  You can specify
an installation prefix other than `/usr/local' using `--prefix',
for instance `--prefix=$HOME'.

For better control, use the options below.

Fine tuning of the installation directories:
  --bindir=DIR            user executables [EPREFIX/bin]
  --sbindir=DIR           system admin executables [EPREFIX/sbin]
  --libexecdir=DIR        program executables [EPREFIX/libexec]
  --sysconfdir=DIR        read-only single-machine data [PREFIX/etc]
  --sharedstatedir=DIR    modifiable architecture-independent data [PREFIX/com]
  --localstatedir=DIR     modifiable single-machine data [PREFIX/var]
  --runstatedir=DIR       modifiable per-process data [LOCALSTATEDIR/run]
  --libdir=DIR            object code libraries [EPREFIX/lib]
  --includedir=DIR        C header files [PREFIX/include]
  --oldincludedir=DIR     C header files for non-gcc [/usr/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
  --infodir=DIR           info documentation [DATAROOTDIR/info]
  --localedir=DIR         locale-dependent data [DATAROOTDIR/locale]
  --mandir=DIR            man documentation [DATAROOTDIR/man]
  --docdir=DIR            documentation root [DATAROOTDIR/doc/python]
  --htmldir=DIR           html documentation [DOCDIR]
  --dvidir=DIR            dvi documentation [DOCDIR]
  --pdfdir=DIR            pdf documentation [DOCDIR]
  --psdir=DIR             ps documentation [DOCDIR]

System types:
  --build=BUILD     configure for building on BUILD [guessed]
  --host=HOST       cross-compile to build programs to run on HOST [BUILD]

Optional Features:
  --disable-option-checking  ignore unrecognized --enable/--with options
  --disable-FEATURE       do not include FEATURE (same as --enable-FEATURE=no)
  --enable-FEATURE[=ARG]  include FEATURE [ARG=yes]
  --enable-universalsdk[=SDKDIR]
                          create a universal binary build. SDKDIR specifies
                          which macOS SDK should be used to perform the build,
                          see Mac/README.rst. (default is no)
  --enable-framework[=INSTALLDIR]
                          create a Python.framework rather than a traditional
                          Unix install. optional INSTALLDIR specifies the
                          installation path. see Mac/README.rst (default is
                          no)
  --enable-wasm-dynamic-linking
                          Enable dynamic linking support for WebAssembly
                          (default is no)
  --enable-wasm-pthreads  Enable pthread emulation for WebAssembly (default is
                          no)
  --enable-shared         enable building a shared Python library (default is
                          no)
  --enable-profiling      enable C-level code profiling with gprof (default is
                          no)
  --enable-pystats        enable internal statistics gathering (default is no)
  --enable-optimizations  enable expensive, stable optimizations (PGO, etc.)
                          (default is no)
  --enable-bolt           enable usage of the llvm-bolt post-link optimizer
                          (default is no)
  --enable-loadable-sqlite-extensions
                          support loadable extensions in the sqlite3 module,
                          see Doc/library/sqlite3.rst (default is no)
  --enable-ipv6           enable ipv6 (with ipv4) support, see
                          Doc/library/socket.rst (default is yes if supported)
  --enable-big-digits[=15|30]
                          use big digits (30 or 15 bits) for Python longs
                          (default is 30)]
  --disable-test-modules  don't build nor install test modules

Optional Packages:
  --with-PACKAGE[=ARG]    use PACKAGE [ARG=yes]
  --without-PACKAGE       do not use PACKAGE (same as --with-PACKAGE=no)
  --with-build-python=python3.12
                          path to build python binary for cross compiling
                          (default: _bootstrap_python or python3.12)
  --with-pkg-config=[yes|no|check]
                          use pkg-config to detect build options (default is
                          check)
  --with-universal-archs=ARCH
                          specify the kind of macOS universal binary that
                          should be created. This option is only valid when
                          --enable-universalsdk is set; options are:
                          ("universal2", "intel-64", "intel-32", "intel",
                          "32-bit", "64-bit", "3-way", or "all") see
                          Mac/README.rst
  --with-framework-name=FRAMEWORK
                          specify the name for the python framework on macOS
                          only valid when --enable-framework is set. see
                          Mac/README.rst (default is 'Python')
  --with-emscripten-target=[browser|node]
                          Emscripten platform
  --with-suffix=SUFFIX    set executable suffix to SUFFIX (default is empty,
                          yes is mapped to '.exe')
  --without-static-libpython
                          do not build libpythonMAJOR.MINOR.a and do not
                          install python.o (default is yes)
  --with-pydebug          build with Py_DEBUG defined (default is no)
  --with-trace-refs       enable tracing references for debugging purpose
                          (default is no)
  --with-assertions       build with C assertions enabled (default is no)
  --with-lto=[full|thin|no|yes]
                          enable Link-Time-Optimization in any build (default
                          is no)
  --with-strict-overflow  if 'yes', add -fstrict-overflow to CFLAGS, else add
                          -fno-strict-overflow (default is no)
  --with-dsymutil         link debug information into final executable with
                          dsymutil in macOS (default is no)
  --with-address-sanitizer
                          enable AddressSanitizer memory error detector,
                          'asan' (default is no)
  --with-memory-sanitizer enable MemorySanitizer allocation error detector,
                          'msan' (default is no)
  --with-undefined-behavior-sanitizer
                          enable UndefinedBehaviorSanitizer undefined
                          behaviour detector, 'ubsan' (default is no)
  --with-hash-algorithm=[fnv|siphash13|siphash24]
                          select hash algorithm for use in Python/pyhash.c
                          (default is SipHash13)
  --with-tzpath=<list of absolute paths separated by pathsep>
                          Select the default time zone search path for
                          zoneinfo.TZPATH
  --with-libs='lib1 ...'  link against additional libs (default is no)
  --with-system-expat     build pyexpat module using an installed expat
                          library, see Doc/library/pyexpat.rst (default is no)
  --with-system-libmpdec  build _decimal module using an installed libmpdec
                          library, see Doc/library/decimal.rst (default is no)
  --with-decimal-contextvar
                          build _decimal module using a coroutine-local rather
                          than a thread-local context (default is yes)
  --with-dbmliborder=db1:db2:...
                          override order to check db backends for dbm; a valid
                          value is a colon separated string with the backend
                          names `ndbm', `gdbm' and `bdb'.
  --with-doc-strings      enable documentation strings (default is yes)
  --with-pymalloc         enable specialized mallocs (default is yes)
  --with-freelists        enable object freelists (default is yes)
  --with-c-locale-coercion
                          enable C locale coercion to a UTF-8 based locale
                          (default is yes)
  --with-valgrind         enable Valgrind support (default is no)
  --with-dtrace           enable DTrace support (default is no)
  --with-libm=STRING      override libm math library to STRING (default is
                          system-dependent)
  --with-libc=STRING      override libc C library to STRING (default is
                          system-dependent)
  --with-platlibdir=DIRNAME
                          Python library directory name (default is "lib")
  --with-wheel-pkg-dir=PATH
                          Directory of wheel packages used by ensurepip
                          (default: none)
  --with(out)-readline[=editline|readline|no]
                          use libedit for backend or disable readline module
  --with-computed-gotos   enable computed gotos in evaluation loop (enabled by
                          default on supported compilers)
  --with-ensurepip[=install|upgrade|no]
                          "install" or "upgrade" using bundled pip (default is
                          upgrade)
  --with-openssl=DIR      root of the OpenSSL directory
  --with-openssl-rpath=[DIR|auto|no]
                          Set runtime library directory (rpath) for OpenSSL
                          libraries, no (default): don't set rpath, auto:
                          auto-detect rpath from --with-openssl and
                          pkg-config, DIR: set an explicit rpath
  --with-ssl-default-suites=[python|openssl|STRING]
                          override default cipher suites string, python: use
                          Python's preferred selection (default), openssl:
                          leave OpenSSL's defaults untouched, STRING: use a
                          custom string, python and STRING also set TLS 1.2 as
                          minimum TLS version
  --with-builtin-hashlib-hashes=md5,sha1,sha2,sha3,blake2
                          builtin hash modules, md5, sha1, sha2, sha3 (with
                          shake), blake2

Some influential environment variables:
  PKG_CONFIG  path to pkg-config utility
  PKG_CONFIG_PATH
              directories to add to pkg-config's search path
  PKG_CONFIG_LIBDIR
              path overriding pkg-config's built-in search path
  MACHDEP     name for machine-dependent library files
  CC          C compiler command
  CFLAGS      C compiler flags
  LDFLAGS     linker flags, e.g. -L<lib dir> if you have libraries in a
              nonstandard directory <lib dir>
  LIBS        libraries to pass to the linker, e.g. -l<library>
  CPPFLAGS    (Objective) C/C++ preprocessor flags, e.g. -I<include dir> if
              you have headers in a nonstandard directory <include dir>
  CPP         C preprocessor
  HOSTRUNNER  Program to run CPython for the host platform
  PROFILE_TASK
              Python args for PGO generation task
  BOLT_INSTRUMENT_FLAGS
              Arguments to llvm-bolt when instrumenting binaries
  BOLT_APPLY_FLAGS
              Arguments to llvm-bolt when creating a BOLT optimized binary
  LIBUUID_CFLAGS
              C compiler flags for LIBUUID, overriding pkg-config
  LIBUUID_LIBS
              linker flags for LIBUUID, overriding pkg-config
  LIBFFI_CFLAGS
              C compiler flags for LIBFFI, overriding pkg-config
  LIBFFI_LIBS linker flags for LIBFFI, overriding pkg-config
  LIBNSL_CFLAGS
              C compiler flags for LIBNSL, overriding pkg-config
  LIBNSL_LIBS linker flags for LIBNSL, overriding pkg-config
  LIBSQLITE3_CFLAGS
              C compiler flags for LIBSQLITE3, overriding pkg-config
  LIBSQLITE3_LIBS
              linker flags for LIBSQLITE3, overriding pkg-config
  TCLTK_CFLAGS
              C compiler flags for TCLTK, overriding pkg-config
  TCLTK_LIBS  linker flags for TCLTK, overriding pkg-config
  X11_CFLAGS  C compiler flags for X11, overriding pkg-config
  X11_LIBS    linker flags for X11, overriding pkg-config
  GDBM_CFLAGS C compiler flags for gdbm
  GDBM_LIBS   additional linker flags for gdbm
  ZLIB_CFLAGS C compiler flags for ZLIB, overriding pkg-config
  ZLIB_LIBS   linker flags for ZLIB, overriding pkg-config
  BZIP2_CFLAGS
              C compiler flags for BZIP2, overriding pkg-config
  BZIP2_LIBS  linker flags for BZIP2, overriding pkg-config
  LIBLZMA_CFLAGS
              C compiler flags for LIBLZMA, overriding pkg-config
  LIBLZMA_LIBS
              linker flags for LIBLZMA, overriding pkg-config
  LIBCRYPT_CFLAGS
              C compiler flags for LIBCRYPT, overriding pkg-config
  LIBCRYPT_LIBS
              linker flags for LIBCRYPT, overriding pkg-config
  LIBREADLINE_CFLAGS
              C compiler flags for LIBREADLINE, overriding pkg-config
  LIBREADLINE_LIBS
              linker flags for LIBREADLINE, overriding pkg-config
  LIBEDIT_CFLAGS
              C compiler flags for LIBEDIT, overriding pkg-config
  LIBEDIT_LIBS
              linker flags for LIBEDIT, overriding pkg-config
  CURSES_CFLAGS
              C compiler flags for CURSES, overriding pkg-config
  CURSES_LIBS linker flags for CURSES, overriding pkg-config
  PANEL_CFLAGS
              C compiler flags for PANEL, overriding pkg-config
  PANEL_LIBS  linker flags for PANEL, overriding pkg-config
  LIBB2_CFLAGS
              C compiler flags for LIBB2, overriding pkg-config
  LIBB2_LIBS  linker flags for LIBB2, overriding pkg-config

Use these variables to override the choices made by `configure' or to help
it to find libraries and programs with nonstandard names/locations.

Report bugs to <https://github.com/python/cpython/issues/>.


```


