---
title: init_variable_default_paths
description: init_variable_default_paths
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32

init_variable_default_paths
{{< /alert >}}

### enum_variable_source
```c++
/**
  This enum values define how system variables are set. For example if a
  variable is set by global option file /etc/my.cnf then source will be
  set to GLOBAL, or if a variable is set from command line then source
  will hold value as COMMAND_LINE.
*/
enum enum_variable_source {
        COMPILED = 1,
        GLOBAL,
        SERVER,
        EXPLICIT,
        EXTRA,
        MYSQL_USER,
        LOGIN,
        COMMAND_LINE,
        PERSISTED,
        DYNAMIC
};
```


### init_variable_default_paths
```c++
/**
  Initialize all the mappings between default config file paths/
  command line options/persistent config file path/login file path
  and corresponding enum_variable_source values.
*/
void init_variable_default_paths() {
        char   datadir[FN_REFLEN] = { 0 };
        string extradir           = (my_defaults_extra_file ? my_defaults_extra_file : string());
        string explicitdir        = (my_defaults_file ? my_defaults_file : string());

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
        char       *env = getenv("MYSQL_HOME");
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











