---
title: 07. load_defaults
description: 完成配置文件和命令行参数的装载
date: 2023-10-30
weight: 50


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32 完成配置文件和命令行参数的装载
{{< /alert >}}

{{<alert>}}


- style 线条样式，对于节点和边：[style](https://graphviz.org/docs/attr-types/style/)
  - dotted
  - dashed
  - bold
  - solid
  - invis
- rankdir = LR/TB;图形方向：由左到右还是由上到下
  - LR（优美）
  - TB（优美）
  - RL
  - BT
- splines = ortho; 走直线
  - true 或者 spline, 样条线（无规则，可为直或者曲线，默认）
  - ortho, 横平竖直（优美）
  - none 或者 "", 无边
  - false 或者 line, 直线段（节点较多时会贯穿节点，尽量不要用）
  - polyline, 折线
  - curved, 曲弧线
- dir 设置绘制箭头的边缘类型
  - both 双向箭头
  - none 无箭头
  - forward 正向箭头
  - back 反向箭头
- subgraph 必须配合 cluster 一起使用，用法为 subgraph cluster* {}

{{</alert>}}



## 整体情况
- MYSQL_CONFIG_NAME=my
- load_default_groups=["mysql_cluster","mysqld","server","mysql-8.0",nullptr,nullptr]

```viz-dot
digraph "my_load_defaults" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "load_defaults" -> "my_load_defaults" [label="MYSQL_CONFIG_NAME, load_default_groups, &argc, &argv, &argv_alloc"];
        "my_load_defaults" -> "init_default_directories";
        "my_load_defaults" -> "my_search_option_files";
        "my_load_defaults" -> "my_default_get_login_file";
        "my_load_defaults" -> "set_args_separator";
        "my_load_defaults" -> "my_message_local" ;
}
```

## 配置文件路径

```viz-dot
digraph "配置文件路径" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "配置文件路径" -> "/etc/";
        "配置文件路径" -> "/etc/mysql/";
        "配置文件路径" -> "DEFAULT_SYSCONFDIR";
        "配置文件路径" -> "MYSQL_HOME";
        "配置文件路径" -> "defaults-extra-file";
        "配置文件路径" -> "~/" ;
}
```
注意事项：

- `DEFAULT_SYSCONFDIR`为cmake3编译时配置的默认路径，通过指定`-DSYSCONFDIR=/root/local/mysql_8032/conf`的路径，因此，在我这里`DEFAULT_SYSCONFDIR`为`/root/local/mysql_8032/conf`







## load_defaults
```c++
int load_defaults(const char *conf_file, const char **groups, int *argc, char ***argv, MEM_ROOT *alloc) {
        return my_load_defaults(conf_file, groups, argc, argv, alloc, &default_directories);
}
```


## my_load_defaults


```viz-dot
digraph "load_defaults" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "load_defaults" -> "my_load_defaults";
        "my_load_defaults" -> "my_search_option_files" [label="搜索文件"];
        "my_search_option_files" -> "search_default_file_with_ext" [label="读取配置文件内容"];

        "group" -> "count" [label="size_t"];
        "group" -> "name" [label="const char *"];
        "group" -> "type_names" [label="const char **"];
        "group" -> "type_lengths" [label="unsigned int *"];
}
```


```c++
const char *load_default_groups[] = {
#ifdef WITH_NDBCLUSTER_STORAGE_ENGINE
    "mysql_cluster",
#endif
    "mysqld",
    "server",
    MYSQL_BASE_VERSION,
    nullptr,
    nullptr};

typedef Prealloced_array<char *, 100> My_args;

struct TYPELIB {
        size_t count{0};
        const char *name{nullptr};
        const char **type_names{nullptr};
        unsigned int *type_lengths{nullptr};
};

struct handle_option_ctx {
        MEM_ROOT *alloc;
        My_args *m_args;
        TYPELIB *group;
};

int my_load_defaults(const char *conf_file,
                     const char **groups,
                     int *argc,
                     char ***argv,
                     MEM_ROOT *alloc,
                     const char ***default_directories) {
        // 存储默认选项的容器
        My_args my_args(key_memory_defaults);
        // 定义了要处理的选项组
        TYPELIB group;
        bool found_print_defaults = false;
        // 记录已使用的命令行参数数量
        uint args_used            = 0;
        int error                 = 0;
        const char **ptr;
        const char **res;
        // 保存处理选项的上下文信息
        struct handle_option_ctx ctx;
        // 存储默认选项目录的数组
        const char **dirs;
        // 用于存储登录文件路径的字符数组
        char my_login_file[FN_REFLEN];
        bool found_no_defaults = false;
        // 用于记录是否使用了参数分隔符
        uint args_sep          = my_getopt_use_args_separator ? 1 : 0;
        DBUG_TRACE;

        if ((dirs = init_default_directories(alloc)) == nullptr)
                goto err;
        /*
          Check if the user doesn't want any default option processing
          --no-defaults is always the first option
        */
        if (*argc >= 2 && !strcmp(argv[0][1], "--no-defaults"))
                no_defaults = found_no_defaults = true;

        group.count      = 0;
        group.name       = "defaults";
        group.type_names = groups;

        for (; *groups; groups++)
                group.count++;

        ctx.alloc  = alloc;
        ctx.m_args = &my_args;
        ctx.group  = &group;

        if ((error = my_search_option_files(conf_file,
                                            argc,
                                            argv,
                                            &args_used,
                                            handle_default_option,
                                            (void *)&ctx,
                                            dirs,
                                            false,
                                            found_no_defaults))) {
                return error;
        }

        if (my_defaults_read_login_file) {
                /* Read options from login group. */
                if (my_default_get_login_file(my_login_file, sizeof(my_login_file)) &&
                    (error = my_search_option_files(my_login_file,
                                                    argc,
                                                    argv,
                                                    &args_used,
                                                    handle_default_option,
                                                    (void *)&ctx,
                                                    dirs,
                                                    true,
                                                    found_no_defaults))) {
                        alloc->Clear();
                        return error;
                }
        }
        /*
          Here error contains <> 0 only if we have a fully specified conf_file
          or a forced default file
        */
        if (!(ptr = (const char **)alloc->Alloc((my_args.size() + *argc + 1 + args_sep) * sizeof(char *))))
                goto err;
        res = ptr;

        /* copy name + found arguments + command line arguments to new array */
        res[0] = argv[0][0]; /* Name MUST be set */
        if (!my_args.empty())
                memcpy((res + 1), &my_args[0], my_args.size() * sizeof(char *));
        /* Skip --defaults-xxx options */
        (*argc) -= args_used;
        (*argv) += args_used;

        /*
          Check if we want to see the new argument list
          This options must always be the last of the default options
        */
        if (*argc >= 2 && !strcmp(argv[0][1], "--print-defaults")) {
                found_print_defaults = true;
                --*argc;
                ++*argv; /* skip argument */
        }

        if (my_getopt_use_args_separator) {
                /* set arguments separator for arguments from config file and
                   command line */
                set_args_separator(&res[my_args.size() + 1]);
        }

        if (*argc)
                memcpy((uchar *)(res + 1 + my_args.size() + args_sep), (char *)((*argv) + 1), (*argc - 1) * sizeof(char *));
        res[my_args.size() + *argc + args_sep] = nullptr; /* last null */

        (*argc) += my_args.size() + args_sep;
        *argv = const_cast<char **>(res);

        if (default_directories)
                *default_directories = dirs;

        if (found_no_defaults)
                return 0;

        if (found_print_defaults) {
                int i;
                printf("%s would have been started with the following arguments:\n", **argv);
                for (i = 1; i < *argc; i++)
                        if (!my_getopt_is_args_separator((*argv)[i])) /* skip arguments separator */
                        {
                                if (strncmp((*argv)[i], "--password", 10) == 0)
                                        printf("%s ", "--password=*****");
                                else
                                        printf("%s ", (*argv)[i]);
                        }
                puts("");
                exit(0);
        }

        return 0;

err:
        my_message_local(ERROR_LEVEL, EE_FAILED_TO_HANDLE_DEFAULTS_FILE);
        exit(1);
        return 0; /* Keep compiler happy */
}

```


## search_default_file_with_ext
> 读取配置文件内容
```c++
static int search_default_file_with_ext(Process_option_func opt_handler,
                                        void *handler_ctx,
                                        const char *dir,
                                        const char *ext,
                                        const char *config_file,
                                        int recursion_level,
                                        bool is_login_file,
                                        bool report_os_error_on_open) {
        char name[FN_REFLEN + 10], buff[4096], curr_gr[4096], *ptr, *end;
        const char **tmp_ext;
        char *value, tmp[FN_REFLEN];
        static const char includedir_keyword[] = "includedir";
        static const char include_keyword[]    = "include";
        const int max_recursion_level          = 10;
        MYSQL_FILE *fp;
        uint line        = 0;
        bool found_group = false;
        uint i, rc;
        MY_DIR *search_dir;
        FILEINFO *search_file;
        myf flags = MYF(report_os_error_on_open ? MY_WME : 0);

        if ((dir ? strlen(dir) : 0) + strlen(config_file) >= FN_REFLEN - 3)
                return 0; /* Ignore wrong paths */
        if (dir) {
                end = convert_dirname(name, dir, NullS);
                if (dir[0] == FN_HOMELIB) /* Add . to filenames in home */
                        *end++ = '.';
                strxmov(end, config_file, ext, NullS);
        } else {
                my_stpcpy(name, config_file);
        }
        fn_format(name, name, "", "", MY_UNPACK_FILENAME);

        if ((rc = check_file_permissions(name, is_login_file, flags)) < 2)
                return (int)rc;

        if (is_login_file) {
                if (!(fp = mysql_file_fopen(key_file_cnf, name, O_RDONLY | MY_FOPEN_BINARY, flags)))
                        return 1; /* Ignore wrong files. */
        } else {
                if (!(fp = mysql_file_fopen(key_file_cnf, name, O_RDONLY, flags)))
                        return 1; /* Ignore wrong files */
        }

        while (true) {
                auto fileline  = mysql_file_getline(buff, sizeof(buff), fp, is_login_file);
                char *linebuff = fileline.get();
                if (linebuff == nullptr)
                        break;

                line++;
                /* Ignore comment and empty lines */
                for (ptr = linebuff; my_isspace(&my_charset_latin1, *ptr); ptr++) {
                }

                if (*ptr == '#' || *ptr == ';' || !*ptr)
                        continue;

                /* Configuration File Directives */
                if (*ptr == '!') {
                        if (recursion_level >= max_recursion_level) {
                                for (end = ptr + strlen(ptr) - 1; my_isspace(&my_charset_latin1, *(end - 1)); end--) {
                                }
                                end[0] = 0;
                                my_message_local(WARNING_LEVEL,
                                                 EE_SKIPPING_DIRECTIVE_DUE_TO_MAX_INCLUDE_RECURSION,
                                                 ptr,
                                                 name,
                                                 line);
                                continue;
                        }

                        /* skip over `!' and following whitespace */
                        for (++ptr; my_isspace(&my_charset_latin1, ptr[0]); ptr++) {
                        }

                        if ((!strncmp(ptr, includedir_keyword, sizeof(includedir_keyword) - 1)) &&
                            my_isspace(&my_charset_latin1, ptr[sizeof(includedir_keyword) - 1])) {
                                if (!(ptr = get_argument(includedir_keyword, sizeof(includedir_keyword), ptr, name, line)))
                                        goto err;

                                if (!(search_dir = my_dir(ptr, MYF(MY_WME)))) {
                                        my_message_local(ERROR_LEVEL,
                                                         EE_FAILED_PROCESSING_DIRECTIVE,
                                                         includedir_keyword,
                                                         name,
                                                         line);
                                        goto err;
                                }

                                for (i = 0; i < search_dir->number_off_files; i++) {
                                        search_file = search_dir->dir_entry + i;
                                        ext         = fn_ext(search_file->name);

                                        /* check extension */
                                        for (tmp_ext = f_extensions; *tmp_ext; tmp_ext++) {
                                                if (!strcmp(ext, *tmp_ext))
                                                        break;
                                        }

                                        if (*tmp_ext) {
                                                fn_format(tmp, search_file->name, ptr, "", MY_UNPACK_FILENAME | MY_SAFE_PATH);

                                                /* add the include file to the paths list with the class of the
                                                 * including file */
                                                std::map<string, enum_variable_source>::iterator it = default_paths.find(name);
                                                /*
                                                  The current file should always be a part of the paths.
                                                  But that applies only for the server.
                                                  For direct load_defaults() use all bets are off.
                                                  Hence keeping it as a dynamic condition.
                                                */
                                                if (it != default_paths.end())
                                                        default_paths[tmp] = it->second;

                                                if (search_default_file_with_ext(opt_handler,
                                                                                 handler_ctx,
                                                                                 nullptr,
                                                                                 nullptr,
                                                                                 tmp,
                                                                                 recursion_level + 1,
                                                                                 is_login_file,
                                                                                 true))
                                                        my_message_local(ERROR_LEVEL,
                                                                         EE_FAILED_PROCESSING_DIRECTIVE,
                                                                         includedir_keyword,
                                                                         name,
                                                                         line);
                                        }
                                }

                                my_dirend(search_dir);
                        } else if ((!strncmp(ptr, include_keyword, sizeof(include_keyword) - 1)) &&
                                   my_isspace(&my_charset_latin1, ptr[sizeof(include_keyword) - 1])) {
                                if (!(ptr = get_argument(include_keyword, sizeof(include_keyword), ptr, name, line)))
                                        goto err;

                                /* add the include file to the paths list with the class of the
                                 * including file */
                                std::map<string, enum_variable_source>::iterator it = default_paths.find(name);
                                /*
                                  The current file should always be a part of the paths.
                                  But that applies only for the server.
                                  For direct load_defaults() use all bets are off.
                                  Hence keeping it as a dynamic condition.
                                */
                                if (it != default_paths.end() && fn_format(tmp, ptr, "", "", MY_UNPACK_FILENAME | MY_SAFE_PATH))
                                        default_paths[tmp] = it->second;

                                if (search_default_file_with_ext(opt_handler,
                                                                 handler_ctx,
                                                                 nullptr,
                                                                 nullptr,
                                                                 ptr,
                                                                 recursion_level + 1,
                                                                 is_login_file,
                                                                 true))
                                        my_message_local(ERROR_LEVEL,
                                                         EE_FAILED_PROCESSING_DIRECTIVE,
                                                         include_keyword,
                                                         name,
                                                         line);
                        }

                        continue;
                }

                if (*ptr == '[') /* Group name */
                {
                        found_group = true;
                        if (!(end = strchr(++ptr, ']'))) {
                                my_message_local(ERROR_LEVEL, EE_INCORRECT_GRP_DEFINITION_IN_CONFIG_FILE, name, line);
                                goto err;
                        }
                        /* Remove end space */
                        for (; my_isspace(&my_charset_latin1, end[-1]); end--) {
                        }

                        end[0] = 0;

                        strmake(curr_gr, ptr, std::min<size_t>((end - ptr) + 1, sizeof(curr_gr) - 1));

                        /* signal that a new group is found */
                        opt_handler(handler_ctx, curr_gr, nullptr, nullptr);

                        continue;
                }
                if (!found_group) {
                        my_message_local(ERROR_LEVEL, EE_OPTION_WITHOUT_GRP_IN_CONFIG_FILE, name, line);
                        goto err;
                }

                /* comments are not supported in login file */
                if (!is_login_file)
                        end = remove_end_comment(ptr);
                else
                        end = ptr + strlen(ptr);

                if ((value = strchr(ptr, '=')))
                        end = value; /* Option without argument */
                for (; my_isspace(&my_charset_latin1, end[-1]); end--) {
                }

                /* Self freeing option buffer */
                std::unique_ptr<char[]> optionBuffer{new char[strlen(linebuff) + 3]};
                char *option = optionBuffer.get();

                if (!value) {
                        strmake(my_stpcpy(option, "--"), ptr, (size_t)(end - ptr));
                        if (opt_handler(handler_ctx, curr_gr, option, name))
                                goto err;
                } else {
                        /* Remove pre- and end space */
                        char *value_end;
                        for (value++; my_isspace(&my_charset_latin1, *value); value++) {
                        }

                        value_end = strend(value);
                        /*
                          We don't have to test for value_end >= value as we know there is
                          an '=' before
                        */
                        for (; my_isspace(&my_charset_latin1, value_end[-1]); value_end--) {
                        }

                        if (value_end < value) /* Empty string */
                                value_end = value;

                        /* remove quotes around argument */
                        if ((*value == '\"' || *value == '\'') && /* First char is quote */
                            (value + 1 < value_end) &&            /* String is longer than 1 */
                            *value == value_end[-1])              /* First char is equal to last char */
                        {
                                value++;
                                value_end--;
                        }
                        ptr    = my_stpnmov(my_stpcpy(option, "--"), ptr, (size_t)(end - ptr));
                        *ptr++ = '=';

                        for (; value != value_end; value++) {
                                if (*value == '\\' && value != value_end - 1) {
                                        switch (*++value) {
                                                case 'n':
                                                        *ptr++ = '\n';
                                                        break;
                                                case 't':
                                                        *ptr++ = '\t';
                                                        break;
                                                case 'r':
                                                        *ptr++ = '\r';
                                                        break;
                                                case 'b':
                                                        *ptr++ = '\b';
                                                        break;
                                                case 's':
                                                        *ptr++ = ' '; /* space */
                                                        break;
                                                case '\"':
                                                        *ptr++ = '\"';
                                                        break;
                                                case '\'':
                                                        *ptr++ = '\'';
                                                        break;
                                                case '\\':
                                                        *ptr++ = '\\';
                                                        break;
                                                default: /* Unknown; Keep '\' */
                                                        *ptr++ = '\\';
                                                        *ptr++ = *value;
                                                        break;
                                        }
                                } else
                                        *ptr++ = *value;
                        }
                        *ptr = 0;
                        if (opt_handler(handler_ctx, curr_gr, option, name))
                                goto err;
                }
        }
        mysql_file_fclose(fp, MYF(0));
        return (0);

