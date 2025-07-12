---
title: mysql_config_editor
date: 2023-10-12
description: mysql_config_editor是MySQL自带的一款用于安全加密登录的工具，可以在一些场合避免使用密码明文


weight: 60000
---


### mysql_config_editor对应的参数信息如下：
>
> - \-\-login-path=name,-G name
>
> - \-\-host=host_name,-h host_name 主机名
>
> - \-\-password,-p 密码，注意这个地方不能使用“=”直接写入密码
>
>- \-\-port=port_num,-P port_num 端口号
>
>- \-\-socket=file_names,-S file_name 文件名
>
>- \-\-user=user_name,-u user_name 用户名
>
>- \-\-warn,-w 默认开启，提示警告信息，如果要忽略警告，使用--skip-warn 参数


```bash

shell> mysql_config_editor set --login-path=mylogin --user=admin  --host=127.0.0.1 --port=3307 --password

shell> mysql_config_editor set --login-path=mylogin_local --user=admin  --host=127.0.0.1 --port=3308 --password

shell> mysql_config_editor print --all
[mylogin]
user = "admin"
password = *****
host = "127.0.0.1"
port = 3307
[mylogin_local]
user = "admin"
password = *****
host = "127.0.0.1"
port = 3308

# 清空指定 login-path
shell> mysql_config_editor remove --login-path=dbadmin
# 清空 login-path
shell> mysql_config_editor reset
shell> mysql_config_editor print --all
```

## 登录

```bash
mylogin_local="mysql --login-path=mylogin_local"
mylogin="mysql --login-path=mylogin"

```



## MYSQL_TEST_LOGIN_FILE

```bash
export MYSQL_TEST_LOGIN_FILE=~/.config/mylogin.cnf

mysql_config_editor set --login-path=mysqldump --host=localhost --user=root --password

```

- 参考： https://dba.stackexchange.com/questions/285144/how-do-i-use-the-mysql-test-login-file-environment-variable-to-change-the-mysql


