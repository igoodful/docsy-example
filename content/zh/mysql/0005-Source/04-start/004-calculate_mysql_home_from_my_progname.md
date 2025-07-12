---
title: 04. calculate_mysql_home_from_my_progname
description: calculate_mysql_home_from_my_progname
date: 2023-10-30
weight: 40


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32
{{< /alert >}}

{{<alert>}}

- `char *mysql_home_ptr` 指向用户家目录
- `char mysql_home[FN_REFLEN], pidfile_name[FN_REFLEN];` 家目录路径

{{</alert>}}

## calculate_mysql_home_from_my_progname
```c++
static void calculate_mysql_home_from_my_progname() {
        const std::string runtime_output_directory_addon{"/runtime_output_directory/"};
#if defined(_WIN32) || defined(APPLE_XCODE)
        char prg_dev[FN_REFLEN];
        my_path(prg_dev, my_progname, nullptr);
        strcat(prg_dev, "/../");
        cleanup_dirname(mysql_home, prg_dev);
        char cmake_binary_dir[FN_REFLEN];
        size_t dlen = 0;
        dirname_part(cmake_binary_dir, mysql_home, &dlen);
        if (dlen > runtime_output_directory_addon.length() &&
            (!strcmp(cmake_binary_dir + (dlen - runtime_output_directory_addon.length()),
                     runtime_output_directory_addon.c_str()) ||
             !strcmp(cmake_binary_dir + (dlen - runtime_output_directory_addon.length()), "\\runtime_output_directory\\"))) {
                mysql_home[strlen(mysql_home) - 1] = '\0';
                dirname_part(cmake_binary_dir, mysql_home, &dlen);
                strmake(mysql_home, cmake_binary_dir, sizeof(mysql_home) - 1);
        }

#else
        const char *tmpenv = getenv("MY_BASEDIR_VERSION");
        if (tmpenv != nullptr) {
                strmake(mysql_home, tmpenv, sizeof(mysql_home) - 1);
        } else {
                char progdir[FN_REFLEN];
                size_t dlen = 0;
                dirname_part(progdir, my_progname, &dlen);
                if (dlen > runtime_output_directory_addon.length() &&
                    !strcmp(progdir + (dlen - runtime_output_directory_addon.length()),
                            runtime_output_directory_addon.c_str())) {
                        char cmake_binary_dir[FN_REFLEN];
                        progdir[strlen(progdir) - 1] = '\0';  // remove trailing "/"
                        dirname_part(cmake_binary_dir, progdir, &dlen);
                        strmake(mysql_home, cmake_binary_dir, sizeof(mysql_home) - 1);
                } else {
                        strcat(progdir, "/../");
                        cleanup_dirname(mysql_home, progdir);
                }
        }
#endif
        mysql_home_ptr = mysql_home;
}

```

## strmake
```c++
char *strmake(char *dst, const char *src, size_t length) {
#ifdef EXTRA_DEBUG
        /*
          'length' is the maximum length of the string; the buffer needs
          to be one character larger to accommodate the terminating '\0'.
          This is easy to get wrong, so we make sure we write to the
          entire length of the buffer to identify incorrect buffer-sizes.
          We only initialise the "unused" part of the buffer here, a) for
          efficiency, and b) because dst==src is allowed, so initialising
          the entire buffer would overwrite the source-string. Also, we
          write a character rather than '\0' as this makes spotting these
          problems in the results easier.
        */
        uint n = 0;
        while (n < length && src[n++])
                ;
        memset(dst + n, (int)'Z', length - n + 1);
#endif

        while (length--)
                if (!(*dst++ = *src++))
                        return dst - 1;
        *dst = 0;
        return dst;
}
```