err:
        mysql_file_fclose(fp, MYF(0));
        return -1; /* Fatal error */
}
```


## handle_default_option
```c++
static int handle_default_option(void *in_ctx, const char *group_name, const char *option, const char *cnf_file) {
        char *tmp;
        struct handle_option_ctx *ctx = (struct handle_option_ctx *)in_ctx;

        if (!option)
                return 0;

        if (find_type(group_name, ctx->group, FIND_TYPE_NO_PREFIX)) {
                if (!(tmp = (char *)ctx->alloc->Alloc(strlen(option) + 1)))
                        return 1;
                if (ctx->m_args->push_back(tmp))
                        return 1;
                my_stpcpy(tmp, option);
                update_variable_source(option, cnf_file);
        }
        return 0;
}
```










#### Prealloced_array
> Prealloced_array模板类功能：管理预分配的数组
>
> 总的来说，该类实现了一个预分配数组，具有类似于标准库中 std::vector 的功能，但又更加灵活，可以根据需求在编译时指定预分配的大小

- 提供了自动内存管理功能，包括内存的分配和释放，以及在预分配空间不足时的动态扩容。
- 类模板的第一个模板参数 Element_type 指定了数组中存储元素的类型。第二个模板参数 Prealloc 指定了数组的预分配大小。

```c++
        struct External {
                Element_type *m_array_ptr;
                size_t m_alloced_size;
                size_t m_alloced_capacity;
        };

        union {
                External m_ext{};
                Element_type m_buff[Prealloc];
        };
