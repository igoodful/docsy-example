---
title: doxygen for mysql-8.0.32
description: doxygen for mysql-8.0.32
date: 2025-05-29
weight: 100
viz: true
---


<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

- https://www.doxygen.nl/download.html
- https://github.com/doxygen/doxygen
- 二进制安装包：https://www.doxygen.nl/files/doxygen-1.14.0.linux.bin.tar.gz
- 源码安装包：https://www.doxygen.nl/files/doxygen-1.14.0.src.tar.gz
- [graphviz](https://graphviz.org/download/)
- https://www.doxygen.nl/manual/docblocks.html
- https://doxygen.cpp.org.cn/manual/doxygen_usage.html
{{< /alert >}}



## 编辑`mysql-8.0.32/Doxyfile.in`

```bash
cd mysql-8.0.32

vim Doxyfile.in

```


```toml
DOXYFILE_ENCODING      = UTF-8
PROJECT_NAME           = MySQL
PROJECT_NUMBER         = 8.0.32
PROJECT_BRIEF          = "Source Code Documentation"
PROJECT_LOGO           = doxygen_resources/logo-mysql-110x55.png
OUTPUT_DIRECTORY       = /root/mysql-8.0.32/build/doxygen
CREATE_SUBDIRS         = YES
ALLOW_UNICODE_NAMES    = NO
OUTPUT_LANGUAGE        = Chinese
BRIEF_MEMBER_DESC      = YES
REPEAT_BRIEF           = YES
ABBREVIATE_BRIEF       =
ALWAYS_DETAILED_SEC    = NO
INLINE_INHERITED_MEMB  = NO
FULL_PATH_NAMES        = YES
STRIP_FROM_PATH        =
STRIP_FROM_INC_PATH    =
SHORT_NAMES            = NO
JAVADOC_AUTOBRIEF      = YES
JAVADOC_BANNER         = NO
QT_AUTOBRIEF           = YES
MULTILINE_CPP_IS_BRIEF = NO
PYTHON_DOCSTRING       = YES
INHERIT_DOCS           = YES
SEPARATE_MEMBER_PAGES  = NO
TAB_SIZE               = 8
ALIASES                = SQL{1}=<small>\1</small> \
                         B{1}=<b>\1</b>
OPTIMIZE_OUTPUT_FOR_C  = NO
OPTIMIZE_OUTPUT_JAVA   = NO
OPTIMIZE_FOR_FORTRAN   = NO
OPTIMIZE_OUTPUT_VHDL   = NO
OPTIMIZE_OUTPUT_SLICE  = NO
EXTENSION_MAPPING      = proto=C++ \
                         i=C++ \
                         ic=C++
MARKDOWN_SUPPORT       = YES
TOC_INCLUDE_HEADINGS   = 0
AUTOLINK_SUPPORT       = NO
BUILTIN_STL_SUPPORT    = NO
CPP_CLI_SUPPORT        = NO
SIP_SUPPORT            = NO
IDL_PROPERTY_SUPPORT   = YES
DISTRIBUTE_GROUP_DOC   = NO
GROUP_NESTED_COMPOUNDS = NO
SUBGROUPING            = YES
INLINE_GROUPED_CLASSES = NO
INLINE_SIMPLE_STRUCTS  = NO
TYPEDEF_HIDES_STRUCT   = NO
LOOKUP_CACHE_SIZE      = 2
NUM_PROC_THREADS       = 1
EXTRACT_ALL            = YES
EXTRACT_PRIVATE        = YES
EXTRACT_PRIV_VIRTUAL   = NO
EXTRACT_PACKAGE        = NO
EXTRACT_STATIC         = YES
EXTRACT_LOCAL_CLASSES  = YES
EXTRACT_LOCAL_METHODS  = NO
EXTRACT_ANON_NSPACES   = YES
RESOLVE_UNNAMED_PARAMS = YES
HIDE_UNDOC_MEMBERS     = NO
HIDE_UNDOC_CLASSES     = NO
HIDE_FRIEND_COMPOUNDS  = NO
HIDE_IN_BODY_DOCS      = NO
INTERNAL_DOCS          = NO
CASE_SENSE_NAMES       = YES
HIDE_SCOPE_NAMES       = NO
HIDE_COMPOUND_REFERENCE= NO
SHOW_HEADERFILE        = YES
SHOW_INCLUDE_FILES     = YES
SHOW_GROUPED_MEMB_INC  = NO
FORCE_LOCAL_INCLUDES   = NO
INLINE_INFO            = YES
SORT_MEMBER_DOCS       = YES
SORT_BRIEF_DOCS        = NO
SORT_MEMBERS_CTORS_1ST = YES
SORT_GROUP_NAMES       = YES
SORT_BY_SCOPE_NAME     = NO
STRICT_PROTO_MATCHING  = NO
GENERATE_TODOLIST      = NO
GENERATE_TESTLIST      = NO
GENERATE_BUGLIST       = NO
GENERATE_DEPRECATEDLIST= YES
ENABLED_SECTIONS       =
MAX_INITIALIZER_LINES  = 30
SHOW_USED_FILES        = YES
SHOW_FILES             = YES
SHOW_NAMESPACES        = YES
FILE_VERSION_FILTER    =
LAYOUT_FILE            =
CITE_BIB_FILES         =
QUIET                  = NO
WARNINGS               = YES
WARN_IF_UNDOCUMENTED   = NO
WARN_IF_DOC_ERROR      = YES
WARN_IF_INCOMPLETE_DOC = YES
WARN_NO_PARAMDOC       = NO
WARN_AS_ERROR          = NO
WARN_FORMAT            = "$file:$line: $text"
WARN_LOGFILE           =
INPUT                  = ./client \
                         ./components \
                         ./include \
                         ./libbinlogevents \
                         ./libbinlogstandalone \
                         ./libservices \
                         ./mysql-test/mysql-test-run.dox \
                         ./mysys \
                         ./plugin/audit_null \
                         ./plugin/auth \
                         ./plugin/daemon_example \
                         ./plugin/ddl_rewriter \
                         ./plugin/fulltext \
                         ./plugin/group_replication \
                         ./plugin/password_validation \
                         ./plugin/replication_observers_example \
                         ./plugin/rewrite_example \
                         ./plugin/rewriter \
                         ./plugin/semisync \
                         ./plugin/test_services \
                         ./plugin/test_service_sql_api \
                         ./plugin/version_token \
                         ./plugin/pfs_table_plugin \
                         ./router/src \
                         ./sql \
                         ./plugin/x/protocol/doc \
                         ./plugin/x/protocol/protobuf \
                         ./sql-common \
                         ./storage \
                         ./strings \
                         ./libmysql \
                         ./vio
INPUT_ENCODING         = UTF-8
FILE_PATTERNS          = *.c \
                         *.cc \
                         *.cpp \
                         *.i \
                         *.ic \
                         *.h \
                         *.hpp \
                         *.d \
                         *.dox \
                         *.inc \
                         *.md \
                         *.py \
                         *.as \
                         *.js \
                         *.proto
RECURSIVE              = YES
EXCLUDE                = cmd-line-utils \
                         client/mysqlbinlog.cc \
                         components/test \
                         extra \
                         generated \
                         include/mysqld_error.h \
                         include/mysqld_ername.h \
                         include/sql_state.h \
                         internal \
                         libevent \
                         plugin/innodb_memcached \
                         plugin/test_services \
                         plugin/test_service_sql_api \
                         source_downloads \
                         sql/sql_hints.cc \
                         sql/sql_yacc.cc \
                         sql/debug_lo_scanner.h \
                         sql/debug_lo_scanner.cc \
                         sql/examples \
                         storage/innobase/include/fts0blex.h \
                         storage/innobase/include/fts0tlex.h \
                         storage/innobase/fts/fts0blex.cc \
                         storage/innobase/fts/fts0tlex.cc \
                         storage/ndb/clusterj \
                         storage/ndb/include \
                         storage/ndb/mcc \
                         storage/ndb/memcache \
                         storage/ndb/ndbapi-examples \
                         storage/ndb/nodejs \
                         storage/ndb/plugin \
                         storage/ndb/src \
                         storage/ndb/test \
                         storage/ndb/tools \
                         support-files \
                         unittest \
                         zlib
EXCLUDE_SYMLINKS       = NO
EXCLUDE_PATTERNS       = */include/boost*/* \
                         */include/mysql/psi/psi_abi*.h \
                         */router/src/*/tests/* \
                         */unittest/* \
                         */build-*/*
EXCLUDE_SYMBOLS        =
EXAMPLE_PATH           = sql/examples \
                         router
EXAMPLE_PATTERNS       =
EXAMPLE_RECURSIVE      = NO
IMAGE_PATH             =
INPUT_FILTER           =
FILTER_PATTERNS        = "*/sql/mysqld.cc=perl ./doxygen_resources/doxygen-filter-mysqld" \
                         "*/plugin/x/protocol/*.proto=perl ./doxygen_resources/doxygen-filter-proto"
FILTER_SOURCE_FILES    = NO
FILTER_SOURCE_PATTERNS =
USE_MDFILE_AS_MAINPAGE =
SOURCE_BROWSER         = NO
INLINE_SOURCES         = NO
STRIP_CODE_COMMENTS    = NO
REFERENCED_BY_RELATION = YES
REFERENCES_RELATION    = YES
REFERENCES_LINK_SOURCE = YES
SOURCE_TOOLTIPS        = YES
USE_HTAGS              = NO
VERBATIM_HEADERS       = YES
ALPHABETICAL_INDEX     = YES
IGNORE_PREFIX          =
GENERATE_HTML          = YES
HTML_OUTPUT            = html
HTML_FILE_EXTENSION    = .html
HTML_HEADER            =
HTML_FOOTER            =
HTML_STYLESHEET        =
HTML_EXTRA_STYLESHEET  = doxygen_resources/mysql.css
HTML_EXTRA_FILES       =
HTML_COLORSTYLE_HUE    = 220
HTML_COLORSTYLE_SAT    = 100
HTML_COLORSTYLE_GAMMA  = 80
HTML_TIMESTAMP         = NO
HTML_DYNAMIC_MENUS     = YES
HTML_DYNAMIC_SECTIONS  = NO
HTML_INDEX_NUM_ENTRIES = 100
GENERATE_DOCSET        = NO
DOCSET_FEEDNAME        = "Doxygen generated docs"
DOCSET_BUNDLE_ID       = org.doxygen.Project
DOCSET_PUBLISHER_ID    = org.doxygen.Publisher
DOCSET_PUBLISHER_NAME  = Publisher
GENERATE_HTMLHELP      = NO
CHM_FILE               =
HHC_LOCATION           =
GENERATE_CHI           = NO
CHM_INDEX_ENCODING     =
BINARY_TOC             = NO
TOC_EXPAND             = NO
GENERATE_QHP           = NO
QCH_FILE               =
QHP_NAMESPACE          = org.doxygen.Project
QHP_VIRTUAL_FOLDER     = doc
QHP_CUST_FILTER_NAME   =
QHP_CUST_FILTER_ATTRS  =
QHP_SECT_FILTER_ATTRS  =
QHG_LOCATION           =
GENERATE_ECLIPSEHELP   = NO
ECLIPSE_DOC_ID         = org.doxygen.Project
DISABLE_INDEX          = NO
GENERATE_TREEVIEW      = YES
FULL_SIDEBAR           = NO
ENUM_VALUES_PER_LINE   = 4
TREEVIEW_WIDTH         = 250
EXT_LINKS_IN_WINDOW    = NO
HTML_FORMULA_FORMAT    = png
FORMULA_FONTSIZE       = 10
FORMULA_TRANSPARENT    = YES
FORMULA_MACROFILE      =
USE_MATHJAX            = NO
MATHJAX_VERSION        = MathJax_2
MATHJAX_FORMAT         = HTML-CSS
MATHJAX_RELPATH        = http://cdn.mathjax.org/mathjax/latest
MATHJAX_EXTENSIONS     =
MATHJAX_CODEFILE       =
SEARCHENGINE           = YES
SERVER_BASED_SEARCH    = NO
EXTERNAL_SEARCH        = NO
SEARCHENGINE_URL       =
SEARCHDATA_FILE        = searchdata.xml
EXTERNAL_SEARCH_ID     =
EXTRA_SEARCH_MAPPINGS  =
GENERATE_LATEX         = NO
LATEX_OUTPUT           = latex
LATEX_CMD_NAME         = latex
MAKEINDEX_CMD_NAME     = makeindex
LATEX_MAKEINDEX_CMD    = makeindex
COMPACT_LATEX          = NO
PAPER_TYPE             = a4
EXTRA_PACKAGES         =
LATEX_HEADER           =
LATEX_FOOTER           =
LATEX_EXTRA_STYLESHEET =
LATEX_EXTRA_FILES      =
PDF_HYPERLINKS         = YES
USE_PDFLATEX           = YES
LATEX_BATCHMODE        = NO
LATEX_HIDE_INDICES     = NO
LATEX_BIB_STYLE        = plain
LATEX_TIMESTAMP        = NO
LATEX_EMOJI_DIRECTORY  =
GENERATE_RTF           = NO
RTF_OUTPUT             = rtf
COMPACT_RTF            = NO
RTF_HYPERLINKS         = NO
RTF_STYLESHEET_FILE    =
RTF_EXTENSIONS_FILE    =
GENERATE_MAN           = NO
MAN_OUTPUT             = man
MAN_EXTENSION          = .3
MAN_SUBDIR             =
MAN_LINKS              = NO
GENERATE_XML           = NO
XML_OUTPUT             = xml
XML_PROGRAMLISTING     = YES
XML_NS_MEMB_FILE_SCOPE = NO
GENERATE_DOCBOOK       = NO
DOCBOOK_OUTPUT         = docbook
GENERATE_AUTOGEN_DEF   = NO
GENERATE_PERLMOD       = NO
PERLMOD_LATEX          = NO
PERLMOD_PRETTY         = YES
PERLMOD_MAKEVAR_PREFIX =
ENABLE_PREPROCESSING   = YES
MACRO_EXPANSION        = YES
EXPAND_ONLY_PREDEF     = YES
SEARCH_INCLUDES        = YES
INCLUDE_PATH           = . \
                         client/include \
                         include \
                         include/mysql \
                         sql
INCLUDE_FILE_PATTERNS  =
PREDEFINED             = WITH_PERFSCHEMA_STORAGE_ENGINE \
                         IN_DOXYGEN \
                         __cplusplus \
                         HAVE_REPLICATION \
                         HAVE_COMPRESS \
                         HAVE_MYSYS \
                         OPTIMIZER_TRACE \
                         MYSQL_SERVER \
                         HAVE_PSI_INTERFACE \
                         HAVE_PSI_1 \
                         HAVE_PSI_2 \
                         USE_PSI_1 \
                         HAVE_PSI_MUTEX_INTERFACE \
                         HAVE_PSI_RWLOCK_INTERFACE \
                         HAVE_PSI_COND_INTERFACE \
                         HAVE_PSI_FILE_INTERFACE \
                         HAVE_PSI_THREAD_INTERFACE \
                         HAVE_PSI_IDLE_INTERFACE \
                         HAVE_PSI_STAGE_INTERFACE \
                         HAVE_PSI_STATEMENT_INTERFACE \
                         HAVE_PSI_STATEMENT_DIGEST_INTERFACE \
                         __attribute__(X)= \
                         MY_ATTRIBUTE(X)= \
                         ALWAYS_INLINE(X)=X \
                         UNIV_DEBUG \
                         UNIV_NOTHROW \
                         __cplusplus \
                         DOXYGEN_IF_DEBUG \
                         IF_DEBUG(...)=__VA_ARGS__ \
                         IF_AHI_DEBUG(...)=__VA_ARGS__
EXPAND_AS_DEFINED      = C_MODE_START \
                         C_MODE_END \
                         ATTRIB_USED_ONLY_IN_DEBUG \
                         DECLARE_COMPONENT \
                         END_DECLARE_COMPONENT \
                         BEGIN_SERVICE_DEFINITION \
                         END_SERVICE_DEFINITION \
                         SERVICE_TYPE \
                         SERVICE_TYPE_NO_CONST \
                         DEFINE_SERVICE_HANDLE \
                         BEGIN_SERVICE_IMPLEMENTATION \
                         END_SERVICE_IMPLEMENTATION \
                         DEFINE_METHOD \
                         DEFINE_BOOL_METHOD \
                         DECLARE_METHOD \
                         DECLARE_BOOL_METHOD
SKIP_FUNCTION_MACROS   = YES
TAGFILES               =
GENERATE_TAGFILE       =
ALLEXTERNALS           = NO
EXTERNAL_GROUPS        = YES
EXTERNAL_PAGES         = YES
DIA_PATH               =
HIDE_UNDOC_RELATIONS   = YES
HAVE_DOT               = YES
DOT_NUM_THREADS        = 10
DOT_FONTNAME           = Helvetica
DOT_FONTSIZE           = 10
DOT_FONTPATH           =
CLASS_GRAPH            = YES
COLLABORATION_GRAPH    = NO
GROUP_GRAPHS           = YES
UML_LOOK               = NO
UML_LIMIT_NUM_FIELDS   = 10
DOT_UML_DETAILS        = NO
DOT_WRAP_THRESHOLD     = 17
TEMPLATE_RELATIONS     = NO
INCLUDE_GRAPH          = NO
INCLUDED_BY_GRAPH      = NO
CALL_GRAPH             = YES
CALLER_GRAPH           = YES
GRAPHICAL_HIERARCHY    = YES
DIRECTORY_GRAPH        = YES
DOT_IMAGE_FORMAT       = svg
INTERACTIVE_SVG        = YES
DOT_PATH               =
DOTFILE_DIRS           =
MSCFILE_DIRS           =
DIAFILE_DIRS           = doxygen_resources
PLANTUML_JAR_PATH      = $(PLANTUML_JAR_PATH)
PLANTUML_CFG_FILE      =
PLANTUML_INCLUDE_PATH  =
DOT_GRAPH_MAX_NODES    = 150
MAX_DOT_GRAPH_DEPTH    = 0
DOT_TRANSPARENT        = NO
DOT_MULTI_TARGETS      = NO
GENERATE_LEGEND        = YES
DOT_CLEANUP            = YES
```


## cmake


```bash
cd mysql-8.0.32

mkdir build && cd build

cmake ../ -DCMAKE_BUILD_TYPE=Debug -DWITH_BUILD_ID=1 -DENABLED_LOCAL_INFILE=1 -DMYSQL_TCP_PORT=8032 -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_bin -DWITH_BOOST=../boost -DCMAKE_INSTALL_PREFIX=/data/mysql/app -DMYSQL_DATADIR=/data/mysql/data -DSYSCONFDIR=/data/mysql/conf -DTMPDIR=/data/mysql/tmp -DMYSQL_UNIX_ADDR=/data/mysql/tmp/mysql.sock -DMYSQLX_UNIX_ADDR=/data/mysql/tmp/mysqlx.sock -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_ICU=bundled -DWITH_LZ4=bundled -DWITH_LIBEVENT=bundled -DWITH_ZLIB=bundled -DWITH_SSL=system -DWITH_ZSTD=bundled
```

## make doxygen

```bash
nohup make doxygen &

```

## 案例


####


```bash
cd mysql-8.0.32

mkdir build-doxygen && cd build-doxygen

cmake ../ -DCMAKE_BUILD_TYPE=Debug -DWITH_BUILD_ID=1 -DENABLED_LOCAL_INFILE=1 -DMYSQL_TCP_PORT=8032 -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_bin -DWITH_BOOST=../boost -DCMAKE_INSTALL_PREFIX=/data/mysql/app -DMYSQL_DATADIR=/data/mysql/data -DSYSCONFDIR=/data/mysql/conf -DTMPDIR=/data/mysql/tmp -DMYSQL_UNIX_ADDR=/data/mysql/tmp/mysql.sock -DMYSQLX_UNIX_ADDR=/data/mysql/tmp/mysqlx.sock -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_ICU=bundled -DWITH_LZ4=bundled -DWITH_LIBEVENT=bundled -DWITH_ZLIB=bundled -DWITH_SSL=system -DWITH_ZSTD=bundled
```


- `root@ubuntu2410:~/mysql-8.0.32/build-doxygen# cat Doxyfile`

```ini
DOXYFILE_ENCODING      = UTF-8
PROJECT_NAME           = MySQL
PROJECT_NUMBER         = 8.0.32
PROJECT_BRIEF          =
PROJECT_LOGO           = doxygen_resources/logo-mysql-110x55.png
PROJECT_ICON           =
OUTPUT_DIRECTORY       = /root/mysql-8.0.32/build-doxygen/doxygen
CREATE_SUBDIRS         = YES
CREATE_SUBDIRS_LEVEL   = 8
ALLOW_UNICODE_NAMES    = NO
OUTPUT_LANGUAGE        = Chinese
BRIEF_MEMBER_DESC      = YES
REPEAT_BRIEF           = YES
ABBREVIATE_BRIEF       =
ALWAYS_DETAILED_SEC    = NO
INLINE_INHERITED_MEMB  = NO
FULL_PATH_NAMES        = YES
STRIP_FROM_PATH        =
STRIP_FROM_INC_PATH    =
SHORT_NAMES            = NO
JAVADOC_AUTOBRIEF      = YES
JAVADOC_BANNER         = NO
QT_AUTOBRIEF           = NO
MULTILINE_CPP_IS_BRIEF = NO
PYTHON_DOCSTRING       = YES
INHERIT_DOCS           = YES
SEPARATE_MEMBER_PAGES  = NO
TAB_SIZE               = 8
ALIASES                = SQL{1}=<small>\1</small> \
                         B{1}=<b>\1</b>
OPTIMIZE_OUTPUT_FOR_C  = NO
OPTIMIZE_OUTPUT_JAVA   = NO
OPTIMIZE_FOR_FORTRAN   = NO
OPTIMIZE_OUTPUT_VHDL   = NO
OPTIMIZE_OUTPUT_SLICE  = NO
EXTENSION_MAPPING      = proto=C++ \
                         i=C++ \
                         ic=C++
MARKDOWN_SUPPORT       = YES
TOC_INCLUDE_HEADINGS   = 6
MARKDOWN_ID_STYLE      = DOXYGEN
AUTOLINK_SUPPORT       = YES
AUTOLINK_IGNORE_WORDS  =
BUILTIN_STL_SUPPORT    = NO
CPP_CLI_SUPPORT        = NO
SIP_SUPPORT            = NO
IDL_PROPERTY_SUPPORT   = YES
DISTRIBUTE_GROUP_DOC   = NO
GROUP_NESTED_COMPOUNDS = NO
SUBGROUPING            = YES
INLINE_GROUPED_CLASSES = NO
INLINE_SIMPLE_STRUCTS  = NO
TYPEDEF_HIDES_STRUCT   = NO
LOOKUP_CACHE_SIZE      = 2
NUM_PROC_THREADS       = 1
TIMESTAMP              = NO
EXTRACT_ALL            = YES
EXTRACT_PRIVATE        = YES
EXTRACT_PRIV_VIRTUAL   = NO
EXTRACT_PACKAGE        = NO
EXTRACT_STATIC         = YES
EXTRACT_LOCAL_CLASSES  = YES
EXTRACT_LOCAL_METHODS  = NO
EXTRACT_ANON_NSPACES   = YES
RESOLVE_UNNAMED_PARAMS = YES
HIDE_UNDOC_MEMBERS     = NO
HIDE_UNDOC_CLASSES     = NO
HIDE_UNDOC_NAMESPACES  = YES
HIDE_FRIEND_COMPOUNDS  = NO
HIDE_IN_BODY_DOCS      = NO
INTERNAL_DOCS          = NO
CASE_SENSE_NAMES       = NO
HIDE_SCOPE_NAMES       = NO
HIDE_COMPOUND_REFERENCE= NO
SHOW_HEADERFILE        = YES
SHOW_INCLUDE_FILES     = YES
SHOW_GROUPED_MEMB_INC  = NO
FORCE_LOCAL_INCLUDES   = NO
INLINE_INFO            = YES
SORT_MEMBER_DOCS       = NO
SORT_BRIEF_DOCS        = NO
SORT_MEMBERS_CTORS_1ST = YES
SORT_GROUP_NAMES       = YES
SORT_BY_SCOPE_NAME     = NO
STRICT_PROTO_MATCHING  = NO
GENERATE_TODOLIST      = NO
GENERATE_TESTLIST      = NO
GENERATE_BUGLIST       = NO
GENERATE_DEPRECATEDLIST= YES
ENABLED_SECTIONS       =
MAX_INITIALIZER_LINES  = 30
SHOW_USED_FILES        = YES
SHOW_FILES             = YES
SHOW_NAMESPACES        = YES
FILE_VERSION_FILTER    =
LAYOUT_FILE            =
CITE_BIB_FILES         =
EXTERNAL_TOOL_PATH     =
QUIET                  = NO
WARNINGS               = YES
WARN_IF_UNDOCUMENTED   = NO
WARN_IF_DOC_ERROR      = YES
WARN_IF_INCOMPLETE_DOC = YES
WARN_NO_PARAMDOC       = NO
WARN_IF_UNDOC_ENUM_VAL = NO
WARN_LAYOUT_FILE       = YES
WARN_AS_ERROR          = NO
WARN_FORMAT            = "$file:$line: $text"
WARN_LINE_FORMAT       = "at line $line of file $file"
WARN_LOGFILE           =
INPUT                  = ./client \
                         ./components \
                         ./include \
                         ./libbinlogevents \
                         ./libbinlogstandalone \
                         ./libservices \
                         ./mysql-test/mysql-test-run.dox \
                         ./mysys \
                         ./plugin/audit_null \
                         ./plugin/auth \
                         ./plugin/daemon_example \
                         ./plugin/ddl_rewriter \
                         ./plugin/fulltext \
                         ./plugin/group_replication \
                         ./plugin/password_validation \
                         ./plugin/replication_observers_example \
                         ./plugin/rewrite_example \
                         ./plugin/rewriter \
                         ./plugin/semisync \
                         ./plugin/test_services \
                         ./plugin/test_service_sql_api \
                         ./plugin/version_token \
                         ./plugin/pfs_table_plugin \
                         ./router/src \
                         ./sql \
                         ./plugin/x/protocol/doc \
                         ./plugin/x/protocol/protobuf \
                         ./sql-common \
                         ./storage \
                         ./strings \
                         ./libmysql \
                         ./vio
INPUT_ENCODING         = UTF-8
INPUT_FILE_ENCODING    =
FILE_PATTERNS          = *.c \
                         *.cc \
                         *.cpp \
                         *.i \
                         *.ic \
                         *.h \
                         *.hpp \
                         *.d \
                         *.dox \
                         *.inc \
                         *.md \
                         *.py \
                         *.as \
                         *.js \
                         *.proto
EXCLUDE                = cmd-line-utils \
                         client/mysqlbinlog.cc \
                         components/test \
                         extra \
                         generated \
                         include/mysqld_error.h \
                         include/mysqld_ername.h \
                         include/sql_state.h \
                         internal \
                         libevent \
                         plugin/innodb_memcached \
                         plugin/test_services \
                         plugin/test_service_sql_api \
                         source_downloads \
                         sql/sql_hints.cc \
                         sql/sql_yacc.cc \
                         sql/debug_lo_scanner.h \
                         sql/debug_lo_scanner.cc \
                         sql/examples \
                         storage/innobase/include/fts0blex.h \
                         storage/innobase/include/fts0tlex.h \
                         storage/innobase/fts/fts0blex.cc \
                         storage/innobase/fts/fts0tlex.cc \
                         storage/ndb/clusterj \
                         storage/ndb/include \
                         storage/ndb/mcc \
                         storage/ndb/memcache \
                         storage/ndb/ndbapi-examples \
                         storage/ndb/nodejs \
                         storage/ndb/plugin \
                         storage/ndb/src \
                         storage/ndb/test \
                         storage/ndb/tools \
                         support-files \
                         unittest \
                         zlib
EXCLUDE                =
EXCLUDE_SYMLINKS       = NO
EXCLUDE_PATTERNS       = */include/boost*/* \
                         */include/mysql/psi/psi_abi*.h \
                         */router/src/*/tests/* \
                         */unittest/* \
                         */build-*/*
EXCLUDE_SYMBOLS        =
EXAMPLE_PATH           = sql/examples \
                         router
EXAMPLE_PATTERNS       =
EXAMPLE_RECURSIVE      = NO
IMAGE_PATH             =
INPUT_FILTER           =
FILTER_PATTERNS        = "*/sql/mysqld.cc=perl ./doxygen_resources/doxygen-filter-mysqld" \
                         "*/plugin/x/protocol/*.proto=perl ./doxygen_resources/doxygen-filter-proto"
FILTER_SOURCE_FILES    = NO
FILTER_SOURCE_PATTERNS =
USE_MDFILE_AS_MAINPAGE =
IMPLICIT_DIR_DOCS      = YES
FORTRAN_COMMENT_AFTER  = 72
SOURCE_BROWSER         = YES
INLINE_SOURCES         = YES
STRIP_CODE_COMMENTS    = YES
REFERENCED_BY_RELATION = YES
REFERENCES_RELATION    = YES
REFERENCES_LINK_SOURCE = YES
SOURCE_TOOLTIPS        = YES
USE_HTAGS              = NO
VERBATIM_HEADERS       = YES
CLANG_ASSISTED_PARSING = NO
CLANG_ADD_INC_PATHS    = YES
CLANG_OPTIONS          =
CLANG_DATABASE_PATH    =
ALPHABETICAL_INDEX     = YES
IGNORE_PREFIX          =
GENERATE_HTML          = YES
HTML_OUTPUT            = html
HTML_FILE_EXTENSION    = .html
HTML_HEADER            =
HTML_FOOTER            =
HTML_STYLESHEET        =
HTML_EXTRA_STYLESHEET  = doxygen_resources/mysql.css
HTML_EXTRA_FILES       =
HTML_COLORSTYLE        = AUTO_LIGHT
HTML_COLORSTYLE_HUE    = 220
HTML_COLORSTYLE_SAT    = 100
HTML_COLORSTYLE_GAMMA  = 80
HTML_DYNAMIC_MENUS     = YES
HTML_DYNAMIC_SECTIONS  = NO
HTML_CODE_FOLDING      = YES
HTML_COPY_CLIPBOARD    = YES
HTML_PROJECT_COOKIE    =
HTML_INDEX_NUM_ENTRIES = 512
GENERATE_DOCSET        = NO
DOCSET_FEEDNAME        = "Doxygen generated docs"
DOCSET_FEEDURL         =
DOCSET_BUNDLE_ID       = org.doxygen.Project
DOCSET_PUBLISHER_ID    = org.doxygen.Publisher
DOCSET_PUBLISHER_NAME  = Publisher
GENERATE_HTMLHELP      = NO
CHM_FILE               =
HHC_LOCATION           =
GENERATE_CHI           = NO
CHM_INDEX_ENCODING     =
BINARY_TOC             = NO
TOC_EXPAND             = NO
SITEMAP_URL            =
GENERATE_QHP           = NO
QCH_FILE               =
QHP_NAMESPACE          = org.doxygen.Project
QHP_VIRTUAL_FOLDER     = doc
QHP_CUST_FILTER_NAME   =
QHP_CUST_FILTER_ATTRS  =
QHP_SECT_FILTER_ATTRS  =
QHG_LOCATION           =
GENERATE_ECLIPSEHELP   = NO
ECLIPSE_DOC_ID         = org.doxygen.Project
DISABLE_INDEX          = NO
GENERATE_TREEVIEW      = YES
PAGE_OUTLINE_PANEL     = YES
FULL_SIDEBAR           = NO
ENUM_VALUES_PER_LINE   = 4
SHOW_ENUM_VALUES       = NO
TREEVIEW_WIDTH         = 400
EXT_LINKS_IN_WINDOW    = NO
OBFUSCATE_EMAILS       = YES
HTML_FORMULA_FORMAT    = png
FORMULA_FONTSIZE       = 10
FORMULA_MACROFILE      =
USE_MATHJAX            = NO
MATHJAX_VERSION        = MathJax_2
MATHJAX_FORMAT         = HTML-CSS
MATHJAX_RELPATH        =
MATHJAX_EXTENSIONS     =
MATHJAX_CODEFILE       =
SEARCHENGINE           = YES
SERVER_BASED_SEARCH    = NO
EXTERNAL_SEARCH        = NO
SEARCHENGINE_URL       =
SEARCHDATA_FILE        = searchdata.xml
EXTERNAL_SEARCH_ID     =
EXTRA_SEARCH_MAPPINGS  =
GENERATE_LATEX         = NO
LATEX_OUTPUT           = latex
LATEX_CMD_NAME         =
MAKEINDEX_CMD_NAME     = makeindex
LATEX_MAKEINDEX_CMD    = makeindex
COMPACT_LATEX          = NO
PAPER_TYPE             = a4
EXTRA_PACKAGES         =
LATEX_HEADER           =
LATEX_FOOTER           =
LATEX_EXTRA_STYLESHEET =
LATEX_EXTRA_FILES      =
PDF_HYPERLINKS         = NO
USE_PDFLATEX           = NO
LATEX_BATCHMODE        = NO
LATEX_HIDE_INDICES     = NO
LATEX_BIB_STYLE        = plainnat
LATEX_EMOJI_DIRECTORY  =
GENERATE_RTF           = NO
RTF_OUTPUT             = rtf
COMPACT_RTF            = NO
RTF_HYPERLINKS         = NO
RTF_STYLESHEET_FILE    =
RTF_EXTENSIONS_FILE    =
RTF_EXTRA_FILES        =
GENERATE_MAN           = NO
MAN_OUTPUT             = man
MAN_EXTENSION          = .3
MAN_SUBDIR             =
MAN_LINKS              = NO
GENERATE_XML           = NO
XML_OUTPUT             = xml
XML_PROGRAMLISTING     = YES
XML_NS_MEMB_FILE_SCOPE = NO
GENERATE_DOCBOOK       = NO
DOCBOOK_OUTPUT         = docbook
GENERATE_AUTOGEN_DEF   = NO
GENERATE_SQLITE3       = NO
SQLITE3_OUTPUT         = sqlite3
SQLITE3_RECREATE_DB    = NO
GENERATE_PERLMOD       = NO
PERLMOD_LATEX          = NO
PERLMOD_PRETTY         = YES
PERLMOD_MAKEVAR_PREFIX =
ENABLE_PREPROCESSING   = YES
MACRO_EXPANSION        = YES
EXPAND_ONLY_PREDEF     = YES
SEARCH_INCLUDES        = YES
INCLUDE_PATH           = . \
                         client/include \
                         include \
                         include/mysql \
                         sql \
			 build-doxygen/include
INCLUDE_FILE_PATTERNS  =
PREDEFINED             = WITH_PERFSCHEMA_STORAGE_ENGINE \
                         IN_DOXYGEN \
                         __cplusplus \
                         HAVE_REPLICATION \
                         HAVE_COMPRESS \
                         HAVE_MYSYS \
                         OPTIMIZER_TRACE \
                         MYSQL_SERVER \
                         HAVE_PSI_INTERFACE \
                         HAVE_PSI_1 \
                         HAVE_PSI_2 \
                         USE_PSI_1 \
                         HAVE_PSI_MUTEX_INTERFACE \
                         HAVE_PSI_RWLOCK_INTERFACE \
                         HAVE_PSI_COND_INTERFACE \
                         HAVE_PSI_FILE_INTERFACE \
                         HAVE_PSI_THREAD_INTERFACE \
                         HAVE_PSI_IDLE_INTERFACE \
                         HAVE_PSI_STAGE_INTERFACE \
                         HAVE_PSI_STATEMENT_INTERFACE \
                         HAVE_PSI_STATEMENT_DIGEST_INTERFACE \
                         __attribute__(X)= \
                         MY_ATTRIBUTE(X)= \
                         ALWAYS_INLINE(X)=X \
                         UNIV_DEBUG \
                         UNIV_NOTHROW \
                         __cplusplus \
                         DOXYGEN_IF_DEBUG \
                         IF_DEBUG(...)=__VA_ARGS__ \
                         IF_AHI_DEBUG(...)=__VA_ARGS__
EXPAND_AS_DEFINED      = C_MODE_START \
                         C_MODE_END \
                         ATTRIB_USED_ONLY_IN_DEBUG \
                         DECLARE_COMPONENT \
                         END_DECLARE_COMPONENT \
                         BEGIN_SERVICE_DEFINITION \
                         END_SERVICE_DEFINITION \
                         SERVICE_TYPE \
                         SERVICE_TYPE_NO_CONST \
                         DEFINE_SERVICE_HANDLE \
                         BEGIN_SERVICE_IMPLEMENTATION \
                         END_SERVICE_IMPLEMENTATION \
                         DEFINE_METHOD \
                         DEFINE_BOOL_METHOD \
                         DECLARE_METHOD \
                         DECLARE_BOOL_METHOD
SKIP_FUNCTION_MACROS   = YES
TAGFILES               =
GENERATE_TAGFILE       =
ALLEXTERNALS           = NO
EXTERNAL_GROUPS        = YES
EXTERNAL_PAGES         = YES
HIDE_UNDOC_RELATIONS   = YES
HAVE_DOT               = YES
DOT_NUM_THREADS        = 4
DOT_COMMON_ATTR        = "fontname=Helvetica,fontsize=10"
DOT_EDGE_ATTR          = "labelfontname=Helvetica,labelfontsize=10"
DOT_NODE_ATTR          = "shape=box,height=0.2,width=0.4"
DOT_FONTPATH           =
CLASS_GRAPH            = YES
COLLABORATION_GRAPH    = YES
GROUP_GRAPHS           = YES
UML_LOOK               = YES
UML_LIMIT_NUM_FIELDS   = 30
UML_MAX_EDGE_LABELS    = 10
DOT_UML_DETAILS        = NO
DOT_WRAP_THRESHOLD     = 17
TEMPLATE_RELATIONS     = NO
INCLUDE_GRAPH          = YES
INCLUDED_BY_GRAPH      = YES
CALL_GRAPH             = YES
CALLER_GRAPH           = YES
GRAPHICAL_HIERARCHY    = YES
DIRECTORY_GRAPH        = YES
DIR_GRAPH_MAX_DEPTH    = 4
DOT_IMAGE_FORMAT       = png
INTERACTIVE_SVG        = NO
DOT_PATH               =
DOTFILE_DIRS           =
DIA_PATH               =
DIAFILE_DIRS           =
PLANTUML_JAR_PATH      = "/usr/share/plantuml/plantuml.jar"
PLANTUML_CFG_FILE      =
PLANTUML_INCLUDE_PATH  =
PLANTUMLFILE_DIRS      =
DOT_GRAPH_MAX_NODES    = 256
MAX_DOT_GRAPH_DEPTH    = 16
DOT_MULTI_TARGETS      = NO
GENERATE_LEGEND        = YES
DOT_CLEANUP            = YES
MSCGEN_TOOL            =
MSCFILE_DIRS           =

```

- `DOT_GRAPH_MAX_NODES` 影响速度和文件大小
- `MAX_DOT_GRAPH_DEPTH` 影响速度和文件大小





### 20250611-Doxyfile.in

`root@ubuntu2410:~/mysql-8.0.32# cat Doxyfile.in`
```bash

DOXYFILE_ENCODING      = UTF-8
PROJECT_NAME           = MySQL
PROJECT_NUMBER         = @VERSION@
PROJECT_BRIEF          =
PROJECT_LOGO           = doxygen_resources/logo-mysql-110x55.png
PROJECT_ICON           =
OUTPUT_DIRECTORY       = @DOXYGEN_OUTPUT_DIR@
CREATE_SUBDIRS         = YES
CREATE_SUBDIRS_LEVEL   = 8
ALLOW_UNICODE_NAMES    = NO
OUTPUT_LANGUAGE        = Chinese
BRIEF_MEMBER_DESC      = YES
REPEAT_BRIEF           = YES
ABBREVIATE_BRIEF       =
ALWAYS_DETAILED_SEC    = NO
INLINE_INHERITED_MEMB  = NO
FULL_PATH_NAMES        = YES
STRIP_FROM_PATH        =
STRIP_FROM_INC_PATH    =
SHORT_NAMES            = NO
JAVADOC_AUTOBRIEF      = YES
JAVADOC_BANNER         = NO
QT_AUTOBRIEF           = NO
MULTILINE_CPP_IS_BRIEF = NO
PYTHON_DOCSTRING       = YES
INHERIT_DOCS           = YES
SEPARATE_MEMBER_PAGES  = NO
TAB_SIZE               = 8
ALIASES                = SQL{1}=<small>\1</small> \
                         B{1}=<b>\1</b>
OPTIMIZE_OUTPUT_FOR_C  = NO
OPTIMIZE_OUTPUT_JAVA   = NO
OPTIMIZE_FOR_FORTRAN   = NO
OPTIMIZE_OUTPUT_VHDL   = NO
OPTIMIZE_OUTPUT_SLICE  = NO
EXTENSION_MAPPING      = proto=C++ \
                         i=C++ \
                         ic=C++
MARKDOWN_SUPPORT       = YES
TOC_INCLUDE_HEADINGS   = 6
MARKDOWN_ID_STYLE      = DOXYGEN
AUTOLINK_SUPPORT       = YES
AUTOLINK_IGNORE_WORDS  =
BUILTIN_STL_SUPPORT    = NO
CPP_CLI_SUPPORT        = NO
SIP_SUPPORT            = NO
IDL_PROPERTY_SUPPORT   = YES
DISTRIBUTE_GROUP_DOC   = NO
GROUP_NESTED_COMPOUNDS = NO
SUBGROUPING            = YES
INLINE_GROUPED_CLASSES = NO
INLINE_SIMPLE_STRUCTS  = NO
TYPEDEF_HIDES_STRUCT   = NO
LOOKUP_CACHE_SIZE      = 2
NUM_PROC_THREADS       = 1
TIMESTAMP              = NO
EXTRACT_ALL            = YES
EXTRACT_PRIVATE        = YES
EXTRACT_PRIV_VIRTUAL   = NO
EXTRACT_PACKAGE        = NO
EXTRACT_STATIC         = YES
EXTRACT_LOCAL_CLASSES  = YES
EXTRACT_LOCAL_METHODS  = NO
EXTRACT_ANON_NSPACES   = YES
RESOLVE_UNNAMED_PARAMS = YES
HIDE_UNDOC_MEMBERS     = NO
HIDE_UNDOC_CLASSES     = NO
HIDE_UNDOC_NAMESPACES  = YES
HIDE_FRIEND_COMPOUNDS  = NO
HIDE_IN_BODY_DOCS      = NO
INTERNAL_DOCS          = NO
CASE_SENSE_NAMES       = NO
HIDE_SCOPE_NAMES       = NO
HIDE_COMPOUND_REFERENCE= NO
SHOW_HEADERFILE        = YES
SHOW_INCLUDE_FILES     = YES
SHOW_GROUPED_MEMB_INC  = NO
FORCE_LOCAL_INCLUDES   = NO
INLINE_INFO            = YES
SORT_MEMBER_DOCS       = NO
SORT_BRIEF_DOCS        = NO
SORT_MEMBERS_CTORS_1ST = YES
SORT_GROUP_NAMES       = YES
SORT_BY_SCOPE_NAME     = NO
STRICT_PROTO_MATCHING  = NO
GENERATE_TODOLIST      = NO
GENERATE_TESTLIST      = NO
GENERATE_BUGLIST       = NO
GENERATE_DEPRECATEDLIST= YES
ENABLED_SECTIONS       =
MAX_INITIALIZER_LINES  = 30
SHOW_USED_FILES        = YES
SHOW_FILES             = YES
SHOW_NAMESPACES        = YES
FILE_VERSION_FILTER    =
LAYOUT_FILE            =
CITE_BIB_FILES         =
EXTERNAL_TOOL_PATH     =
QUIET                  = NO
WARNINGS               = YES
WARN_IF_UNDOCUMENTED   = NO
WARN_IF_DOC_ERROR      = YES
WARN_IF_INCOMPLETE_DOC = YES
WARN_NO_PARAMDOC       = NO
WARN_IF_UNDOC_ENUM_VAL = NO
WARN_LAYOUT_FILE       = YES
WARN_AS_ERROR          = NO
WARN_FORMAT            = "$file:$line: $text"
WARN_LINE_FORMAT       = "at line $line of file $file"
WARN_LOGFILE           =
INPUT                  = ./client \
                         ./components \
                         ./include \
                         ./libbinlogevents \
                         ./libbinlogstandalone \
                         ./libservices \
                         ./mysql-test/mysql-test-run.dox \
                         ./mysys \
                         ./plugin/audit_null \
                         ./plugin/auth \
                         ./plugin/daemon_example \
                         ./plugin/ddl_rewriter \
                         ./plugin/fulltext \
                         ./plugin/group_replication \
                         ./plugin/password_validation \
                         ./plugin/replication_observers_example \
                         ./plugin/rewrite_example \
                         ./plugin/rewriter \
                         ./plugin/semisync \
                         ./plugin/test_services \
                         ./plugin/test_service_sql_api \
                         ./plugin/version_token \
                         ./plugin/pfs_table_plugin \
                         ./router/src \
                         ./sql \
                         ./plugin/x/protocol/doc \
                         ./plugin/x/protocol/protobuf \
                         ./sql-common \
                         ./storage \
                         ./strings \
                         ./libmysql \
                         ./vio
INPUT_ENCODING         = UTF-8
INPUT_FILE_ENCODING    =
FILE_PATTERNS          = *.c \
                         *.cc \
                         *.cpp \
                         *.i \
                         *.ic \
                         *.h \
                         *.hpp \
                         *.d \
                         *.dox \
                         *.inc \
                         *.md \
                         *.py \
                         *.as \
                         *.js \
                         *.proto
EXCLUDE                = cmd-line-utils \
                         client/mysqlbinlog.cc \
                         components/test \
                         extra \
                         generated \
                         include/mysqld_error.h \
                         include/mysqld_ername.h \
                         include/sql_state.h \
                         internal \
                         libevent \
                         plugin/innodb_memcached \
                         plugin/test_services \
                         plugin/test_service_sql_api \
                         source_downloads \
                         sql/sql_hints.cc \
                         sql/sql_yacc.cc \
                         sql/debug_lo_scanner.h \
                         sql/debug_lo_scanner.cc \
                         sql/examples \
                         storage/innobase/include/fts0blex.h \
                         storage/innobase/include/fts0tlex.h \
                         storage/innobase/fts/fts0blex.cc \
                         storage/innobase/fts/fts0tlex.cc \
                         storage/ndb/clusterj \
                         storage/ndb/include \
                         storage/ndb/mcc \
                         storage/ndb/memcache \
                         storage/ndb/ndbapi-examples \
                         storage/ndb/nodejs \
                         storage/ndb/plugin \
                         storage/ndb/src \
                         storage/ndb/test \
                         storage/ndb/tools \
                         support-files \
                         unittest \
                         zlib
EXCLUDE                =
EXCLUDE_SYMLINKS       = NO
EXCLUDE_PATTERNS       = */include/boost*/* \
                         */include/mysql/psi/psi_abi*.h \
                         */router/src/*/tests/* \
                         */unittest/* \
                         */build-*/*
EXCLUDE_SYMBOLS        =
EXAMPLE_PATH           = sql/examples \
                         router
EXAMPLE_PATTERNS       =
EXAMPLE_RECURSIVE      = NO
IMAGE_PATH             =
INPUT_FILTER           =
FILTER_PATTERNS        = "*/sql/mysqld.cc=perl ./doxygen_resources/doxygen-filter-mysqld" \
                         "*/plugin/x/protocol/*.proto=perl ./doxygen_resources/doxygen-filter-proto"
FILTER_SOURCE_FILES    = NO
FILTER_SOURCE_PATTERNS =
USE_MDFILE_AS_MAINPAGE =
IMPLICIT_DIR_DOCS      = YES
FORTRAN_COMMENT_AFTER  = 72
SOURCE_BROWSER         = YES
INLINE_SOURCES         = YES
STRIP_CODE_COMMENTS    = YES
REFERENCED_BY_RELATION = YES
REFERENCES_RELATION    = YES
REFERENCES_LINK_SOURCE = YES
SOURCE_TOOLTIPS        = YES
USE_HTAGS              = NO
VERBATIM_HEADERS       = YES
CLANG_ASSISTED_PARSING = NO
CLANG_ADD_INC_PATHS    = YES
CLANG_OPTIONS          =
CLANG_DATABASE_PATH    =
ALPHABETICAL_INDEX     = YES
IGNORE_PREFIX          =
GENERATE_HTML          = YES
HTML_OUTPUT            = html
HTML_FILE_EXTENSION    = .html
HTML_HEADER            =
HTML_FOOTER            =
HTML_STYLESHEET        =
HTML_EXTRA_STYLESHEET  = doxygen_resources/mysql.css
HTML_EXTRA_FILES       =
HTML_COLORSTYLE        = AUTO_LIGHT
HTML_COLORSTYLE_HUE    = 220
HTML_COLORSTYLE_SAT    = 100
HTML_COLORSTYLE_GAMMA  = 80
HTML_DYNAMIC_MENUS     = YES
HTML_DYNAMIC_SECTIONS  = NO
HTML_CODE_FOLDING      = YES
HTML_COPY_CLIPBOARD    = YES
HTML_PROJECT_COOKIE    =
HTML_INDEX_NUM_ENTRIES = 512
GENERATE_DOCSET        = NO
DOCSET_FEEDNAME        = "Doxygen generated docs"
DOCSET_FEEDURL         =
DOCSET_BUNDLE_ID       = org.doxygen.Project
DOCSET_PUBLISHER_ID    = org.doxygen.Publisher
DOCSET_PUBLISHER_NAME  = Publisher
GENERATE_HTMLHELP      = NO
CHM_FILE               =
HHC_LOCATION           =
GENERATE_CHI           = NO
CHM_INDEX_ENCODING     =
BINARY_TOC             = NO
TOC_EXPAND             = NO
SITEMAP_URL            =
GENERATE_QHP           = NO
QCH_FILE               =
QHP_NAMESPACE          = org.doxygen.Project
QHP_VIRTUAL_FOLDER     = doc
QHP_CUST_FILTER_NAME   =
QHP_CUST_FILTER_ATTRS  =
QHP_SECT_FILTER_ATTRS  =
QHG_LOCATION           =
GENERATE_ECLIPSEHELP   = NO
ECLIPSE_DOC_ID         = org.doxygen.Project
DISABLE_INDEX          = NO
GENERATE_TREEVIEW      = YES
PAGE_OUTLINE_PANEL     = YES
FULL_SIDEBAR           = NO
ENUM_VALUES_PER_LINE   = 4
SHOW_ENUM_VALUES       = NO
TREEVIEW_WIDTH         = 400
EXT_LINKS_IN_WINDOW    = NO
OBFUSCATE_EMAILS       = YES
HTML_FORMULA_FORMAT    = png
FORMULA_FONTSIZE       = 10
FORMULA_MACROFILE      =
USE_MATHJAX            = NO
MATHJAX_VERSION        = MathJax_2
MATHJAX_FORMAT         = HTML-CSS
MATHJAX_RELPATH        =
MATHJAX_EXTENSIONS     =
MATHJAX_CODEFILE       =
SEARCHENGINE           = YES
SERVER_BASED_SEARCH    = NO
EXTERNAL_SEARCH        = NO
SEARCHENGINE_URL       =
SEARCHDATA_FILE        = searchdata.xml
EXTERNAL_SEARCH_ID     =
EXTRA_SEARCH_MAPPINGS  =
GENERATE_LATEX         = NO
LATEX_OUTPUT           = latex
LATEX_CMD_NAME         =
MAKEINDEX_CMD_NAME     = makeindex
LATEX_MAKEINDEX_CMD    = makeindex
COMPACT_LATEX          = NO
PAPER_TYPE             = a4
EXTRA_PACKAGES         =
LATEX_HEADER           =
LATEX_FOOTER           =
LATEX_EXTRA_STYLESHEET =
LATEX_EXTRA_FILES      =
PDF_HYPERLINKS         = NO
USE_PDFLATEX           = NO
LATEX_BATCHMODE        = NO
LATEX_HIDE_INDICES     = NO
LATEX_BIB_STYLE        = plainnat
LATEX_EMOJI_DIRECTORY  =
GENERATE_RTF           = NO
RTF_OUTPUT             = rtf
COMPACT_RTF            = NO
RTF_HYPERLINKS         = NO
RTF_STYLESHEET_FILE    =
RTF_EXTENSIONS_FILE    =
RTF_EXTRA_FILES        =
GENERATE_MAN           = NO
MAN_OUTPUT             = man
MAN_EXTENSION          = .3
MAN_SUBDIR             =
MAN_LINKS              = NO
GENERATE_XML           = NO
XML_OUTPUT             = xml
XML_PROGRAMLISTING     = YES
XML_NS_MEMB_FILE_SCOPE = NO
GENERATE_DOCBOOK       = NO
DOCBOOK_OUTPUT         = docbook
GENERATE_AUTOGEN_DEF   = NO
GENERATE_SQLITE3       = NO
SQLITE3_OUTPUT         = sqlite3
SQLITE3_RECREATE_DB    = NO
GENERATE_PERLMOD       = NO
PERLMOD_LATEX          = NO
PERLMOD_PRETTY         = YES
PERLMOD_MAKEVAR_PREFIX =
ENABLE_PREPROCESSING   = YES
MACRO_EXPANSION        = YES
EXPAND_ONLY_PREDEF     = YES
SEARCH_INCLUDES        = YES
INCLUDE_PATH           = . \
                         client/include \
                         include \
                         include/mysql \
                         sql \
			 build-doxygen/include
INCLUDE_FILE_PATTERNS  =
PREDEFINED             = WITH_PERFSCHEMA_STORAGE_ENGINE \
                         IN_DOXYGEN \
                         __cplusplus \
                         HAVE_REPLICATION \
                         HAVE_COMPRESS \
                         HAVE_MYSYS \
                         OPTIMIZER_TRACE \
                         MYSQL_SERVER \
                         HAVE_PSI_INTERFACE \
                         HAVE_PSI_1 \
                         HAVE_PSI_2 \
                         USE_PSI_1 \
                         HAVE_PSI_MUTEX_INTERFACE \
                         HAVE_PSI_RWLOCK_INTERFACE \
                         HAVE_PSI_COND_INTERFACE \
                         HAVE_PSI_FILE_INTERFACE \
                         HAVE_PSI_THREAD_INTERFACE \
                         HAVE_PSI_IDLE_INTERFACE \
                         HAVE_PSI_STAGE_INTERFACE \
                         HAVE_PSI_STATEMENT_INTERFACE \
                         HAVE_PSI_STATEMENT_DIGEST_INTERFACE \
                         __attribute__(X)= \
                         MY_ATTRIBUTE(X)= \
                         ALWAYS_INLINE(X)=X \
                         UNIV_DEBUG \
                         UNIV_NOTHROW \
                         __cplusplus \
                         DOXYGEN_IF_DEBUG \
                         IF_DEBUG(...)=__VA_ARGS__ \
                         IF_AHI_DEBUG(...)=__VA_ARGS__
EXPAND_AS_DEFINED      = C_MODE_START \
                         C_MODE_END \
                         ATTRIB_USED_ONLY_IN_DEBUG \
                         DECLARE_COMPONENT \
                         END_DECLARE_COMPONENT \
                         BEGIN_SERVICE_DEFINITION \
                         END_SERVICE_DEFINITION \
                         SERVICE_TYPE \
                         SERVICE_TYPE_NO_CONST \
                         DEFINE_SERVICE_HANDLE \
                         BEGIN_SERVICE_IMPLEMENTATION \
                         END_SERVICE_IMPLEMENTATION \
                         DEFINE_METHOD \
                         DEFINE_BOOL_METHOD \
                         DECLARE_METHOD \
                         DECLARE_BOOL_METHOD
SKIP_FUNCTION_MACROS   = YES
TAGFILES               =
GENERATE_TAGFILE       =
ALLEXTERNALS           = NO
EXTERNAL_GROUPS        = YES
EXTERNAL_PAGES         = YES
HIDE_UNDOC_RELATIONS   = YES
HAVE_DOT               = YES
DOT_NUM_THREADS        = 4
DOT_COMMON_ATTR        = "fontname=Helvetica,fontsize=10"
DOT_EDGE_ATTR          = "labelfontname=Helvetica,labelfontsize=10"
DOT_NODE_ATTR          = "shape=box,height=0.2,width=0.4"
DOT_FONTPATH           =
CLASS_GRAPH            = YES
COLLABORATION_GRAPH    = YES
GROUP_GRAPHS           = YES
UML_LOOK               = NO
UML_LIMIT_NUM_FIELDS   = 30
UML_MAX_EDGE_LABELS    = 10
DOT_UML_DETAILS        = NO
DOT_WRAP_THRESHOLD     = 17
TEMPLATE_RELATIONS     = NO
INCLUDE_GRAPH          = YES
INCLUDED_BY_GRAPH      = YES
CALL_GRAPH             = YES
CALLER_GRAPH           = YES
GRAPHICAL_HIERARCHY    = YES
DIRECTORY_GRAPH        = YES
DIR_GRAPH_MAX_DEPTH    = 4
DOT_IMAGE_FORMAT       = png
INTERACTIVE_SVG        = NO
DOT_PATH               =
DOTFILE_DIRS           =
DIA_PATH               =
DIAFILE_DIRS           =
PLANTUML_JAR_PATH      =
PLANTUML_CFG_FILE      =
PLANTUML_INCLUDE_PATH  =
PLANTUMLFILE_DIRS      =
DOT_GRAPH_MAX_NODES    = 128
MAX_DOT_GRAPH_DEPTH    = 8
DOT_MULTI_TARGETS      = NO
GENERATE_LEGEND        = YES
DOT_CLEANUP            = YES
MSCGEN_TOOL            =
MSCFILE_DIRS           =

```










