---
title: 02. substitute_progpath
description: substitute_progpath
date: 2023-10-30
weight: 20


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

- `FN_HOMELIB = ~`
- `FN_LIBCHAR = /`
- `NullS = (char *)0`

- `extern char *home_dir;` 用户家目录
- `extern const char *my_progname;` 程序名称





{{</alert>}}

## substitute_progpath
```c++
void substitute_progpath(char **argv) {
        if (test_if_hard_path(argv[0])) return;

#if defined(_WIN32)
        if (GetModuleFileName(NULL, my_progpath, sizeof(my_progpath))) {
                my_orig_progname = argv[0];
                argv[0]          = my_progpath;
        }
#else
        /* If the path has a directory component, use my_realpath()
           (implicitly relative to cwd) */
        if (strchr(argv[0], FN_LIBCHAR) != nullptr && !my_realpath(my_progpath, argv[0], MYF(0))) {
                my_orig_progname = argv[0];
                argv[0]          = my_progpath;
                return;
        }

        // my_realpath() cannot resolve it, it must be a bare executable
        // name in path
        assert(strchr(argv[0], FN_LIBCHAR) == nullptr);

        const char *spbegin = getenv("PATH");
        if (spbegin == nullptr) spbegin = "";
        const char *spend = spbegin + strlen(spbegin);

        while (true) {
                const char *colonend = std::find(spbegin, spend, ':');
                std::string cand{spbegin, colonend};
                spbegin = colonend + 1;

                cand.append(1, '/');
                cand.append(argv[0]);

                if (my_access(cand.c_str(), X_OK) == 0) {
                        if (my_realpath(my_progpath, cand.c_str(), MYF(0))) {
                                // Fallback to raw cand
                                assert(cand.length() < FN_REFLEN);
                                std::copy(cand.begin(), cand.end(), my_progpath);
                                my_progpath[cand.length()] = '\0';
                        }
                        my_orig_progname = argv[0];
                        argv[0]          = my_progpath;
                        break;
                }
                if (colonend == spend) {
                        assert(false);
                        break;
                }
        }  // while (true)
#endif  // defined(_WIN32)
        if (my_orig_progname == nullptr) {
                LogErr(WARNING_LEVEL, ER_FAILED_TO_GET_ABSOLUTE_PATH, argv[0]);
        }
}
```


## test_if_hard_path
```c++
int test_if_hard_path(const char *dir_name) {
        if (dir_name[0] == FN_HOMELIB && dir_name[1] == FN_LIBCHAR) return (home_dir != NullS && test_if_hard_path(home_dir));
        if (dir_name[0] == FN_LIBCHAR) return (true);
#ifdef FN_DEVCHAR
        return (strchr(dir_name, FN_DEVCHAR) != 0);
#else
        return false;
#endif
} /* test_if_hard_path */
```