```

```viz-dot
digraph "Prealloced_array" {
        rankdir=TB;

        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "Prealloced_array"[shape="doubleoctagon"];
        "Prealloced_array" -> "initial_capacity" [label="static const size_t"];
        "Prealloced_array" -> "m_psi_key" [label="PSI_memory_key"];
        "Prealloced_array" -> "m_ext" [label="External",style = dashed];
        "Prealloced_array" -> "m_buff" [label="Element_type[]",style = dashed];

        "m_ext" -> "m_array_ptr" [label="Element_type*"];
        "m_ext" -> "m_alloced_size";
        "m_ext" -> "m_alloced_capacity";
}
```



```c++

template <typename Element_type, size_t Prealloc>
class Prealloced_array {

        static constexpr bool Has_trivial_destructor = std::is_trivially_destructible<Element_type>::value;

        bool using_inline_buffer() const {
                return m_inline_size >= 0;
        }

        Element_type *buffer() {
                return using_inline_buffer() ? m_buff : m_ext.m_array_ptr;
        }
        const Element_type *buffer() const {
                return using_inline_buffer() ? m_buff : m_ext.m_array_ptr;
        }

        void set_size(size_t n) {
                if (using_inline_buffer()) {
                        m_inline_size = n;
                } else {
                        m_ext.m_alloced_size = n;
                }
        }
        void adjust_size(int delta) {
                if (using_inline_buffer()) {
                        m_inline_size += delta;
                } else {
                        m_ext.m_alloced_size += delta;
                }
        }

public:
        /// Initial capacity of the array.
        static const size_t initial_capacity = Prealloc;

