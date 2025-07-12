---
title: calculate_mysql_home_from_my_progname
description: calculate_mysql_home_from_my_progname
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}



### calculate_mysql_home_from_my_progname
```c++
static void calculate_mysql_home_from_my_progname() {
  const std::string runtime_output_directory_addon{
      "/runtime_output_directory/"};
#if defined(_WIN32) || defined(APPLE_XCODE)
  /* Allow Win32 users to move MySQL anywhere */
  char prg_dev[FN_REFLEN];
  my_path(prg_dev, my_progname, nullptr);

  // On windows or Xcode the basedir will always be one level up from where
  // the executable is located. E.g. <basedir>/bin/mysqld.exe in a
  // package, or <basedir>/runtime_output_directory/<buildconfig>/mysqld.exe
  // for a sandbox build.
  strcat(prg_dev, "/../");  // Remove containing directory to get base dir
  cleanup_dirname(mysql_home, prg_dev);

  // New layout: <cmake_binary_dir>/runtime_output_directory/<buildconfig>/
  char cmake_binary_dir[FN_REFLEN];
  size_t dlen = 0;
  dirname_part(cmake_binary_dir, mysql_home, &dlen);
  if (dlen > runtime_output_directory_addon.length() &&
      (!strcmp(
           cmake_binary_dir + (dlen - runtime_output_directory_addon.length()),
           runtime_output_directory_addon.c_str()) ||
       !strcmp(
           cmake_binary_dir + (dlen - runtime_output_directory_addon.length()),
           "\\runtime_output_directory\\"))) {
    mysql_home[strlen(mysql_home) - 1] = '\0';  // remove trailing
    dirname_part(cmake_binary_dir, mysql_home, &dlen);
    strmake(mysql_home, cmake_binary_dir, sizeof(mysql_home) - 1);
  }
  // The sql_print_information below outputs nothing ??
  // fprintf(stderr, "mysql_home %s\n", mysql_home);
  // fflush(stderr);
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



### dirname_part
```c++
size_t dirname_part(char *to, const char *name, size_t *to_res_length) {
  size_t length;
  DBUG_TRACE;
  DBUG_PRINT("enter", ("'%s'", name));

  length = dirname_length(name);
  *to_res_length = (size_t)(convert_dirname(to, name, name + length) - to);
  return length;
} /* dirname */

```

### dirname_length
> /root/local/mysql_8032/bin/mysqld
```c++
size_t dirname_length(const char *name) {
#ifdef _WIN32
  CHARSET_INFO *fs = fs_character_set();
#endif
  const char *pos = name - 1;
#ifdef FN_DEVCHAR
  const char *devchar_pos = strrchr(name, FN_DEVCHAR);
  if (devchar_pos != nullptr) pos = devchar_pos;
#endif

  const char *gpos = pos++;
  for (; *pos; pos++) /* Find last FN_LIBCHAR */
  {
#ifdef _WIN32
    uint l;
    if (use_mb(fs) && (l = my_ismbchar(fs, pos, pos + 3))) {
      pos += l - 1;
      continue;
    }
#endif
    if (is_directory_separator(*pos)) gpos = pos;
  }
  return gpos + 1 - name;
}
```
{{< alert  color="secondary"  >}}
<!---->
举例说明：
- name="/root/local/mysql_8032/bin/mysqld"
- size_t = "/root/local/mysql_8032/bin/"的字节数= 27

{{< /alert>}}

### convert_dirname
```c++
char *convert_dirname(char *to, const char *from, const char *from_end) {
  char *to_org = to;
#ifdef _WIN32
  CHARSET_INFO *fs = fs_character_set();
#endif
  DBUG_TRACE;

  /* We use -2 here, because we need place for the last FN_LIBCHAR */
  if (!from_end || (from_end - from) > FN_REFLEN - 2)
    from_end = from + FN_REFLEN - 2;

#if FN_LIBCHAR != '/'
  {
    for (; from < from_end && *from; from++) {
      if (*from == '/')
        *to++ = FN_LIBCHAR;
      else {
#ifdef _WIN32
        uint l;
        if (use_mb(fs) && (l = my_ismbchar(fs, from, from + 3))) {
          memmove(to, from, l);
          to += l;
          from += l - 1;
          to_org = to; /* Don't look inside mbchar */
        } else
#endif
        {
          *to++ = *from;
        }
      }
    }
    *to = 0;
  }
#else
  /* This is ok even if to == from, because we need to cut the string */
  to = strmake(to, from, (size_t)(from_end - from));
#endif

  /* Add FN_LIBCHAR to the end of directory path */
  if (to != to_org && (to[-1] != FN_LIBCHAR && to[-1] != FN_DEVCHAR)) {
    *to++ = FN_LIBCHAR;
    *to = 0;
  }
  return to; /* Pointer to end of dir */
} /* convert_dirname */
```