```c++
void mysql_read_default_options(struct st_mysql_options *options, const char *filename, const char *group) {
        int argc;
        char *argv_buff[1], **argv;
        const char *groups[3];
        DBUG_TRACE;
        DBUG_PRINT("enter", ("file: %s  group: %s", filename, group ? group : "NULL"));

        static_assert(OPT_keep_this_one_last == array_elements(default_options),
                      "OPT_keep_this_one_last needs to be the last element.");

        argc = 1;
        argv = argv_buff;
        argv_buff[0] = const_cast<char *>("client");
        groups[0] = "client";
        groups[1] = group;
        groups[2] = nullptr;

        MEM_ROOT alloc{PSI_NOT_INSTRUMENTED, 512};
        my_load_defaults(filename, groups, &argc, &argv, &alloc, nullptr);
        if (argc != 1) /* If some default option */
        {
                char **option = argv;
                while (*++option) {
                        if (my_getopt_is_args_separator(option[0])) /* skip arguments separator */
                                continue;
                        /* DBUG_PRINT("info",("option: %s",option[0])); */
                        if (option[0][0] == '-' && option[0][1] == '-') {
                                char *end = strchr(*option, '=');
                                char *opt_arg = nullptr;
                                if (end != nullptr) {
                                        opt_arg = end + 1;
                                        *end = 0; /* Remove '=' */
                                }
                                /* Change all '_' in variable name to '-' */
                                for (end = *option; end != nullptr; end = strchr(end, '_')) *end = '-';
                                switch (find_type(*option + 2, &option_types, FIND_TYPE_BASIC)) {
                                        case OPT_port:
                                                if (opt_arg) options->port = atoi(opt_arg);
                                                break;
                                        case OPT_socket:
                                                if (opt_arg) {
                                                        my_free(options->unix_socket);
                                                        options->unix_socket =
                                                            my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                }
                                                break;
                                        case OPT_compress:
                                                options->compress = true;
                                                options->client_flag |= CLIENT_COMPRESS;
                                                break;
                                        case OPT_password:
                                                if (opt_arg) {
                                                        my_free(options->password);
                                                        options->password =
                                                            my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                }
                                                break;
                                        case OPT_pipe:
                                                options->protocol = MYSQL_PROTOCOL_PIPE;
                                                break;
                                        case OPT_connect_timeout:
                                        case OPT_timeout:
                                                if (opt_arg) options->connect_timeout = atoi(opt_arg);
                                                break;
                                        case OPT_user:
                                                if (opt_arg) {
                                                        my_free(options->user);
                                                        options->user =
                                                            my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                }
                                                break;
                                        case OPT_init_command:
                                                add_init_command(options, opt_arg);
                                                break;
                                        case OPT_host:
                                                if (opt_arg) {
                                                        my_free(options->host);
                                                        options->host =
                                                            my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                }
                                                break;
                                        case OPT_database:
                                                if (opt_arg) {
                                                        my_free(options->db);
                                                        options->db = my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                }
                                                break;
                                        case OPT_debug:
#ifndef MYSQL_SERVER
                                                mysql_debug(opt_arg ? opt_arg : "d:t:o,/tmp/client.trace");
                                                break;
#endif
                                        case OPT_return_found_rows:
                                                options->client_flag |= CLIENT_FOUND_ROWS;
                                                break;
                                        case OPT_ssl_key:
                                                my_free(options->ssl_key);
                                                options->ssl_key = my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_ssl_cert:
                                                my_free(options->ssl_cert);
                                                options->ssl_cert = my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_ssl_ca:
                                                my_free(options->ssl_ca);
                                                options->ssl_ca = my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_ssl_capath:
                                                my_free(options->ssl_capath);
                                                options->ssl_capath = my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_ssl_cipher:
                                                my_free(options->ssl_cipher);
                                                options->ssl_cipher = my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_tls_ciphersuites:
                                                EXTENSION_SET_STRING(options, tls_ciphersuites, opt_arg);
                                                break;
                                        case OPT_tls_version:
                                                EXTENSION_SET_SSL_STRING(options, tls_version, opt_arg, SSL_MODE_PREFERRED);
                                                break;
                                        case OPT_ssl_crl:
                                                EXTENSION_SET_SSL_STRING(options, ssl_crl, opt_arg, SSL_MODE_PREFERRED);
                                                break;
                                        case OPT_ssl_crlpath:
                                                EXTENSION_SET_SSL_STRING(options, ssl_crlpath, opt_arg, SSL_MODE_PREFERRED);
                                                break;
                                        case OPT_character_sets_dir:
                                                my_free(options->charset_dir);
                                                options->charset_dir =
                                                    my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_default_character_set:
                                                my_free(options->charset_name);
                                                options->charset_name =
                                                    my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_interactive_timeout:
                                                options->client_flag |= CLIENT_INTERACTIVE;
                                                break;
                                        case OPT_local_infile:
                                                if (!opt_arg || atoi(opt_arg) != 0)
                                                        options->client_flag |= CLIENT_LOCAL_FILES;
                                                else
                                                        options->client_flag &= ~CLIENT_LOCAL_FILES;
                                                break;
                                        case OPT_disable_local_infile:
                                                options->client_flag &= ~CLIENT_LOCAL_FILES;
                                                break;
                                        case OPT_max_allowed_packet:
                                                if (opt_arg) options->max_allowed_packet = atoi(opt_arg);
                                                break;
                                        case OPT_protocol:
                                                if ((options->protocol =
                                                         find_type(opt_arg, &sql_protocol_typelib, FIND_TYPE_BASIC)) <= 0) {
                                                        my_message_local(ERROR_LEVEL, EE_UNKNOWN_PROTOCOL_OPTION, opt_arg);
                                                        exit(1);
                                                }
                                                break;
                                        case OPT_shared_memory_base_name:
#if defined(_WIN32)
                                                my_free(options->shared_memory_base_name);

                                                options->shared_memory_base_name =
                                                    my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
#endif
                                                break;
                                        case OPT_multi_results:
                                                options->client_flag |= CLIENT_MULTI_RESULTS;
                                                break;
                                        case OPT_multi_statements:
                                        case OPT_multi_queries:
                                                options->client_flag |= CLIENT_MULTI_STATEMENTS | CLIENT_MULTI_RESULTS;
                                                break;
                                        case OPT_report_data_truncation:
                                                options->report_data_truncation = opt_arg ? (atoi(opt_arg) != 0) : true;
                                                break;
                                        case OPT_plugin_dir: {
                                                char buff[FN_REFLEN], buff2[FN_REFLEN];
                                                if (strlen(opt_arg) >= FN_REFLEN) opt_arg[FN_REFLEN] = '\0';
                                                if (my_realpath(buff, opt_arg, 0)) {
                                                        DBUG_PRINT("warning",
                                                                   ("failed to normalize the plugin path: %s", opt_arg));
                                                        break;
                                                }
                                                convert_dirname(buff2, buff, nullptr);
                                                EXTENSION_SET_STRING(options, plugin_dir, buff2);
                                        } break;
                                        case OPT_default_auth:
                                                EXTENSION_SET_STRING(options, default_auth, opt_arg);
                                                break;
                                        case OPT_bind_address:
                                                my_free(options->bind_address);
                                                options->bind_address =
                                                    my_strdup(key_memory_mysql_options, opt_arg, MYF(MY_WME));
                                                break;
                                        case OPT_enable_cleartext_plugin:
                                                ENSURE_EXTENSIONS_PRESENT(options);
                                                options->extension->enable_cleartext_plugin =
                                                    (!opt_arg || atoi(opt_arg) != 0) ? true : false;
                                                break;
                                        case OPT_optional_resultset_metadata:
                                                if (!opt_arg || atoi(opt_arg) != 0)
                                                        options->client_flag |= CLIENT_OPTIONAL_RESULTSET_METADATA;
                                                else
                                                        options->client_flag &= ~CLIENT_OPTIONAL_RESULTSET_METADATA;
                                                break;

                                        default:
                                                DBUG_PRINT("warning", ("unknown option: %s", option[0]));
                                }
                        }
                }
        }
}


int my_load_defaults(const char *conf_file, const char **groups, int *argc, char ***argv, MEM_ROOT *alloc,
                     const char ***default_directories) {
        My_args my_args(key_memory_defaults);
        TYPELIB group;
        bool found_print_defaults = false;
        uint args_used = 0;
        int error = 0;
        const char **ptr;
        const char **res;
        struct handle_option_ctx ctx;
        const char **dirs;
        char my_login_file[FN_REFLEN];
        bool found_no_defaults = false;
        uint args_sep = my_getopt_use_args_separator ? 1 : 0;
        DBUG_TRACE;

        if ((dirs = init_default_directories(alloc)) == nullptr) goto err;
        /*
          Check if the user doesn't want any default option processing
          --no-defaults is always the first option
        */
        if (*argc >= 2 && !strcmp(argv[0][1], "--no-defaults")) no_defaults = found_no_defaults = true;

        group.count = 0;
        group.name = "defaults";
        group.type_names = groups;

        for (; *groups; groups++) group.count++;

        ctx.alloc = alloc;
        ctx.m_args = &my_args;
        ctx.group = &group;

        if ((error = my_search_option_files(conf_file, argc, argv, &args_used, handle_default_option, (void *)&ctx, dirs, false,
                                            found_no_defaults))) {
                return error;
        }

        if (my_defaults_read_login_file) {
                /* Read options from login group. */
                if (my_default_get_login_file(my_login_file, sizeof(my_login_file)) &&
                    (error = my_search_option_files(my_login_file, argc, argv, &args_used, handle_default_option, (void *)&ctx,
                                                    dirs, true, found_no_defaults))) {
                        alloc->Clear();
                        return error;
                }
        }
        /*
          Here error contains <> 0 only if we have a fully specified conf_file
          or a forced default file
        */
        if (!(ptr = (const char **)alloc->Alloc((my_args.size() + *argc + 1 + args_sep) * sizeof(char *)))) goto err;
        res = ptr;

        /* copy name + found arguments + command line arguments to new array */
        res[0] = argv[0][0]; /* Name MUST be set */
        if (!my_args.empty()) memcpy((res + 1), &my_args[0], my_args.size() * sizeof(char *));
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

        if (*argc) memcpy((uchar *)(res + 1 + my_args.size() + args_sep), (char *)((*argv) + 1), (*argc - 1) * sizeof(char *));
        res[my_args.size() + *argc + args_sep] = nullptr; /* last null */

        (*argc) += my_args.size() + args_sep;
        *argv = const_cast<char **>(res);

        if (default_directories) *default_directories = dirs;

        if (found_no_defaults) return 0;

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


int my_default_get_login_file(char *file_name, size_t file_name_size) {
        size_t rc;

        if (getenv("MYSQL_TEST_LOGIN_FILE")) rc = snprintf(file_name, file_name_size, "%s", getenv("MYSQL_TEST_LOGIN_FILE"));
#ifdef _WIN32
        else if (getenv("APPDATA"))
                rc = snprintf(file_name, file_name_size, "%s\\MySQL\\.mylogin.cnf", getenv("APPDATA"));
#else
        else if (getenv("HOME"))
                rc = snprintf(file_name, file_name_size, "%s/.mylogin.cnf", getenv("HOME"));
#endif
        else {
                memset(file_name, 0, file_name_size);
                return 0;
        }
        /* Anything <= 0 will be treated as error. */
        if (rc <= 0) return 0;

        return 1;
}
```