        /// Standard typedefs.
        typedef Element_type value_type;
        typedef size_t size_type;
        typedef ptrdiff_t difference_type;

        typedef Element_type *iterator;
        typedef const Element_type *const_iterator;

        explicit Prealloced_array(PSI_memory_key psi_key) : m_psi_key(psi_key) {
                static_assert(Prealloc != 0, "We do not want a zero-size array.");
        }

        Prealloced_array(PSI_memory_key psi_key, size_t initial_size) : m_psi_key(psi_key) {
                static_assert(Prealloc != 0, "We do not want a zero-size array.");

                if (initial_size > Prealloc) {
                        // We avoid using reserve() since it requires Element_type to be copyable.
                        void *mem = my_malloc(m_psi_key, initial_size * element_size(), MYF(MY_WME));
                        if (!mem)
                                return;
                        m_inline_size            = -1;
                        m_ext.m_alloced_size     = initial_size;
                        m_ext.m_array_ptr        = static_cast<Element_type *>(mem);
                        m_ext.m_alloced_capacity = initial_size;
                } else {
                        m_inline_size = initial_size;
                }
                for (size_t ix = 0; ix < initial_size; ++ix) {
                        Element_type *p = &buffer()[ix];
                        ::new (p) Element_type();
                }
        }

        Prealloced_array(const Prealloced_array &that) : m_psi_key(that.m_psi_key) {
                if (this->reserve(that.capacity()))
                        return;
                for (const Element_type *p = that.begin(); p != that.end(); ++p)
                        this->push_back(*p);
        }

        Prealloced_array(Prealloced_array &&that) : m_psi_key(that.m_psi_key) {
                *this = std::move(that);
        }

        Prealloced_array(PSI_memory_key psi_key, const_iterator first, const_iterator last) : m_psi_key(psi_key) {
                if (this->reserve(last - first))
                        return;
                for (; first != last; ++first)
                        push_back(*first);
        }

        Prealloced_array(std::initializer_list<Element_type> elems)
            : Prealloced_array(PSI_NOT_INSTRUMENTED, elems.begin(), elems.end()) {
        }


        Prealloced_array &operator=(const Prealloced_array &that) {
                this->clear();
                if (this->reserve(that.capacity()))
                        return *this;
                for (const Element_type *p = that.begin(); p != that.end(); ++p)
                        this->push_back(*p);
                return *this;
        }

        Prealloced_array &operator=(Prealloced_array &&that) {
                this->clear();
                if (!that.using_inline_buffer()) {
                        if (!using_inline_buffer())
                                my_free(m_ext.m_array_ptr);
                        // The array is on the heap, so we can just grab it.
                        m_ext.m_array_ptr        = that.m_ext.m_array_ptr;
                        m_inline_size            = -1;
                        m_ext.m_alloced_size     = that.m_ext.m_alloced_size;
                        m_ext.m_alloced_capacity = that.m_ext.m_alloced_capacity;
                        that.m_inline_size       = 0;
                } else {
                        // Move over each element.
                        if (this->reserve(that.capacity()))
                                return *this;
                        for (Element_type *p = that.begin(); p != that.end(); ++p)
                                this->push_back(std::move(*p));
                        that.clear();
                }
                return *this;
        }

        ~Prealloced_array() {
                if (!Has_trivial_destructor) {
                        clear();
                }
                if (!using_inline_buffer())
                        my_free(m_ext.m_array_ptr);
        }

        size_t capacity() const {
                return using_inline_buffer() ? Prealloc : m_ext.m_alloced_capacity;
        }
        size_t element_size() const {
                return sizeof(Element_type);
        }
        bool empty() const {
                return size() == 0;
        }
        size_t size() const {
                return using_inline_buffer() ? m_inline_size : m_ext.m_alloced_size;
        }

        Element_type &at(size_t n) {
                assert(n < size());
                return buffer()[n];
        }

        const Element_type &at(size_t n) const {
                assert(n < size());
                return buffer()[n];
        }

        Element_type &operator[](size_t n) {
                return at(n);
        }
        const Element_type &operator[](size_t n) const {
                return at(n);
        }

        Element_type &back() {
                return at(size() - 1);
        }
        const Element_type &back() const {
                return at(size() - 1);
        }

        Element_type &front() {
                return at(0);
        }
        const Element_type &front() const {
                return at(0);
        }


        iterator begin() {
                return buffer();
        }
        iterator end() {
                return buffer() + size();
        }
        const_iterator begin() const {
                return buffer();
        }
        const_iterator end() const {
                return buffer() + size();
        }
        /// Returns a constant pointer to the first element in the array.
        const_iterator cbegin() const {
                return begin();
        }
        /// Returns a constant pointer to the past-the-end element in the array.
        const_iterator cend() const {
                return end();
        }

        bool assign_at(size_t n, const value_type &val) {
                if (n < size()) {
                        at(n) = val;
                        return false;
                }
                if (reserve(n + 1))
                        return true;
                resize(n);
                return push_back(val);
        }

        bool reserve(size_t n) {
                if (n <= capacity())
                        return false;

                void *mem = my_malloc(m_psi_key, n * element_size(), MYF(MY_WME));
                if (!mem)
                        return true;
                Element_type *new_array = static_cast<Element_type *>(mem);

                size_t old_size = size();
                for (size_t ix = 0; ix < old_size; ++ix) {
                        Element_type *new_p = &new_array[ix];
                        Element_type &old_p = buffer()[ix];
                        ::new (new_p) Element_type(std::move(old_p));
                        if (!Has_trivial_destructor)
                                old_p.~Element_type();
                }

                if (!using_inline_buffer())
                        my_free(m_ext.m_array_ptr);

                m_ext.m_alloced_size     = old_size;
                m_inline_size            = -1;
                m_ext.m_array_ptr        = new_array;
                m_ext.m_alloced_capacity = n;
                return false;
        }

        bool push_back(const Element_type &element) {
                return emplace_back(element);
        }

        bool push_back(Element_type &&element) {
                return emplace_back(std::move(element));
        }

        template <typename... Args>
        bool emplace_back(Args &&...args) {
                const size_t expansion_factor = 2;
                if (size() == capacity() && reserve(capacity() * expansion_factor))
                        return true;
                Element_type *p = &buffer()[size()];
                adjust_size(1);
                ::new (p) Element_type(std::forward<Args>(args)...);
                return false;
        }

        void pop_back() {
                assert(!empty());
                if (!Has_trivial_destructor)
                        back().~Element_type();
                adjust_size(-1);
        }

        iterator insert(const_iterator position, const value_type &val) {
                return emplace(position, val);
        }

        iterator insert(const_iterator position, value_type &&val) {
                return emplace(position, std::move(val));
        }

        template <typename... Args>
        iterator emplace(const_iterator position, Args &&...args) {
                const difference_type n = position - begin();
                emplace_back(std::forward<Args>(args)...);
                std::rotate(begin() + n, end() - 1, end());
                return begin() + n;
        }

