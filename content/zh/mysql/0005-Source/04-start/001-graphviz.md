---
title: 01. 启动流程图
description: graphviz
date: 2023-10-30
weight: 10


viz: true
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

{{</alert>}}


```viz-dot
digraph "main" {
        compound=true;
        ranksep = 0.2;
        node [rankdir=TB;fontsize = "12"];

        mysqld_main [label="mysqld_main", shape = doubleoctagon];
        substitute_progpath [abel="substitute_progpath", shape = octagon];
        mysqld_main -> substitute_progpath;
        notify_connect [abel="notify_connect", shape = octagon];
        substitute_progpath -> notify_connect
        notify [label="WITH_SYSTEMD_DEBUG", shape = octagon];
        notify_connect -> notify;
        calculate_mysql_home_from_my_progname [label="calculate_mysql_home_from_my_progname", shape = octagon];
        notify -> calculate_mysql_home_from_my_progname [label="获取MySQL家目录：$MY_BASEDIR_VERSION"];
        subgraph cluster0 {
                label="pre_initialize_performance_schema"
                record_main_thread_id [label="record_main_thread_id", shape = octagon];
                pfs_initialized [label="pfs_initialized = false", shape = octagon];
                init_all_builtin_memory_class [label="init_all_builtin_memory_class", shape = octagon];
                g_reset_template [label="g_reset_template", shape = octagon];
                global_idle_stat [abel="global_idle_stat", shape = octagon];
                global_table_io_stat [label="global_table_io_stat", shape = octagon];
                global_table_lock_stat [label="global_table_lock_stat", shape = octagon];
                g_histogram_pico_timers [label="g_histogram_pico_timers", shape = octagon];
                global_statements_histogram [label="global_statements_histogram", shape = octagon];
                THR_PFS [label="THR_PFS = nullptr", shape = octagon];

                record_main_thread_id -> pfs_initialized [arrowsize=1];
                pfs_initialized -> init_all_builtin_memory_class;
                init_all_builtin_memory_class -> g_reset_template;
                g_reset_template -> global_idle_stat;
                global_idle_stat -> global_table_io_stat;
                global_table_io_stat -> global_table_lock_stat;
                global_table_lock_stat -> g_histogram_pico_timers;
                g_histogram_pico_timers -> global_statements_histogram;
                global_statements_histogram -> THR_PFS;
        }
        calculate_mysql_home_from_my_progname -> record_main_thread_id [lhead="cluster0",label="性能模式预初始化"];

        subgraph cluster1 {
                label="my_init"
                ENV [label="my_init_done = true\nmy_umask = 0640\nmy_umask_dir = 0750\n getenv(UMASK)\n getenv(UMASK_DIR)", shape = octagon];
                my_thread_global_init [label="my_thread_global_init", shape = octagon];
                my_thread_init [label="my_thread_init", shape = octagon];
                HOME [label="HOME", shape = octagon];
                MyFileInit [label="MyFileInit", shape = octagon];

                ENV -> my_thread_global_init [label="线程全局初始化"];
                my_thread_global_init -> my_thread_init;
                my_thread_init -> HOME
                HOME -> MyFileInit
        }
        THR_PFS -> ENV [lhead="cluster1",ltail="cluster0",label="初始化线程和锁"];

        subgraph cluster2 {
                label="load_defaults"
                my_load_defaults


        }

        sys_var_init



}
```


