        std::pair<iterator, bool> insert_unique(const value_type &val) {
                std::pair<iterator, iterator> p = std::equal_range(begin(), end(), val);
                if (p.first == p.second)
                        return std::make_pair(insert(p.first, val), true);
                return std::make_pair(p.first, false);
        }

        size_type erase_unique(const value_type &val) {
                std::pair<iterator, iterator> p = std::equal_range(begin(), end(), val);
                if (p.first == p.second)
                        return 0;  // Not found
                erase(p.first);
                return 1;
        }

        size_type count_unique(const value_type &val) const {
                return std::binary_search(begin(), end(), val);
        }

        iterator erase(const_iterator position) {
                assert(position != end());
                return erase(position - begin());
        }

        iterator erase(size_t ix) {
                assert(ix < size());
                iterator pos = begin() + ix;
                if (pos + 1 != end())
                        std::move(pos + 1, end(), pos);
                pop_back();
                return pos;
        }

        void erase_at_end(const_iterator first) {
                const_iterator last        = cend();
                const difference_type diff = last - first;
                if (!Has_trivial_destructor) {
                        for (; first != last; ++first)
                                first->~Element_type();
                }
                adjust_size(-diff);
        }

        iterator erase(const_iterator first, const_iterator last) {
                iterator start = begin() + (first - cbegin());
                iterator stop  = begin() + (last - cbegin());
                if (first != last)
                        erase_at_end(std::move(stop, end(), start));
                return start;
        }

        void swap(Prealloced_array &rhs) {
                if (!using_inline_buffer() && !rhs.using_inline_buffer()) {
                        std::swap(m_ext.m_alloced_size, rhs.m_ext.m_alloced_size);
                        std::swap(m_ext.m_alloced_capacity, rhs.m_ext.m_alloced_capacity);
                        std::swap(m_ext.m_array_ptr, rhs.m_ext.m_array_ptr);
                        std::swap(m_psi_key, rhs.m_psi_key);
                        return;
                }
                std::swap(*this, rhs);
        }

        void shrink_to_fit() {
                if (using_inline_buffer())
                        return;
                if (size() == capacity())
                        return;
                Prealloced_array tmp(m_psi_key, begin(), end());
                if (size() <= Prealloc) {
                        this->~Prealloced_array();
                        new (this) Prealloced_array(tmp.m_psi_key, tmp.begin(), tmp.end());
                } else {

                        swap(tmp);
                }
        }

        void resize(size_t n, const Element_type &val = Element_type()) {
                if (n == size())
                        return;
                if (n > size()) {
                        if (!reserve(n)) {
                                while (n != size())
                                        push_back(val);
                        }
                        return;
                }
                if (!Has_trivial_destructor) {
                        while (n != size())
                                pop_back();
                }
                set_size(n);
        }

        void clear() {
                if (!Has_trivial_destructor) {
                        for (Element_type *p = begin(); p != end(); ++p)
                                p->~Element_type();
                }
                set_size(0);
        }

private:
        PSI_memory_key m_psi_key;
        int m_inline_size = 0;
        struct External {
                Element_type *m_array_ptr;
                size_t m_alloced_size;
                size_t m_alloced_capacity;
        };

        union {
                External m_ext{};
                Element_type m_buff[Prealloc];
        };
};

typedef Prealloced_array<char *, 100> My_args;
```

这段代码定义了一个名为Prealloced_array的模板类，用于管理预分配的数组。让我们逐段解释它的功能和实现细节：

类模板声明：

template <typename Element_type, size_t Prealloc>
- Element_type：数组元素的类型。
- Prealloc：预分配的数组容量。

类模板成员变量：

- m_psi_key：PSI（Performance Schema Instrumentation）内存键，用于跟踪内存使用。
- m_inline_size：如果大于等于0，则表示使用内联缓冲区m_buff，并且存储了实际大小；如果小于0，则表示使用外部存储，存储在m_ext的m_alloced_size中。
- m_buff[Prealloc]：内联缓冲区，用于存储预分配的元素。
- m_ext：外部存储结构体，包含指向堆上分配的数组的指针m_array_ptr、分配的大小m_alloced_size和容量m_alloced_capacity。

构造函数：
- Prealloced_array(PSI_memory_key psi_key)：初始化内联缓冲区，使用默认构造函数初始化元素。
- Prealloced_array(PSI_memory_key psi_key, size_t initial_size)：初始化内联缓冲区或堆上分配的数组，使用默认构造函数初始化元素。
- Prealloced_array(const Prealloced_array &that)：拷贝构造函数，深度复制数组元素。
- Prealloced_array(Prealloced_array &&that)：移动构造函数，转移资源所有权。
- Prealloced_array(std::initializer_list<Element_type> elems)：通过初始化列表初始化数组。

析构函数：
- ~Prealloced_array()：销毁数组元素，释放堆上分配的数组内存。

容量和大小操作：
- capacity()：返回数组的容量。
- size()：返回数组的大小。
- empty()：判断数组是否为空。
- resize(size_t n, const Element_type &val = Element_type())：重新设置数组的大小，并根据需要插入或删除元素。
- shrink_to_fit()：请求数组将容量调整为适合其大小。

访问元素：
- operator[]、at()、front()、back()：访问数组的首尾元素。
- begin()、end()：返回指向数组首尾的迭代器。
- insert()、emplace()：在指定位置插入元素。
- push_back()、emplace_back()：在数组末尾插入元素。
- pop_back()：移除数组末尾的元素。
- erase()、erase_at_end()：移除指定位置或范围内的元素。

其他操作：
- swap()：交换两个数组的内容。
- clear()：清空数组，销毁所有元素。

这个类模板提供了预分配的数组功能，并且能够自动选择在堆上还是使用内联缓冲区来存储元素，以提高性能和灵活性。


#### MEM_ROOT
> 管理内存分配

- `typedef unsigned int PSI_memory_key`

```viz-dot
digraph "MEM_ROOT" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = rect;
                fontsize = "12"
                ];
        "MEM_ROOT"[shape="doubleoctagon"];
        "MEM_ROOT" -> "m_current_block" [label="Block*"];
        "MEM_ROOT" -> "s_dummy_target" [label="static char"];
        "MEM_ROOT" -> "m_current_free_start" [label="char *"];
        "MEM_ROOT" -> "m_current_free_end" [label="char *"];
        "MEM_ROOT" -> "m_block_size";
        "MEM_ROOT" -> "m_orig_block_size" ;
        "MEM_ROOT" -> "m_max_capacity";
        "MEM_ROOT" -> "m_allocated_size";
        "MEM_ROOT" -> "m_error_for_capacity_exceeded";
        "MEM_ROOT" -> "m_error_handler" [label="void (*)(void) "];
        "MEM_ROOT" -> "m_psi_key" [label="PSI_memory_key"];

        "m_current_block" -> "prev" [label="Block*"];
        "m_current_block" -> "end" [label="char*"];
}
```





```c++
#define MY_ATTRIBUTE(A) __attribute__(A)
#define MY_ALIGN(A, L) (((A) + (L)-1) & ~((L)-1))
// 确保所分配的内存大小是 double 类型的对齐
#define ALIGN_SIZE(A) MY_ALIGN((A), sizeof(double))


struct MEM_ROOT {
private:
        // 用于表示内存块
        struct Block {
                // 指向前一个内存块的指针
                Block *prev{nullptr};
                // 指向内存块末尾下一个字节的指针，用于判断内存块是否包含某个指针
                char *end{nullptr};
        };

public:
        // 在调用默认构造函数时，实际上会调用带有两个参数的构造函数 MEM_ROOT(0, 512)，其中参数 0 是用于 PSI_memory_key，参数 512 是用于 block_size
        MEM_ROOT() : MEM_ROOT(0, 512) {
        }

        MEM_ROOT(PSI_memory_key key, size_t block_size)
            : m_block_size(block_size), m_orig_block_size(block_size), m_psi_key(key) {
#if defined(MYSQL_SERVER)
                m_error_handler = sql_alloc_error_handler;
#endif
        }

        MEM_ROOT(const MEM_ROOT &) = delete;
        MEM_ROOT(MEM_ROOT &&other) noexcept
            : m_current_block(other.m_current_block),
              m_current_free_start(other.m_current_free_start),
              m_current_free_end(other.m_current_free_end),
              m_block_size(other.m_block_size),
              m_orig_block_size(other.m_orig_block_size),
              m_max_capacity(other.m_max_capacity),
              m_allocated_size(other.m_allocated_size),
              m_error_for_capacity_exceeded(other.m_error_for_capacity_exceeded),
              m_error_handler(other.m_error_handler),
              m_psi_key(other.m_psi_key) {
                other.m_current_block      = nullptr;
                other.m_allocated_size     = 0;
                other.m_block_size         = m_orig_block_size;
                other.m_current_free_start = &s_dummy_target;
                other.m_current_free_end   = &s_dummy_target;
        }

        MEM_ROOT &operator=(const MEM_ROOT &) = delete;
        MEM_ROOT &operator=(MEM_ROOT &&other) noexcept {
                Clear();
                ::new (this) MEM_ROOT(std::move(other));
                return *this;
        }

        ~MEM_ROOT() {
                Clear();
        }

        // #define MY_ATTRIBUTE(A) __attribute__(A)
        void *Alloc(size_t length) MY_ATTRIBUTE((malloc)) {
                // ((((length)) + (sizeof(double))-1) & ~((sizeof(double))-1))
                length = ALIGN_SIZE(length);
                // Skip the straight path if simulating OOM; it should always fail.
                DBUG_EXECUTE_IF("simulate_out_of_memory", return AllocSlow(length););

                if (static_cast<size_t>(m_current_free_end - m_current_free_start) >= length) {
                        void *ret = m_current_free_start;
                        m_current_free_start += length;
                        return ret;
                }

                return AllocSlow(length);
        }


        template <class T, class... Args>
        T *ArrayAlloc(size_t num, Args... args) {
                static_assert(alignof(T) <= 8, "MEM_ROOT only returns 8-aligned memory.");
                if (num * sizeof(T) < num) {
                        // Overflow.
                        return nullptr;
                }
                T *ret = static_cast<T *>(Alloc(num * sizeof(T)));
                if (ret == nullptr) {
                        // Out of memory.
                        return nullptr;
                }

                // Initialize all elements.
                for (size_t i = 0; i < num; ++i) {
                        new (&ret[i]) T(args...);
                }

                return ret;
        }


        void Claim(bool claim);

        void Clear();


        void ClearForReuse();


        bool inited() const {
                return m_block_size != 0;
        }

        void set_max_capacity(size_t max_capacity) {
                m_max_capacity = max_capacity;
        }

        size_t get_max_capacity() const {
                return m_max_capacity;
        }


        void set_error_for_capacity_exceeded(bool report_error) {
                m_error_for_capacity_exceeded = report_error;
        }


        bool get_error_for_capacity_exceeded() const {
                return m_error_for_capacity_exceeded;
        }


        void set_error_handler(void (*error_handler)(void)) {
                m_error_handler = error_handler;
        }

        size_t allocated_size() const {
                return m_allocated_size;
        }

        void set_block_size(size_t block_size) {
                m_block_size = m_orig_block_size = block_size;
        }


        std::pair<char *, char *> Peek() const {
                return {m_current_free_start, m_current_free_end};
        }

        bool ForceNewBlock(size_t minimum_length);

        void RawCommit(size_t length) {
                assert(static_cast<size_t>(m_current_free_end - m_current_free_start) >= length);
                m_current_free_start += length;
        }

        bool Contains(void *ptr) const {
                for (Block *block = m_current_block; block != nullptr; block = block->prev) {
                        if (ptr >= block && ptr < block->end) {
                                return true;
                        }
                }
                return false;
        }

private:
        // 用于确保 Alloc 函数永远不会返回 nullptr,在 Alloc 函数中，如果请求分配的长度为零，为了避免返回 nullptr，会返回这个变量的地址
        static char s_dummy_target;
        // 用于分配新的内存块
        Block *AllocBlock(size_t wanted_length, size_t minimum_length);

        // 用于分配不适合当前空闲块的内存,当 Alloc 函数无法满足分配请求时，会调用该函数
        // 它接受一个参数 length，表示要分配的内存长度
        // 函数的作用是在新的内存块中分配所需长度的内存，并返回分配的内存地址
        void *AllocSlow(size_t length);

        // 用于释放内存块链表中的所有内存块,它接受一个指向内存块链表起始处的指针 start
        // 函数的作用是从给定的起始内存块开始，释放链表中的所有内存块
        static void FreeBlocks(Block *start);
        // 当前正在分配内存的内存块
        Block *m_current_block = nullptr;
        // 当前内存块中可用内存的起始位置
        char *m_current_free_start = &s_dummy_target;
        // 当前内存块中可用内存结束位置
        char *m_current_free_end = &s_dummy_target;
        // 下一个内存块的预期大小
        size_t m_block_size;
        // 下一个内存块的原始大小
        size_t m_orig_block_size;
        // 内存块的最大容量
        size_t m_max_capacity = 0;
        // 已分配的内存大小
        size_t m_allocated_size = 0;
        // 是否在超出容量时报告错误
        bool m_error_for_capacity_exceeded = false;
        // 内存分配失败时的错误处理函数
        void (*m_error_handler)(void) = nullptr;
        // 性能模式下用于标识内存的键
        PSI_memory_key m_psi_key = 0;
};
```

#### init_default_directories
> 初始化配置文件路径，即初始化默认的目录列表的函数

- DEFAULT_DIRS_SIZE = 7


```c++
#define MAX_DEFAULT_DIRS 6
#define DEFAULT_DIRS_SIZE (MAX_DEFAULT_DIRS + 1)

#define MY_ALIGN(A, L) (((A) + (L)-1) & ~((L)-1))
#define ALIGN_SIZE(A) MY_ALIGN((A), sizeof(double))

static const char **init_default_directories(MEM_ROOT *alloc) {
        // 存储默认目录列表
        const char **dirs;
        // 存储环境变量值
        char *env;
        // 记录添加目录时发生的错误数
        int errors = 0;
        // 分配内存，用于存储默认目录列表的指针,分配了一个大小为 7 * sizeof(char *) 字节的内存空间
        // 并将其转换为const char **类型的指针，赋值给dirs
        dirs = (const char **)alloc->Alloc(DEFAULT_DIRS_SIZE * sizeof(char *));
        // 检查分配内存是否成功，如果失败则返回nullptr
        if (dirs == nullptr)
                return nullptr;
        // 用零初始化分配的内存，即将所有指针设置为 nullptr
        memset(dirs, 0, DEFAULT_DIRS_SIZE * sizeof(char *));

#ifdef _WIN32

        {
                char fname_buffer[FN_REFLEN];
                if (my_get_system_windows_directory(fname_buffer, sizeof(fname_buffer)))
                        errors += add_directory(alloc, fname_buffer, dirs);

                if (GetWindowsDirectory(fname_buffer, sizeof(fname_buffer)))
                        errors += add_directory(alloc, fname_buffer, dirs);

                errors += add_directory(alloc, "C:/", dirs);

                if (my_get_module_parent(fname_buffer, sizeof(fname_buffer)) != NULL)
                        errors += add_directory(alloc, fname_buffer, dirs);
        }

#else
        // 将 "/etc/" 目录添加到目录列表中
        errors += add_directory(alloc, "/etc/", dirs);
        // 将 "/etc/mysql/" 目录添加到目录列表中
        errors += add_directory(alloc, "/etc/mysql/", dirs);

        #if defined(DEFAULT_SYSCONFDIR)
        if (DEFAULT_SYSCONFDIR[0])
                // 将 DEFAULT_SYSCONFDIR 宏所表示的目录添加到目录列表中
                errors += add_directory(alloc, DEFAULT_SYSCONFDIR, dirs);
        #endif /* DEFAULT_SYSCONFDIR */

#endif

        if ((env = getenv("MYSQL_HOME")))
                // 将环境变量 "MYSQL_HOME" 所表示的目录添加到目录列表中
                errors += add_directory(alloc, env, dirs);

        /* Placeholder for --defaults-extra-file=<path> */
        errors += add_directory(alloc, "", dirs);

#if !defined(_WIN32)
        // 将 "~/"" 目录添加到目录列表中
        errors += add_directory(alloc, "~/", dirs);
#endif
        // 根据错误计数决定返回结果。如果有错误发生，则返回 nullptr，否则返回目录列表的指针
        return (errors > 0 ? nullptr : dirs);
}
```

#### add_directory
> 这个函数的主要目的是将规范化后的目录字符串添加到目录列表中

```c++
static int add_directory(MEM_ROOT *alloc, const char *dir, const char **dirs) {
        // 用于存储目录的规范化版本
        char buf[FN_REFLEN];
        // 存储目录字符串的长度
        size_t len;
        // 用于存储通过strmake_root函数创建的字符串的地址
        char *p;
        // 用于存储数组追加操作的结
        bool err [[maybe_unused]];
        // 将目录字符串dir规范化，并将规范化后的结果存储在buf中，同时将规范化后的字符串长度赋值给len
        len = normalize_dirname(buf, dir);
        // 将规范化后的目录字符串复制到alloc所管理的内存中，并返回指向该字符串的指针。如果复制失败，则返回1表示失败
        if (!(p = strmake_root(alloc, buf, len)))
                return 1;
        // 将指定的目录字符串添加到目录列表中
        err = array_append_string_unique(p, dirs, DEFAULT_DIRS_SIZE);
        assert(err == false);

        return 0;
}

size_t normalize_dirname(char *to, const char *from) {
        size_t length;
        char buff[FN_REFLEN];
        DBUG_TRACE;
        (void)intern_filename(buff, from);
        length = strlen(buff);
        if (length &&
#ifdef FN_DEVCHAR
            buff[length - 1] != FN_DEVCHAR &&
#endif
            buff[length - 1] != FN_LIBCHAR && buff[length - 1] != '/') {
                if (length >= sizeof(buff) - 1)
                        length = sizeof(buff) - 2;
                buff[length]     = FN_LIBCHAR;
                buff[length + 1] = '\0';
        }

        length = cleanup_dirname(to, buff);

        return length;
}

char *strmake_root(MEM_ROOT *root, const char *str, size_t len) {
        char *pos;
        if ((pos = static_cast<char *>(root->Alloc(len + 1)))) {
                if (len > 0)
                        memcpy(pos, str, len);
                pos[len] = 0;
        }
        return pos;
}

bool array_append_string_unique(const char *str, const char **array, size_t size) {
        const char **p;
        const char **end = array + size - 1;
        assert(*end == nullptr);

        for (p = array; *p; ++p) {
                if (strcmp(*p, str) == 0)
                        break;
        }
        if (p >= end)
                return true;

        assert(*p == nullptr || strcmp(*p, str) == 0);

        while (*(p + 1)) {
                *p = *(p + 1);
                ++p;
        }

        assert(p < end);
        *p = str;

        return false;
}
```

#### my_default_get_login_file




#### init_default_directories


#### my_search_option_files
> 读取配置文件内容

```c++
int my_search_option_files(const char *conf_file,
                           int *argc,
                           char ***argv,
                           uint *args_used,
                           Process_option_func func,
                           void *func_ctx,
                           const char **default_directories,
                           bool is_login_file,
                           bool found_no_defaults) {
        const char **dirs;
        char *forced_default_file, *forced_extra_defaults;
        DBUG_TRACE;

        /* Skip for login file. */
        if (!is_login_file) {
                /* Check if we want to force the use a specific default file */
                *args_used += get_defaults_options(*argc - *args_used,
                                                   *argv + *args_used,
                                                   &forced_default_file,
                                                   &forced_extra_defaults,
                                                   const_cast<char **>(&my_defaults_group_suffix),
                                                   const_cast<char **>(&my_login_path),
                                                   found_no_defaults);

                if (!my_defaults_group_suffix)
                        my_defaults_group_suffix = getenv("MYSQL_GROUP_SUFFIX");

                if (forced_extra_defaults && !defaults_already_read) {
                        int error = fn_expand(forced_extra_defaults, my_defaults_extra_file_buffer);
                        if (error)
                                return error;

                        my_defaults_extra_file = my_defaults_extra_file_buffer;
                }

                if (forced_default_file && !defaults_already_read) {
                        int error = fn_expand(forced_default_file, my_defaults_file_buffer);
                        if (error)
                                return error;
                        my_defaults_file = my_defaults_file_buffer;
                }

                defaults_already_read = true;
                init_variable_default_paths();

                /*
                  We can only handle 'defaults-group-suffix' if we are called from
                  load_defaults() as otherwise we can't know the type of 'func_ctx'
                */

                if (my_defaults_group_suffix && func == handle_default_option) {
                        /* Handle --defaults-group-suffix= */
                        uint i;
                        const char **extra_groups;
                        const size_t instance_len     = strlen(my_defaults_group_suffix);
                        struct handle_option_ctx *ctx = (struct handle_option_ctx *)func_ctx;
                        char *ptr;
                        TYPELIB *group = ctx->group;

                        if (!(extra_groups = (const char **)ctx->alloc->Alloc((2 * group->count + 1) * sizeof(char *))))
                                return 2;

                        for (i = 0; i < group->count; i++) {
                                size_t len;
                                extra_groups[i] = group->type_names[i]; /** copy group */

                                len = strlen(extra_groups[i]);
                                if (!(ptr = (char *)ctx->alloc->Alloc((uint)(len + instance_len + 1))))
                                        return 2;

                                extra_groups[i + group->count] = ptr;

                                /** Construct new group */
                                memcpy(ptr, extra_groups[i], len);
                                memcpy(ptr + len, my_defaults_group_suffix, instance_len + 1);
                        }

                        group->count *= 2;
                        group->type_names               = extra_groups;
                        group->type_names[group->count] = nullptr;
                }
        } else if (my_login_path && func == handle_default_option) {
                /* Handle --login_path= */
                uint i;
                size_t len;
                const char **extra_groups;
                size_t instance_len           = 0;
                struct handle_option_ctx *ctx = (struct handle_option_ctx *)func_ctx;
                char *ptr;
                TYPELIB *group = ctx->group;

                if (!(extra_groups = (const char **)ctx->alloc->Alloc((group->count + 3) * sizeof(char *))))
                        return 2;

                for (i = 0; i < group->count; i++) {
                        extra_groups[i] = group->type_names[i]; /** copy group */
                }

                extra_groups[i] = my_login_path;

                if (my_defaults_group_suffix && func == handle_default_option) {
                        instance_len = strlen(my_defaults_group_suffix);
                        len          = strlen(extra_groups[i]);

                        if (!(ptr = (char *)ctx->alloc->Alloc((uint)(len + instance_len + 1))))
                                return 2;

                        extra_groups[i + 1] = ptr;

                        /** Construct new group */
                        memcpy(ptr, extra_groups[i], len);
                        memcpy(ptr + len, my_defaults_group_suffix, instance_len + 1);
                        group->count += 1;
                }

                group->count += 1;
                group->type_names               = extra_groups;
                group->type_names[group->count] = nullptr;
        }

        // If conf_file is an absolute path, we only read it
        if (dirname_length(conf_file)) {
                int error;
                if ((error = search_default_file(func, func_ctx, NullS, conf_file, is_login_file)) < 0)
                        goto err;
        }
        // If my defaults file is set (from a previous run), we read it
        else if (my_defaults_file) {
                int error;
                if ((error = search_default_file_with_ext(func, func_ctx, "", "", my_defaults_file, 0, is_login_file, false)) <
                    0)
                        goto err;
                if (error > 0) {
                        my_message_local(ERROR_LEVEL, EE_FAILED_TO_OPEN_DEFAULTS_FILE, my_defaults_file);
                        goto err;
                }
        } else if (!found_no_defaults) {
                for (dirs = default_directories; *dirs; dirs++) {
                        if (**dirs) {
                                if (search_default_file(func, func_ctx, *dirs, conf_file, is_login_file) < 0)
                                        goto err;
                        } else if (my_defaults_extra_file) {
                                int error;
                                if ((error = search_default_file_with_ext(func,
                                                                          func_ctx,
                                                                          "",
                                                                          "",
                                                                          my_defaults_extra_file,
                                                                          0,
                                                                          is_login_file,
                                                                          false)) < 0)
                                        goto err; /* Fatal error */
                                if (error > 0) {
                                        my_message_local(ERROR_LEVEL, EE_FAILED_TO_OPEN_DEFAULTS_FILE, my_defaults_extra_file);
                                        goto err;
                                }
                        }
                }
        }

        return 0;

err:
        my_message_local(ERROR_LEVEL, EE_FAILED_TO_HANDLE_DEFAULTS_FILE);
        return 1;
}
```

##### init_variable_default_paths

```c++
void init_variable_default_paths() {
        char datadir[FN_REFLEN] = {0};
        string extradir         = (my_defaults_extra_file ? my_defaults_extra_file : string());
        string explicitdir      = (my_defaults_file ? my_defaults_file : string());

        string defsyscondir;
#if defined(DEFAULT_SYSCONFDIR)
        defsyscondir = DEFAULT_SYSCONFDIR;
#endif

#ifdef _WIN32
        char buffer[FN_REFLEN];

        /* windows supports ini/cnf extension for some config files */
        if (GetWindowsDirectory(buffer, sizeof(buffer))) {
                default_paths[string(buffer) + "\\my.ini"] = enum_variable_source::GLOBAL;
                default_paths[string(buffer) + "\\my.cnf"] = enum_variable_source::GLOBAL;
        }
        default_paths["C:\\my.ini"] = enum_variable_source::GLOBAL;
        default_paths["C:\\my.cnf"] = enum_variable_source::GLOBAL;
        if (GetModuleFileName(NULL, buffer, (DWORD)sizeof(buffer))) {
                char *end = strend(buffer), *last = NULL;
                for (; end > buffer; end--) {
                        if (*end == FN_LIBCHAR) {
                                if (last) {
                                        end[1] = 0;
                                        break;
                                }
                                last = end;
                        }
                }
                default_paths[string(buffer) + "\\.mylogin.cnf"] = enum_variable_source::LOGIN;
        }
#else
        char *env = getenv("MYSQL_HOME");
        std::string mysql_home(env == nullptr ? "" : env);
        if (!mysql_home.empty()) {
                if (mysql_home.back() != '/') {
                        mysql_home.push_back('/');
                }
                default_paths[mysql_home + "my.cnf"] = enum_variable_source::SERVER;
        }

        char buffer[FN_REFLEN] = "~/";
        unpack_filename(buffer, buffer);
        default_paths["/etc/my.cnf"]                   = enum_variable_source::GLOBAL;
        default_paths["/etc/mysql/my.cnf"]             = enum_variable_source::GLOBAL;
        default_paths[string(buffer) + ".my.cnf"]      = enum_variable_source::MYSQL_USER;
        default_paths[string(buffer) + ".mylogin.cnf"] = enum_variable_source::LOGIN;

        #if defined(DEFAULT_SYSCONFDIR)
        default_paths[defsyscondir + "/my.cnf"] = enum_variable_source::GLOBAL;
        #endif
#endif

        if (datadir_buffer[0])
                default_paths[string(datadir_buffer) + MYSQL_PERSIST_CONFIG_NAME + ".cnf"] = enum_variable_source::PERSISTED;
        else {
                convert_dirname(datadir, MYSQL_DATADIR, NullS);
                default_paths[string(datadir) + MYSQL_PERSIST_CONFIG_NAME + ".cnf"] = enum_variable_source::PERSISTED;
        }
        if (extradir.length())
                default_paths[extradir] = enum_variable_source::EXTRA;
        if (explicitdir.length())
                default_paths[explicitdir] = enum_variable_source::EXPLICIT;

        default_paths[""] = enum_variable_source::COMMAND_LINE;
}

```

