---
title: sql/mysqld.cc
description: mysqld_main函数
date: 2023-10-30
weight: 10001


---

{{< alert >}}

- __environ="PKG_CONFIG_PATH=/db/storage/guolicheng/local/lib/pkgconfig"


#### 环境变量
- NOTIFY_SOCKET


{{< /alert >}}

### mysqld_main
> 1. 检测线程栈的方向
> 2. 解析argv[0]
> 3. 处理环境变量NOTIFY_SOCKET和WITH_SYSTEMD_DEBUG
> 4. 读取配置文件和解析命令行选项
> 根据全局变量HOME获取：home_dir
> 初始化变量
> HAVE_PSI_INTERFACEHAVE_PSI_INTERFACE：PSI钩子，每个都对应着特定的模块或活动，用于收集和记录性能相关的数据



**HAVE_PSI_INTERFACEHAVE_PSI_INTERFACE：**
- psi_thread_hook 用于线程相关的性能数据，包括线程执行的信息。
- psi_mutex_hook 用于互斥锁的性能数据，包括锁的使用情况。
- psi_rwlock_hook 用于读写锁的性能数据，包括读写锁的使用情况。
- psi_cond_hook 用于条件变量的性能数据，包括条件变量的使用情况。
- psi_file_hook 用于文件 I/O 操作的性能数据，包括文件读写的信息。
- psi_socket_hook 用于套接字操作的性能数据，包括套接字的创建和使用情况。
- psi_table_hook 用于表访问的性能数据，包括表的读写和锁的使用情况。
- psi_mdl_hook 用于元数据锁（MDL）的性能数据，包括元数据锁的使用情况。
- psi_idle_hook 用于空闲状态的性能数据，包括数据库空闲时的信息。
- psi_stage_hook 用于阶段（stage）的性能数据，包括各个阶段的计时和统计信息。
- psi_statement_hook 用于 SQL 语句执行的性能数据，包括语句的执行情况。
- psi_transaction_hook 用于事务的性能数据，包括事务的提交和回滚情况。
- psi_memory_hook 用于内存分配和释放的性能数据，包括内存使用情况。
- psi_error_hook 用于错误处理的性能数据，包括错误的发生和处理情况。
- psi_data_lock_hook 用于数据锁的性能数据，包括数据锁的使用情况。
- psi_system_hook 用于系统级性能数据，包括系统级别的信息和统计数据。
- psi_tls_channel_hook 用于 TLS 通道的性能数据，包括 TLS 通道的使用情况。

PSI（Performance Schema Instrumentation）是 MySQL 数据库引擎中的一项功能，用于提供详细的性能信息和统计数据。它允许开发人员和管理员监视和分析数据库系统的性能，以便更好地了解系统的行为、瓶颈和资源利用情况。

具体来说，PSI 提供了一种轻量级的仪表框架，可以测量数据库内部的各种活动。这些活动包括线程执行、锁的使用、文件 I/O、表访问、内存分配和释放等。通过收集这些性能数据，开发人员可以诊断性能问题、优化查询和配置，并更好地了解数据库引擎的内部运行情况。

在你提供的 MySQL 代码片段中，通过使用 PSI 钩子和服务，MySQL 在运行时能够动态地收集和记录各种性能相关的信息，从而支持性能分析和监测。每个模块（如线程、锁、文件、表等）都有对应的 PSI 服务，用于跟踪和记录相应活动的性能数据。这有助于开发人员优化数据库系统的性能，并更好地理解其运行状况。







## mysqld_main
这段代码是 MySQL 中 mysqld_main 函数的一部分，它负责 MySQL 服务器的启动和初始化。以下是对该代码的中文详解：

- initialize_stack_direction:初始化堆栈方向。
- substitute_progpath:替换程序路径。
- sysd::notify_connect:使用系统服务通知连接。
- sysd::notify:使用系统服务发送通知，这里发送 "STATUS=Server startup in progress" 通知。
- my_progname:设置 MySQL 程序名。
- calculate_mysql_home_from_my_progname:通过程序名计算 MySQL 的主目录。
- pre_initialize_performance_schema:在非 Windows 系统上，用于在初始化性能模式之前执行一些预初始化工作。
- my_init:MySQL 初始化，包括内存分配和其他一些初始化。
- load_defaults:加载配置文件，设置默认参数。
- strmake(mysql_real_data_home, get_relative_path(MYSQL_DATADIR), sizeof(mysql_real_data_home) - 1):构建 mysql_real_data_home 变量，表示 MySQL 数据目录的绝对路径。
- system_charset_info:设置系统字符集信息。
- local_message_hook:设置本地消息处理钩子。
- sys_var_init:初始化系统变量。
- persisted_variables_cache:处理持久化变量的缓存。
- remaining_argc, remaining_argv:保存未处理的命令行参数。
- init_variable_default_paths:初始化变量的默认路径。
- heo_error = handle_early_options():处理早期选项，执行一些早期的设置。
- init_sql_statement_names:初始化 SQL 语句的名称。
- requested_open_files:用于存储请求的打开文件数量。
- init_error_log:初始化错误日志。
- component_infrastructure_init:初始化组件基础设施。
- files_charset_info:设置文件字符集信息。
- keyring_helper:处理密钥环的帮助类，包括初始化密钥环。
- initialize_manifest_file_components:初始化清单文件中的组件。
- set_srv_keyring_implementation_as_default:将服务密钥环实现设置为默认值。
- persisted_variables_cache:处理持久化变量的缓存，包括读取和设置。
- my_getopt_skip_unknown:设置是否跳过未知的命令行选项。
- init_server_psi_keys:初始化服务器 PSI 键。
- my_thread_global_reinit:重新初始化线程全局变量。
- init_ssl:初始化 SSL。
- umask:设置文件掩码。
- component_infrastructure_init:再次初始化组件基础设施。
- files_charset_info:设置文件字符集信息。
- initialize_information_schema_acl:初始化信息模式的 ACL。
- create_pid_file:创建 PID 文件。
- reload_optimizer_cost_constants:重新加载优化器成本常量。
- mysql_component_infrastructure_init:再次初始化组件基础设施。
- mysql_rm_tmp_tables:删除临时表。
- handle_options:处理命令行选项。
- acl_init:初始化访问控制列表（ACL）。
- init_acl_memory:初始化 ACL 内存。
- dynamic_privilege_init:初始化动态权限。
- initialize_performance_schema_acl:初始化性能模式的 ACL。
- initialize_information_schema_acl:初始化信息模式的 ACL。
- Servers_init:初始化服务器。
- udf_read_functions_table:读取 UDF 函数表。
- init_status_vars:初始化状态变量。
- check_binlog_cache_size:检查二进制日志缓存大小。
- check_binlog_stmt_cache_size:检查二进制日志语句缓存大小。
- binlog_unsafe_map_init:初始化二进制日志不安全映射。
- ReplicaInitializer:复制初始化器，用于初始化复制。
- initialize_performance_schema_acl:初始化性能模式的 ACL。
- initialize_information_schema_acl:初始化信息模式的 ACL。
- after_recovery_hook:在数据库恢复后执行的挂钩。
- start_signal_handler:启动信号处理程序。
- validate_authentication_policy:验证身份验证策略。
- update_authentication_policy:更新身份验证策略。
- set_persisted_options:设置持久化选项。
- process_bootstrap:处理引导。
- mysql_audit_notify:发送审计通知。
- mysqld_process_must_end_at_startup:MySQL 进程在启动时是否必须结束的标志。
- create_compress_gtid_table_thread:创建压缩 GTID 表的线程。
- LOG_TYPE_ERROR / LOG_SUBSYSTEM_TAG / SYSTEM_LEVEL / lookup(...):记录日志消息，表示 MySQL 服务器已启动。
- opt_disable_networking:禁用网络。
- mysql_admin_bind_addr_str:MySQL 管理地址字符串。
- start_handle_manager:启动处理管理器。
- create_compress_gtid_table_thread:创建压缩 GTID 表的线程。
- LogEvent / MYSQL_SET_STAGE / after_engine_recovery_hook:记录日志事件，设置



```c++
#ifdef _WIN32
int win_main(int argc, char **argv)
#else
int mysqld_main(int argc, char **argv)
#endif
{
        // Substitute the full path to the executable in argv[0]
        substitute_progpath(argv);
        sysd::notify_connect();
        sysd::notify("STATUS=Server startup in progress\n");

        /*
          Perform basic thread library and malloc initialization,
          to be able to read defaults files and parse options.
        */
        my_progname = argv[0];
        calculate_mysql_home_from_my_progname();

#ifndef _WIN32
#ifdef WITH_PERFSCHEMA_STORAGE_ENGINE
        pre_initialize_performance_schema();
#endif /*WITH_PERFSCHEMA_STORAGE_ENGINE */
        // For windows, my_init() is called from the win specific mysqld_main
        if (my_init()) // init my_sys library & pthreads
        {
                LogErr(ERROR_LEVEL, ER_MYINIT_FAILED);
                flush_error_log_messages();
                return 1;
        }
#endif /* _WIN32 */

        orig_argc                    = argc;
        orig_argv                    = argv;
        my_getopt_use_args_separator = true;
        my_defaults_read_login_file  = false;
        if (load_defaults(MYSQL_CONFIG_NAME, load_default_groups, &argc, &argv, &argv_alloc)) {
                flush_error_log_messages();
                return 1;
        }

        /* Set data dir directory paths */
        strmake(mysql_real_data_home, get_relative_path(MYSQL_DATADIR), sizeof(mysql_real_data_home) - 1);

        /* Must be initialized early for comparison of options name */
        system_charset_info = &my_charset_utf8mb3_general_ci;

        /* Write mysys error messages to the error log. */
        local_message_hook = error_log_print;

        sys_var_init();

#ifdef _WIN32
        if (mysys::is_my_malloc_using_jemalloc()) {
                LogErr(INFORMATION_LEVEL, ER_MY_MALLOC_USING_JEMALLOC);
        } else {
                for (auto &msg : mysys::fetch_jemalloc_initialization_messages()) {
                        LogErr(msg.m_severity, ER_MY_MALLOC_USING_JEMALLOC + msg.m_ecode, msg.m_message.c_str());
                }
        }
#endif
        /*
         Initialize variables cache for persisted variables, load persisted
         config file and append parse early  read only persisted variables
         to command line options if present.
        */
        bool arg_separator_added = false;
        if (persisted_variables_cache.init(&argc, &argv) || persisted_variables_cache.load_persist_file() ||
            persisted_variables_cache.append_parse_early_variables(&argc, &argv, arg_separator_added)) {
                flush_error_log_messages();
                return 1;
        }

        remaining_argc = argc;
        remaining_argv = argv;

        init_variable_default_paths();

        int heo_error;

#ifdef WITH_PERFSCHEMA_STORAGE_ENGINE
        /*
          Initialize the array of performance schema instrument configurations.
        */
        init_pfs_instrument_array();
#endif /* WITH_PERFSCHEMA_STORAGE_ENGINE */

        heo_error = handle_early_options();

        init_sql_statement_names();
        ulong requested_open_files = 0;

        //  Init error log subsystem. This does not actually open the log yet.
        if (init_error_log())
                unireg_abort(MYSQLD_ABORT_EXIT);
        if (!opt_validate_config)
                adjust_related_options(&requested_open_files);

#ifdef WITH_PERFSCHEMA_STORAGE_ENGINE
        if (heo_error == 0) {
                if (!is_help_or_validate_option() && !opt_initialize) {
                        int pfs_rc;
                        /* Add sizing hints from the server sizing parameters.
                         */
                        pfs_param.m_hints.m_table_definition_cache  = table_def_size;
                        pfs_param.m_hints.m_table_open_cache        = table_cache_size;
                        pfs_param.m_hints.m_max_connections         = max_connections;
                        pfs_param.m_hints.m_open_files_limit        = requested_open_files;
                        pfs_param.m_hints.m_max_prepared_stmt_count = max_prepared_stmt_count;

                        pfs_rc = initialize_performance_schema(&pfs_param,
                                                               &psi_thread_hook,
                                                               &psi_mutex_hook,
                                                               &psi_rwlock_hook,
                                                               &psi_cond_hook,
                                                               &psi_file_hook,
                                                               &psi_socket_hook,
                                                               &psi_table_hook,
                                                               &psi_mdl_hook,
                                                               &psi_idle_hook,
                                                               &psi_stage_hook,
                                                               &psi_statement_hook,
                                                               &psi_transaction_hook,
                                                               &psi_memory_hook,
                                                               &psi_error_hook,
                                                               &psi_data_lock_hook,
                                                               &psi_system_hook,
                                                               &psi_tls_channel_hook);
                        if ((pfs_rc != 0) && pfs_param.m_enabled) {
                                pfs_param.m_enabled = false;
                                LogErr(WARNING_LEVEL, ER_PERFSCHEMA_INIT_FAILED);
                        }
                }
        }
#endif /* WITH_PERFSCHEMA_STORAGE_ENGINE */

#ifdef WITH_LOCK_ORDER
        if (heo_error == 0) {
                if (lo_param.m_enabled && !opt_help && !opt_initialize) {
                        int lo_rc;
                        lo_rc = LO_init(&lo_param,
                                        &psi_thread_hook,
                                        &psi_mutex_hook,
                                        &psi_rwlock_hook,
                                        &psi_cond_hook,
                                        &psi_file_hook,
                                        &psi_socket_hook,
                                        &psi_table_hook,
                                        &psi_mdl_hook,
                                        &psi_idle_hook,
                                        &psi_stage_hook,
                                        &psi_statement_hook,
                                        &psi_transaction_hook,
                                        &psi_memory_hook);
                        if (lo_rc != 0) {
                                LogErr(WARNING_LEVEL, ER_LOCK_ORDER_INIT_FAILED);
                        }
                }
        }
#endif /* WITH_LOCK_ORDER */

        /*
          Other provider of the instrumentation interface should
          initialize PSI_hook here:
          - HAVE_PSI_INTERFACE is for the instrumentation interface
          - WITH_PERFSCHEMA_STORAGE_ENGINE is for one implementation
            of the interface,
          but there could be alternate implementations, which is why
          these two defines are kept separate.
        */

#ifdef HAVE_PSI_INTERFACE
        /*
          Obtain the current performance schema instrumentation interface,
          if available.
        */

        void *service;

        if (psi_thread_hook != nullptr) {
                service = psi_thread_hook->get_interface(PSI_CURRENT_THREAD_VERSION);
                if (service != nullptr) {
                        set_psi_thread_service(service);
                }
        }

        if (psi_mutex_hook != nullptr) {
                service = psi_mutex_hook->get_interface(PSI_CURRENT_MUTEX_VERSION);
                if (service != nullptr) {
                        set_psi_mutex_service(service);
                }
        }

        if (psi_rwlock_hook != nullptr) {
                service = psi_rwlock_hook->get_interface(PSI_CURRENT_RWLOCK_VERSION);
                if (service != nullptr) {
                        set_psi_rwlock_service(service);
                }
        }

        if (psi_cond_hook != nullptr) {
                service = psi_cond_hook->get_interface(PSI_CURRENT_COND_VERSION);
                if (service != nullptr) {
                        set_psi_cond_service(service);
                }
        }

        if (psi_file_hook != nullptr) {
                service = psi_file_hook->get_interface(PSI_CURRENT_FILE_VERSION);
                if (service != nullptr) {
                        set_psi_file_service(service);
                }
        }

        if (psi_socket_hook != nullptr) {
                service = psi_socket_hook->get_interface(PSI_CURRENT_SOCKET_VERSION);
                if (service != nullptr) {
                        set_psi_socket_service(service);
                }
        }

        if (psi_table_hook != nullptr) {
                service = psi_table_hook->get_interface(PSI_CURRENT_TABLE_VERSION);
                if (service != nullptr) {
                        set_psi_table_service(service);
                }
        }

        if (psi_mdl_hook != nullptr) {
                service = psi_mdl_hook->get_interface(PSI_CURRENT_MDL_VERSION);
                if (service != nullptr) {
                        set_psi_mdl_service(service);
                }
        }

        if (psi_idle_hook != nullptr) {
                service = psi_idle_hook->get_interface(PSI_CURRENT_IDLE_VERSION);
                if (service != nullptr) {
                        set_psi_idle_service(service);
                }
        }

        if (psi_stage_hook != nullptr) {
                service = psi_stage_hook->get_interface(PSI_CURRENT_STAGE_VERSION);
                if (service != nullptr) {
                        set_psi_stage_service(service);
                }
        }

        if (psi_statement_hook != nullptr) {
                service = psi_statement_hook->get_interface(PSI_CURRENT_STATEMENT_VERSION);
                if (service != nullptr) {
                        set_psi_statement_service(service);
                }
        }

        if (psi_transaction_hook != nullptr) {
                service = psi_transaction_hook->get_interface(PSI_CURRENT_TRANSACTION_VERSION);
                if (service != nullptr) {
                        set_psi_transaction_service(service);
                }
        }

        if (psi_memory_hook != nullptr) {
                service = psi_memory_hook->get_interface(PSI_CURRENT_MEMORY_VERSION);
                if (service != nullptr) {
                        set_psi_memory_service(service);
                }
        }

        if (psi_error_hook != nullptr) {
                service = psi_error_hook->get_interface(PSI_CURRENT_ERROR_VERSION);
                if (service != nullptr) {
                        set_psi_error_service(service);
                }
        }

        if (psi_data_lock_hook != nullptr) {
                service = psi_data_lock_hook->get_interface(PSI_CURRENT_DATA_LOCK_VERSION);
                if (service != nullptr) {
                        set_psi_data_lock_service(service);
                }
        }

        if (psi_system_hook != nullptr) {
                service = psi_system_hook->get_interface(PSI_CURRENT_SYSTEM_VERSION);
                if (service != nullptr) {
                        set_psi_system_service(service);
                }
        }

        if (psi_tls_channel_hook != nullptr) {
                service = psi_tls_channel_hook->get_interface(PSI_CURRENT_TLS_CHANNEL_VERSION);
                if (service != nullptr) {
                        set_psi_tls_channel_service(service);
                }
        }

        /*
          Now that we have parsed the command line arguments, and have
          initialized the performance schema itself, the next step is to
          register all the server instruments.
        */
        init_server_psi_keys();

        /*
          Now that some instrumentation is in place,
          recreate objects which were initialised early,
          so that they are instrumented as well.
        */
        my_thread_global_reinit();
#endif /* HAVE_PSI_INTERFACE */

        /* This limits ability to configure SSL library through config options
         */
        init_ssl();

        /* Set umask as early as possible */
        umask(((~my_umask) & 0666));

        /*
          Initialize Components core subsystem early on, once we have PSI, which
          it uses. This part doesn't use any more MySQL-specific functionalities
          but error logging and PFS.
        */
        if (component_infrastructure_init()) {
                flush_error_log_messages();
                return 1;
        }

        {
                /* Must be initialized early because it is required by dynamic
                 * loader */
                files_charset_info  = &my_charset_utf8mb3_general_ci;
                auto keyring_helper = std::make_unique<Plugin_and_data_dir_option_parser>(remaining_argc, remaining_argv);

                if (keyring_helper->valid() == false) {
                        flush_error_log_messages();
                        return 1;
                }

                if (initialize_manifest_file_components()) {
                        flush_error_log_messages();
                        return 1;
                }

                /*
                  If keyring component was loaded through manifest file,
                  services provided by such a component should get priority over
                  keyring plugin. That's why we have to set defaults before
                  proxy keyring services are loaded.
                */
                set_srv_keyring_implementation_as_default();
        }

        /*
          Append read only persisted variables to command line now.
          Note that if arg separator is already added, it will not
          be added again.
        */
        if (persisted_variables_cache.append_read_only_variables(
                    &remaining_argc, &remaining_argv, arg_separator_added, false)) {
                flush_error_log_messages();
                return 1;
        }
        my_getopt_use_args_separator = false;

        /*
          Initialize Performance Schema component services.
        */
#ifdef HAVE_PSI_THREAD_INTERFACE
        if (!is_help_or_validate_option() && !opt_initialize) {
                register_pfs_notification_service();
                register_pfs_resource_group_service();
        }
#endif

        // Initialize the resource group subsystem.
        auto res_grp_mgr = resourcegroups::Resource_group_mgr::instance();
        if (!is_help_or_validate_option() && !opt_initialize) {
                if (res_grp_mgr->init()) {
                        LogErr(ERROR_LEVEL, ER_RESOURCE_GROUP_SUBSYSTEM_INIT_FAILED);
                        unireg_abort(MYSQLD_ABORT_EXIT);
                }
        }

#ifdef HAVE_PSI_THREAD_INTERFACE
        /* Instrument the main thread */
        PSI_thread *psi = PSI_THREAD_CALL(new_thread)(key_thread_main, 0, nullptr, 0);
        PSI_THREAD_CALL(set_thread_os_id)(psi);
        PSI_THREAD_CALL(set_thread)(psi);
#endif /* HAVE_PSI_THREAD_INTERFACE */

        /* Initialize audit interface globals. Audit plugins are inited later.
         */
        mysql_audit_initialize();

        Srv_session::module_init();

        /*
          Perform basic query log initialization. Should be called after
          MY_INIT, as it initializes mutexes.
        */
        query_logger.init();

        if (heo_error) {
                /*
                  Parsing command line option failed,
                  Since we don't have a workable remaining_argc/remaining_argv
                  to continue the server initialization, this is as far as this
                  code can go.
                  This is the best effort to log meaningful messages:
                  - messages will be printed to stderr, which is not redirected
                  yet,
                  - messages will be printed in the NT event log, for windows.
                */
                flush_error_log_messages();
                /*
                  Not enough initializations for unireg_abort()
                  Using exit() for windows.
                */
                exit(MYSQLD_ABORT_EXIT);
        }

        if (init_common_variables()) {
                setup_error_log();
                unireg_abort(MYSQLD_ABORT_EXIT); // Will do exit
        }

        keyring_lockable_init();

        my_init_signals();
        /*
          Install server's my_abort routine to assure my_aborts prints signal
          info sequentially without sudden termination.
        */
        set_my_abort(my_server_abort);

        size_t guardize = 0;
#ifndef _WIN32
        int retval = pthread_attr_getguardsize(&connection_attrib, &guardize);
        assert(retval == 0);
        if (retval != 0)
                guardize = my_thread_stack_size;
#endif

#if defined(__ia64__) || defined(__ia64)
        /*
          Peculiar things with ia64 platforms - it seems we only have half the
          stack size in reality, so we have to double it here
        */
        guardize = my_thread_stack_size;
#endif

        if (0 != my_thread_attr_setstacksize(&connection_attrib, my_thread_stack_size + guardize)) {
                assert(false);
        }

        {
                /* Retrieve used stack size;  Needed for checking stack
                 * overflows */
                size_t stack_size = 0;
                my_thread_attr_getstacksize(&connection_attrib, &stack_size);

                /* We must check if stack_size = 0 as Solaris 2.9 can return 0
                 * here */
                if (stack_size && stack_size < (my_thread_stack_size + guardize)) {
                        LogErr(WARNING_LEVEL, ER_STACKSIZE_UNEXPECTED, my_thread_stack_size + guardize, (long)stack_size);
#if defined(__ia64__) || defined(__ia64)
                        my_thread_stack_size = stack_size / 2;
#else
                        my_thread_stack_size = static_cast<ulong>(stack_size - guardize);
#endif
                }
        }

#ifndef NDEBUG
        test_lc_time_sz();
        srand(static_cast<uint>(time(nullptr)));
#endif

#if !defined(_WIN32)

        if (opt_initialize && opt_daemonize) {
                fprintf(stderr, "Initialize and daemon options are incompatible.\n");
                unireg_abort(MYSQLD_ABORT_EXIT);
        }

        if (opt_daemonize && log_error_dest == disabled_my_option && (isatty(STDOUT_FILENO) || isatty(STDERR_FILENO))) {
                // Just use the default in this case.
                log_error_dest = "";
        }

        if (opt_daemonize && !opt_validate_config) {
                if (chdir("/") < 0) {
                        LogErr(ERROR_LEVEL, ER_CANNOT_CHANGE_TO_ROOT_DIR, strerror(errno));
                        unireg_abort(MYSQLD_ABORT_EXIT);
                }

                if ((pipe_write_fd = mysqld::runtime::mysqld_daemonize()) < -1) {
                        LogErr(ERROR_LEVEL, ER_FAILED_START_MYSQLD_DAEMON);
                        unireg_abort(MYSQLD_ABORT_EXIT);
                }

                if (pipe_write_fd < 0) {
                        // This is the launching process and the daemon appears
                        // to have started ok (Need to call unireg_abort with
                        // success here to clean up resources in the launching
                        // process.
                        unireg_abort(MYSQLD_SUCCESS_EXIT);
                }

                // Need to update the value of current_pid so that it reflects
                // the pid of the daemon (the previous value was set by
                // unireg_init() while still in the launcher process.
                current_pid = static_cast<ulong>(getpid());
        }
#endif

#ifndef _WIN32
        user_info = check_user(mysqld_user);
        if (!user_info.IsVoid()) {
#if HAVE_CHOWN
                if (unlikely(opt_initialize)) {
                        /* need to change the owner of the freshly created data
                         * directory */
                        MY_STAT stat;
                        char    errbuf[MYSYS_STRERROR_SIZE];
                        bool    must_chown = true;

                        /* fetch the directory's owner */
                        if (!my_stat(mysql_real_data_home, &stat, MYF(0))) {
                                LogErr(INFORMATION_LEVEL,
                                       ER_CANT_STAT_DATADIR,
                                       my_errno(),
                                       my_strerror(errbuf, sizeof(errbuf), my_errno()));
                        }
                        /* Don't change it if it's already the same as SElinux
                           stops this */
                        else if (stat.st_uid == user_info.pw_uid && stat.st_gid == user_info.pw_gid)
                                must_chown = false;

                        if (must_chown && chown(mysql_real_data_home, user_info.pw_uid, user_info.pw_gid)) {
                                LogErr(ERROR_LEVEL, ER_CANT_CHOWN_DATADIR, mysqld_user);
                                unireg_abort(1);
                        }
                }
#endif

#if defined(HAVE_MLOCKALL) && defined(MCL_CURRENT)
                if (locked_in_memory) // getuid() == 0 here
                        set_effective_user(user_info);
                else
#endif
                        set_user(mysqld_user, user_info);
        }
#endif // !_WIN32

        /*
         initiate key migration if any one of the migration specific
         options are provided.
        */
        if (opt_keyring_migration_source || opt_keyring_migration_destination || migrate_connect_options) {
                int exit_state = MYSQLD_ABORT_EXIT;
                while (true) {
                        Migrate_keyring mk;
                        my_getopt_skip_unknown = true;
                        if (mk.init(remaining_argc,
                                    remaining_argv,
                                    opt_keyring_migration_source,
                                    opt_keyring_migration_destination,
                                    opt_keyring_migration_user,
                                    opt_keyring_migration_host,
                                    opt_keyring_migration_password,
                                    opt_keyring_migration_socket,
                                    opt_keyring_migration_port,
                                    opt_keyring_migration_to_component)) {
                                LogErr(ERROR_LEVEL, ER_KEYRING_MIGRATION_FAILED);
                                log_error_dest = "stderr";
                                flush_error_log_messages();
                                break;
                        }

                        if (mk.execute()) {
                                LogErr(ERROR_LEVEL, ER_KEYRING_MIGRATION_FAILED);
                                log_error_dest = "stderr";
                                flush_error_log_messages();
                                break;
                        }

                        my_getopt_skip_unknown = false;
                        LogErr(INFORMATION_LEVEL, ER_KEYRING_MIGRATION_SUCCESSFUL);
                        log_error_dest = "stderr";
                        flush_error_log_messages();
                        exit_state = MYSQLD_SUCCESS_EXIT;
                        break;
                }
                unireg_abort(exit_state);
        }

        /*
         We have enough space for fiddling with the argv, continue
        */
        if (!(is_help_or_validate_option()) && my_setwd(mysql_real_data_home, MYF(0))) {
                char errbuf[MYSYS_STRERROR_SIZE];

                LogErr(ERROR_LEVEL,
                       ER_CANT_SET_DATA_DIR,
                       mysql_real_data_home,
                       errno,
                       my_strerror(errbuf, sizeof(errbuf), errno));
                unireg_abort(MYSQLD_ABORT_EXIT); /* purecov: inspected */
        }

        /*
         The subsequent calls may take a long time : e.g. innodb log read.
         Thus set the long running service control manager timeout
        */
#if defined(_WIN32)
        if (windows_service) {
                if (setup_service_status_cmd_processed_handle())
                        unireg_abort(MYSQLD_ABORT_EXIT);

                char buf[32];
                snprintf(buf, sizeof(buf), "T %lu", slow_start_timeout);
                Service_status_msg msg(buf);
                send_service_status(msg);
        }
#endif

        /* Determine default TCP port and unix socket name */
        set_ports();

        if (init_server_components())
                unireg_abort(MYSQLD_ABORT_EXIT);

        if (!server_id_supplied)
                LogErr(INFORMATION_LEVEL, ER_WARN_NO_SERVERID_SPECIFIED);

        /*
          Add server_uuid to the sid_map.  This must be done after
          server_uuid has been initialized in init_server_auto_options and
          after the binary log (and sid_map file) has been initialized in
          init_server_components().

          No error message is needed: init_sid_map() prints a message.

          Strictly speaking, this is not currently needed when
          opt_bin_log==0, since the variables that gtid_state->init
          initializes are not currently used in that case.  But we call it
          regardless to avoid possible future bugs if gtid_state ever
          needs to do anything else.
        */
        global_sid_lock->wrlock();
        int gtid_ret = gtid_state->init();
        global_sid_lock->unlock();

        if (gtid_ret)
                unireg_abort(MYSQLD_ABORT_EXIT);

        if (!opt_initialize && !opt_initialize_insecure) {
                // Initialize executed_gtids from mysql.gtid_executed table.
                if (gtid_state->read_gtid_executed_from_table() == -1)
                        unireg_abort(1);
        }

        if (opt_bin_log) {
                /*
                  Initialize GLOBAL.GTID_EXECUTED and GLOBAL.GTID_PURGED from
                  gtid_executed table and binlog files during server startup.
                */
                Gtid_set *executed_gtids        = const_cast<Gtid_set *>(gtid_state->get_executed_gtids());
                Gtid_set *lost_gtids            = const_cast<Gtid_set *>(gtid_state->get_lost_gtids());
                Gtid_set *gtids_only_in_table   = const_cast<Gtid_set *>(gtid_state->get_gtids_only_in_table());
                Gtid_set *previous_gtids_logged = const_cast<Gtid_set *>(gtid_state->get_previous_gtids_logged());

                Gtid_set purged_gtids_from_binlog(global_sid_map, global_sid_lock);
                Gtid_set gtids_in_binlog(global_sid_map, global_sid_lock);
                Gtid_set gtids_in_binlog_not_in_table(global_sid_map, global_sid_lock);

                if (mysql_bin_log.init_gtid_sets(&gtids_in_binlog,
                                                 &purged_gtids_from_binlog,
                                                 opt_source_verify_checksum,
                                                 true /*true=need lock*/,
                                                 nullptr /*trx_parser*/,
                                                 nullptr /*partial_trx*/,
                                                 true /*is_server_starting*/))
                        unireg_abort(MYSQLD_ABORT_EXIT);

                global_sid_lock->wrlock();

                purged_gtids_from_binlog.dbug_print("purged_gtids_from_binlog");
                gtids_in_binlog.dbug_print("gtids_in_binlog");

                if (!gtids_in_binlog.is_empty() && !gtids_in_binlog.is_subset(executed_gtids)) {
                        gtids_in_binlog_not_in_table.add_gtid_set(&gtids_in_binlog);
                        if (!executed_gtids->is_empty())
                                gtids_in_binlog_not_in_table.remove_gtid_set(executed_gtids);
                        /*
                          Save unsaved GTIDs into gtid_executed table, in the
                          following four cases:
                            1. the upgrade case.
                            2. the case that a slave is provisioned from a
                          backup of the master and the slave is cleaned by RESET
                          MASTER and RESET SLAVE before this.
                            3. the case that no binlog rotation happened from
                          the last RESET MASTER on the server before it crashes.
                            4. The set of GTIDs of the last binlog is not saved
                          into the gtid_executed table if server crashes, so we
                          save it into gtid_executed table and executed_gtids
                          during recovery from the crash.
                        */
                        if (gtid_state->save(&gtids_in_binlog_not_in_table) == -1) {
                                global_sid_lock->unlock();
                                unireg_abort(MYSQLD_ABORT_EXIT);
                        }
                        executed_gtids->add_gtid_set(&gtids_in_binlog_not_in_table);
                }

                /* gtids_only_in_table= executed_gtids - gtids_in_binlog */
                if (gtids_only_in_table->add_gtid_set(executed_gtids) != RETURN_STATUS_OK) {
                        global_sid_lock->unlock();
                        unireg_abort(MYSQLD_ABORT_EXIT);
                }
                gtids_only_in_table->remove_gtid_set(&gtids_in_binlog);
                /*
                  lost_gtids = executed_gtids -
                               (gtids_in_binlog - purged_gtids_from_binlog)
                             = gtids_only_in_table + purged_gtids_from_binlog;
                */
                assert(lost_gtids->is_empty());
                if (lost_gtids->add_gtid_set(gtids_only_in_table) != RETURN_STATUS_OK ||
                    lost_gtids->add_gtid_set(&purged_gtids_from_binlog) != RETURN_STATUS_OK) {
                        global_sid_lock->unlock();
                        unireg_abort(MYSQLD_ABORT_EXIT);
                }

                /* Prepare previous_gtids_logged for next binlog */
                if (previous_gtids_logged->add_gtid_set(&gtids_in_binlog) != RETURN_STATUS_OK) {
                        global_sid_lock->unlock();
                        unireg_abort(MYSQLD_ABORT_EXIT);
                }

                /*
                  Write the previous set of gtids at this point because during
                  the creation of the binary log this is not done as we cannot
                  move the init_gtid_sets() to a place before opening the binary
                  log. This requires some investigation.

                  /Alfranio
                */
                Previous_gtids_log_event prev_gtids_ev(&gtids_in_binlog);

                global_sid_lock->unlock();

                (prev_gtids_ev.common_footer)->checksum_alg = static_cast<enum_binlog_checksum_alg>(binlog_checksum_options);

                if (mysql_bin_log.write_event_to_binlog_and_sync(&prev_gtids_ev))
                        unireg_abort(MYSQLD_ABORT_EXIT);

                // run auto purge member function. It will evaluate auto purge
                // controls and configuration, calculate which log files are to
                // be purged, and if any file is to be purged, it will purge it.
                // This is all encapsulated insiude the auto purge member
                // function.
                //
                // Note on the DBUG_EVALUATE_IF usage below:
                // - when compiling it out: the condition evaluates to true,
                // thus
                //   mysql_bin_log.auto_purge_at_server_startup() runs
                // - when "expire_logs_always_at_start" is set: evaluates to
                // false,
                //   thus mysql_bin_log.purge_logs_before_date() runs
                // - when "expire_logs_always_at_start" is not set: evaluates to
                // true,
                //   this mysql_bin_log.auto_purge_at_server_startup() runs
                if (DBUG_EVALUATE_IF("expire_logs_always_at_start", false, true))
                        mysql_bin_log.auto_purge_at_server_startup();
                else if (expire_logs_days > 0 || binlog_expire_logs_seconds > 0)
                        mysql_bin_log.purge_logs_before_date(time(nullptr), true);

                (void)RUN_HOOK(server_state, after_engine_recovery, (nullptr));
        }

        if (init_ssl_communication())
                unireg_abort(MYSQLD_ABORT_EXIT);
        if (network_init())
                unireg_abort(MYSQLD_ABORT_EXIT);

#ifdef _WIN32
        if (opt_require_secure_transport && !opt_enable_shared_memory && !have_ssl() && !opt_initialize) {
                LogErr(ERROR_LEVEL, ER_TRANSPORTS_WHAT_TRANSPORTS);
                unireg_abort(MYSQLD_ABORT_EXIT);
        }
#endif

        /*
         Initialize my_str_malloc(), my_str_realloc() and my_str_free()
        */
        my_str_malloc  = &my_str_malloc_mysqld;
        my_str_free    = &my_str_free_mysqld;
        my_str_realloc = &my_str_realloc_mysqld;

        error_handler_hook = my_message_sql;

        bool abort = false;

        /* Save pid of this process in a file */
        if (!opt_initialize) {
                if (create_pid_file())
                        abort = true;
        }

        /* Read the optimizer cost model configuration tables */
        if (!opt_initialize)
                reload_optimizer_cost_constants();

        if (
                /*
                  Read components table to restore previously installed
                  components. This requires read access to mysql.component
                  table.
                */
                (!opt_initialize && mysql_component_infrastructure_init()) || mysql_rm_tmp_tables()) {
                abort = true;
        }

        /* we do want to exit if there are any other unknown options */
        if (remaining_argc > 1) {
                int              ho_error;
                struct my_option no_opts[] = {
                        { nullptr, 0, nullptr, nullptr, nullptr, nullptr, GET_NO_ARG, NO_ARG, 0, 0, 0, nullptr, 0, nullptr }
                };
                /*
                  We need to eat any 'loose' arguments first before we conclude
                  that there are unprocessed options.
                */
                my_getopt_skip_unknown = false;

                if ((ho_error = handle_options(&remaining_argc, &remaining_argv, no_opts, mysqld_get_one_option)))
                        abort = true;
                else {
                        /* Add back the program name handle_options removes */
                        remaining_argc++;
                        remaining_argv--;
                        my_getopt_skip_unknown = true;

                        if (remaining_argc > 1) {
                                LogErr(ERROR_LEVEL, ER_EXCESS_ARGUMENTS, remaining_argv[1]);
                                LogErr(INFORMATION_LEVEL, ER_VERBOSE_HINT);
                                abort = true;
                        }
                }
        }

        if (abort || acl_init(opt_noacl)) {
                if (!abort)
                        LogErr(ERROR_LEVEL, ER_PRIVILEGE_SYSTEM_INIT_FAILED);
                abort     = true;
                opt_noacl = true;
        }

        /*
         if running with --initialize, explicitly allocate the memory
         to be used by ACL objects.
        */
        if (opt_initialize)
                init_acl_memory();

        if (abort || my_tz_init((THD *)nullptr, default_tz_name, opt_initialize) || grant_init(opt_noacl)) {
                set_connection_events_loop_aborted(true);

                delete_pid_file(MYF(MY_WME));

                unireg_abort(MYSQLD_ABORT_EXIT);
        }

        /*
          Bootstrap the dynamic privilege service implementation
        */
        if (dynamic_privilege_init()) {
                LogErr(WARNING_LEVEL, ER_PERSISTENT_PRIVILEGES_BOOTSTRAP);
        }

        /*
          In the case of upgrade, the bootstrap thread would have already
          initialized the structures necessary for federated server from
          mysql.servers table. Hence we need not initialize them again here.
        */
        if (!opt_initialize && (dd::upgrade::no_server_upgrade_required() || opt_upgrade_mode == UPGRADE_MINIMAL))
                servers_init(nullptr);

        if (!opt_noacl) {
                udf_read_functions_table();
        }

        init_status_vars();
        /* If running with --initialize, do not start replication. */
        if (opt_initialize)
                opt_skip_replica_start = true;

        check_binlog_cache_size(nullptr);
        check_binlog_stmt_cache_size(nullptr);

        binlog_unsafe_map_init();

        ReplicaInitializer replica_initializer(
                opt_initialize, opt_skip_replica_start, rpl_channel_filters, &opt_replica_skip_errors);

#ifdef WITH_LOCK_ORDER
        if (!opt_initialize) {
                LO_activate();
        }
#endif /* WITH_LOCK_ORDER */

#ifdef WITH_PERFSCHEMA_STORAGE_ENGINE
        initialize_performance_schema_acl(opt_initialize);
#endif /* WITH_PERFSCHEMA_STORAGE_ENGINE */

        initialize_information_schema_acl();

        (void)RUN_HOOK(server_state, after_recovery, (nullptr));

        if (Events::init(opt_noacl || opt_initialize))
                unireg_abort(MYSQLD_ABORT_EXIT);

#ifndef _WIN32
        //  Start signal handler thread.
        start_signal_handler();
#endif
        if (opt_authentication_policy && validate_authentication_policy(opt_authentication_policy)) {
                /* --authentication_policy is set to invalid value */
                LogErr(ERROR_LEVEL, ER_INVALID_AUTHENTICATION_POLICY);
                return 1;
        } else {
                /* update the value */
                update_authentication_policy();
        }
        /* set all persistent options */
        if (persisted_variables_cache.set_persisted_options(false)) {
                LogErr(ERROR_LEVEL, ER_CANT_SET_UP_PERSISTED_VALUES);
                flush_error_log_messages();
                return 1;
        }

        /*
          Invoke the bootstrap thread, if required.
        */
        process_bootstrap();

        /*
          Event must be invoked after error_handler_hook is assigned to
          my_message_sql, otherwise my_message will not cause the event to
          abort.
        */
        void *argv_p = argv;
        if (mysql_audit_notify(AUDIT_EVENT(MYSQL_AUDIT_SERVER_STARTUP_STARTUP), static_cast<const char **>(argv_p), argc))
                unireg_abort(MYSQLD_ABORT_EXIT);

#ifdef _WIN32
        create_shutdown_and_restart_thread();
#endif
        if (mysqld_process_must_end_at_startup) {
#if !defined(_WIN32)
                if (opt_daemonize)
                        mysqld::runtime::signal_parent(pipe_write_fd, 1);
#endif
                unireg_abort(MYSQLD_SUCCESS_EXIT);
        }

        start_handle_manager();

        create_compress_gtid_table_thread();

        LogEvent()
                .type(LOG_TYPE_ERROR)
                .subsys(LOG_SUBSYSTEM_TAG)
                .prio(SYSTEM_LEVEL)
                .lookup(ER_SERVER_STARTUP_MSG,
                        my_progname,
                        server_version,
#ifdef HAVE_SYS_UN_H
                        (opt_initialize ? "" : mysqld_unix_port),
#else
                        "",
#endif
                        mysqld_port,
                        MYSQL_COMPILATION_COMMENT_SERVER);

        if (!opt_disable_networking && my_admin_bind_addr_str)
                LogEvent()
                        .type(LOG_TYPE_ERROR)
                        .subsys(LOG_SUBSYSTEM_TAG)
                        .prio(SYSTEM_LEVEL)
                        .lookup(ER_SERVER_STARTUP_ADMIN_INTERFACE,
                                my_admin_bind_addr_str,
                                mysqld_admin_port,
                                MYSQL_COMPILATION_COMMENT);

#if defined(_WIN32)
        if (windows_service) {
                Service_status_msg s("R");
                send_service_status(s);
        }
#endif

        server_components_initialized();

        /*
          Set opt_super_readonly here because if opt_super_readonly is set
          in get_option, it will create problem while setting up event
          scheduler.
        */
        set_super_read_only_post_init();

        DBUG_PRINT("info", ("Block, listening for incoming connections"));

        (void)MYSQL_SET_STAGE(0, __FILE__, __LINE__);

        server_operational_state = SERVER_OPERATING;
        sysd::notify("READY=1\nSTATUS=Server is operational\nMAIN_PID=", getpid(), "\n");

        (void)RUN_HOOK(server_state, before_handle_connection, (nullptr));

#if defined(_WIN32)
        if (mysqld_socket_acceptor != nullptr)
                mysqld_socket_acceptor->check_and_spawn_admin_connection_handler_thread();
        setup_conn_event_handler_threads();
#else
        mysql_mutex_lock(&LOCK_socket_listener_active);
        // Make it possible for the signal handler to kill the listener.
        socket_listener_active = true;
        mysql_mutex_unlock(&LOCK_socket_listener_active);

        if (opt_daemonize) {
                if (nstdout != nullptr) {
                        // Show the pid on stdout if daemonizing and connected
                        // to tty
                        fprintf(nstdout, "mysqld is running as pid %lu\n", current_pid);
                        fclose(nstdout);
                        nstdout = nullptr;
                }

                mysqld::runtime::signal_parent(pipe_write_fd, 1);
        }

        mysqld_socket_acceptor->check_and_spawn_admin_connection_handler_thread();
        mysqld_socket_acceptor->connection_event_loop();
#endif /* _WIN32 */
        server_operational_state = SERVER_SHUTTING_DOWN;
        sysd::notify("STOPPING=1\nSTATUS=Server shutdown in progress\n");

        DBUG_PRINT("info", ("No longer listening for incoming connections"));

        mysql_audit_notify(
                MYSQL_AUDIT_SERVER_SHUTDOWN_SHUTDOWN, MYSQL_AUDIT_SERVER_SHUTDOWN_REASON_SHUTDOWN, MYSQLD_SUCCESS_EXIT);

        terminate_compress_gtid_table_thread();
        /*
          Save set of GTIDs of the last binlog into gtid_executed table
          on server shutdown.
        */
        if (opt_bin_log)
                if (gtid_state->save_gtids_of_last_binlog_into_table())
                        LogErr(WARNING_LEVEL, ER_CANT_SAVE_GTIDS);

#ifndef _WIN32
        mysql_mutex_lock(&LOCK_socket_listener_active);
        // Notify the signal handler that we have stopped listening for
        // connections.
        socket_listener_active = false;
        mysql_cond_broadcast(&COND_socket_listener_active);
        mysql_mutex_unlock(&LOCK_socket_listener_active);
#endif // !_WIN32

#ifdef HAVE_PSI_THREAD_INTERFACE
        /*
          Disable the main thread instrumentation,
          to avoid recording events during the shutdown.
        */
        PSI_THREAD_CALL(delete_current_thread)();
#endif /* HAVE_PSI_THREAD_INTERFACE */

        DBUG_PRINT("info", ("Waiting for shutdown proceed"));
        int ret = 0;
#ifdef _WIN32
        if (shutdown_restart_thr_handle.handle)
                ret = my_thread_join(&shutdown_restart_thr_handle, NULL);
        shutdown_restart_thr_handle.handle = NULL;
        if (0 != ret)
                LogErr(WARNING_LEVEL, ER_CANT_JOIN_SHUTDOWN_THREAD, "shutdown ", ret);
#else
        if (signal_thread_id.thread != 0)
                ret = my_thread_join(&signal_thread_id, nullptr);
        signal_thread_id.thread = 0;
        if (0 != ret)
                LogErr(WARNING_LEVEL, ER_CANT_JOIN_SHUTDOWN_THREAD, "signal_", ret);
#endif // _WIN32

        clean_up(true);
        mysqld_exit(signal_hand_thr_exit_code);
}


```



#### 检测线程栈的方向
> linux一般都是从低地址向高地址增长，因此该函数一般返回-1
>
> -1 表示线程方向是向下增长
>
> 1 表示线程方向是向上增长
```c
int initialize_stack_direction() {
  int a;
  int retval = ptr_f(&a);
  if (retval == 0) {
    stack_direction = -1;
  } else {
    stack_direction = 1;
  }
  return retval;
}
```

#### 根据不同操作系统，确定可执行文件绝对路径
> 现根据命令行第一段的前缀是/开头的绝对路径还是以~开头的相对路径：argv[0]
>
>
```c
void substitute_progpath(char **argv) {
  if (test_if_hard_path(argv[0])) return;

#if defined(_WIN32)
  if (GetModuleFileName(NULL, my_progpath, sizeof(my_progpath))) {
    my_orig_progname = argv[0];
    argv[0] = my_progpath;
  }
#else
  /* If the path has a directory component, use my_realpath()
     (implicitly relative to cwd) */
  if (strchr(argv[0], FN_LIBCHAR) != nullptr &&
      !my_realpath(my_progpath, argv[0], MYF(0))) {
    my_orig_progname = argv[0];
    argv[0] = my_progpath;
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
      argv[0] = my_progpath;
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


#### 根据环境变量NOTIFY_SOCKET
> 该变量一般没有设置

```c
void notify_connect() {
#ifndef _WIN32
  const char *sockstr = getenv("NOTIFY_SOCKET");
  if (sockstr == nullptr) {
#ifdef WITH_SYSTEMD_DEBUG
    LogErr(WARNING_LEVEL, ER_SYSTEMD_NOTIFY_DEBUG,
           "NOTIFY_SOCKET not set in environment. sd_notify messages "
           "will not be sent!",
           "");
#endif /* WITH_SYSTEMD_DEBUG */
    return;
  }
  size_t sockstrlen = strlen(sockstr);
  size_t sunpathlen = sizeof(sockaddr_un::sun_path) - 1;
  if (sockstrlen > sunpathlen) {
    LogErr(SYSTEM_LEVEL, ER_SYSTEMD_NOTIFY_PATH_TOO_LONG, sockstr, sockstrlen,
           sunpathlen);
    return;
  }
  NotifyGlobals::socket = socket(AF_UNIX, SOCK_DGRAM, 0);

  sockaddr_un addr;
  socklen_t addrlen;
  memset(&addr, 0, sizeof(sockaddr_un));
  addr.sun_family = AF_UNIX;
  if (sockstr[0] != '@') {
    strcpy(addr.sun_path, sockstr);
    addrlen = offsetof(struct sockaddr_un, sun_path) + sockstrlen + 1;
  } else {  // Abstract namespace socket
    addr.sun_path[0] = '\0';
    strncpy(&addr.sun_path[1], sockstr + 1, strlen(sockstr) - 1);
    addrlen = offsetof(struct sockaddr_un, sun_path) + sockstrlen;
  }
  int ret = -1;
  do {
    ret = connect(NotifyGlobals::socket,
                  reinterpret_cast<const sockaddr *>(&addr), addrlen);
  } while (ret == -1 && errno == EINTR);
  if (ret == -1) {
    char errbuf[512];
    LogErr(WARNING_LEVEL, ER_SYSTEMD_NOTIFY_CONNECT_FAILED, sockstr,
           my_strerror(errbuf, sizeof(errbuf) - 1, errno));
    NotifyGlobals::socket = -1;
  }
#endif /* not defined _WIN32 */
}

```

#### sysd::notify("STATUS=Server startup in progress\n");
> 模板函数，将一系列不同类型的参数格式化为字符串
```c
/**
  Takes a variable number of arguments of different type and formats
  them on NotifyGlobals::fmt, and sends result to notification socket.

  @param t current argument to format
  @param ts remaining args parameter pack for recursive call
*/
template <typename T, typename... Ts>
inline void notify(T t [[maybe_unused]], Ts... ts [[maybe_unused]]) {
#ifndef _WIN32
#ifndef WITH_SYSTEMD_DEBUG
  if (NotifyGlobals::socket == -1) {
    return;
  }
#endif /* WITH_SYSTEMD_DEBUG */
  NotifyGlobals::fmt << t;
  notify(ts...);
#endif /* not defined _WIN32 */
}
```



#### calculate_mysql_home_from_my_progname();
> argv[0]=/db/storage/guolicheng/local/mysql_3306/bin/mysqld
> 根据不同系统平台构建MySQL的主目录路径
>
> 读取环境变量：MY_BASEDIR_VERSION
>
> mysql_home_ptr="/db/storage/guolicheng/local/mysql_3306/"
```c
static void calculate_mysql_home_from_my_progname() {
  const std::string runtime_output_directory_addon{
      "/runtime_output_directory/"};
#if defined(_WIN32) || defined(APPLE_XCODE)
  char prg_dev[FN_REFLEN];
  my_path(prg_dev, my_progname, nullptr);

  strcat(prg_dev, "/../");
  cleanup_dirname(mysql_home, prg_dev);

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
      progdir[strlen(progdir) - 1] = '\0';
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



#### pre_initialize_performance_schema()
> 如果支持WITH_PERFSCHEMA_STORAGE_ENGINE，则先预初始化performance_schema
>
> 记录主线程的线程id
>
> 初始化内存类别
>
> 重置全局统计信息

```c
void pre_initialize_performance_schema() {
  record_main_thread_id();

  pfs_initialized = false;

  init_all_builtin_memory_class();

  PFS_table_stat::g_reset_template.reset();
  global_idle_stat.reset();
  global_table_io_stat.reset();
  global_table_lock_stat.reset();
  g_histogram_pico_timers.init();
  global_statements_histogram.reset();

  /*
    There is no automatic cleanup. Please either use:
    - my_thread_end()
    - or PSI_server->delete_current_thread()
    in the instrumented code, to explicitly cleanup the instrumentation.
  */
  THR_PFS = nullptr;
}

```

##### init_all_builtin_memory_class
```c
void
init_all_builtin_memory_class()
{
  init_builtin_memory_class(&builtin_memory_mutex,
                            TABLE_DOC("mutex_instances"));

  init_builtin_memory_class(&builtin_memory_rwlock,
                            TABLE_DOC("rwlock_instances"));

  init_builtin_memory_class(&builtin_memory_cond,
                            TABLE_DOC("cond_instances"));

  init_builtin_memory_class(&builtin_memory_file,
                            TABLE_DOC("file_instances"));

  init_builtin_memory_class(&builtin_memory_socket,
                            TABLE_DOC("socket_instances"));

  init_builtin_memory_class(&builtin_memory_mdl,
                            TABLE_DOC("metadata_locks"));

  init_builtin_memory_class(&builtin_memory_file_handle,
                            TABLE_DOC("file_handle"));

  init_builtin_memory_class(&builtin_memory_account,
                            TABLE_DOC("accounts"));

  init_builtin_memory_class(&builtin_memory_account_waits,
                            TABLE_DOC("events_waits_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_stages,
                            TABLE_DOC("events_stages_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_statements,
                            TABLE_DOC("events_statements_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_transactions,
                            TABLE_DOC("events_transactions_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_errors,
                            TABLE_DOC("events_errors_summary_by_account_by_error"));

  init_builtin_memory_class(&builtin_memory_account_memory,
                            TABLE_DOC("memory_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_stages,
                            TABLE_DOC("events_stages_summary_global_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_statements,
                            TABLE_DOC("events_statements_summary_global_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_memory,
                            TABLE_DOC("memory_summary_global_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_errors,
                            TABLE_DOC("events_errors_summary_global_by_error"));

  init_builtin_memory_class(&builtin_memory_host,
                            TABLE_DOC("hosts"));

  init_builtin_memory_class(&builtin_memory_host_waits,
                            TABLE_DOC("events_waits_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_stages,
                            TABLE_DOC("events_stages_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_statements,
                            TABLE_DOC("events_statements_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_transactions,
                            TABLE_DOC("events_transactions_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_errors,
                            TABLE_DOC("events_errors_summary_by_host_by_error"));

  init_builtin_memory_class(&builtin_memory_host_memory,
                            TABLE_DOC("memory_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread,
                            TABLE_DOC("threads"));

  init_builtin_memory_class(&builtin_memory_thread_waits,
                            TABLE_DOC("events_waits_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_stages,
                            TABLE_DOC("events_stages_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_statements,
                            TABLE_DOC("events_statements_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_transactions,
                            TABLE_DOC("events_transactions_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_errors,
                            TABLE_DOC("events_errors_summary_by_thread_by_error"));

  init_builtin_memory_class(&builtin_memory_thread_memory,
                            TABLE_DOC("memory_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_waits_history,
                            TABLE_DOC("events_waits_history"));

  init_builtin_memory_class(&builtin_memory_thread_stages_history,
                            TABLE_DOC("events_stages_history"));

  init_builtin_memory_class(&builtin_memory_thread_statements_history,
                            TABLE_DOC("events_statements_history"));

  init_builtin_memory_class(&builtin_memory_thread_statements_history_tokens,
                            COL_DOC("events_statements_history", "digest_text"));

  init_builtin_memory_class(&builtin_memory_thread_statements_history_sqltext,
                            COL_DOC("events_statements_history", "sql_text"));

  init_builtin_memory_class(&builtin_memory_thread_statements_stack,
                            TABLE_DOC("events_statements_current"));

  init_builtin_memory_class(&builtin_memory_thread_statements_stack_tokens,
                            COL_DOC("events_statements_current", "digest_text"));

  init_builtin_memory_class(&builtin_memory_thread_statements_stack_sqltext,
                            COL_DOC("events_statements_current", "sql_text"));

  init_builtin_memory_class(&builtin_memory_thread_transaction_history,
                            TABLE_DOC("events_transactions_history"));

  init_builtin_memory_class(&builtin_memory_thread_session_connect_attrs,
                            TABLE_DOC("session_connect_attrs"));

  init_builtin_memory_class(&builtin_memory_user,
                            TABLE_DOC("users"));

  init_builtin_memory_class(&builtin_memory_user_waits,
                            TABLE_DOC("events_waits_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_stages,
                            TABLE_DOC("events_stages_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_statements,
                            TABLE_DOC("events_statements_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_transactions,
                            TABLE_DOC("events_transactions_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_errors,
                            TABLE_DOC("events_errors_summary_by_user_by_error"));

  init_builtin_memory_class(&builtin_memory_user_memory,
                            TABLE_DOC("memory_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_mutex_class,
                            GEN_DOC("mutex_class", "mutex instrument classes"));

  init_builtin_memory_class(&builtin_memory_rwlock_class,
                            GEN_DOC("rwlock_class", "rwlock instrument classes"));

  init_builtin_memory_class(&builtin_memory_cond_class,
                            GEN_DOC("cond_class", "cond instrument classes"));

  init_builtin_memory_class(&builtin_memory_thread_class,
                            GEN_DOC("thread_class", "thread instrument classes"));

  init_builtin_memory_class(&builtin_memory_file_class,
                            GEN_DOC("file_class", "file instrument classes"));

  init_builtin_memory_class(&builtin_memory_socket_class,
                            GEN_DOC("socket_class", "socket instrument classes"));

  init_builtin_memory_class(&builtin_memory_stage_class,
                            GEN_DOC("stage_class", "stage instrument classes"));

  init_builtin_memory_class(&builtin_memory_statement_class,
                            GEN_DOC("statement_class", "statement instrument classes"));

  init_builtin_memory_class(&builtin_memory_memory_class,
                            GEN_DOC("memory_class", "memory instrument classes"));

  init_builtin_memory_class(&builtin_memory_setup_actor,
                            TABLE_DOC("setup_actors"));

  init_builtin_memory_class(&builtin_memory_setup_object,
                            TABLE_DOC("setup_objects"));

  init_builtin_memory_class(&builtin_memory_digest,
                            TABLE_DOC("events_statements_summary_by_digest"));

  init_builtin_memory_class(&builtin_memory_digest_tokens,
                            COL_DOC("events_statements_summary_by_digest", "digest_text"));

  init_builtin_memory_class(&builtin_memory_stages_history_long,
                            TABLE_DOC("events_stages_history_long"));

  init_builtin_memory_class(&builtin_memory_statements_history_long,
                            TABLE_DOC("events_statements_history_long"));

  init_builtin_memory_class(&builtin_memory_statements_history_long_tokens,
                            COL_DOC("events_statements_history_long", "digest_text"));

  init_builtin_memory_class(&builtin_memory_statements_history_long_sqltext,
                            COL_DOC("events_statements_history_long", "sql_text"));

  init_builtin_memory_class(&builtin_memory_transactions_history_long,
                            TABLE_DOC("events_transactions_history_long"));

  init_builtin_memory_class(&builtin_memory_waits_history_long,
                            TABLE_DOC("events_waits_history_long"));

  init_builtin_memory_class(&builtin_memory_table,
                            TABLE_DOC("table_handles"));

  init_builtin_memory_class(&builtin_memory_table_share,
                            TABLE_DOC("table_shares"));

  init_builtin_memory_class(&builtin_memory_table_share_index,
                            TABLE_DOC("table_io_waits_summary_by_index_usage"));

  init_builtin_memory_class(&builtin_memory_table_share_lock,
                            TABLE_DOC("table_lock_waits_summary_by_table"));

  init_builtin_memory_class(&builtin_memory_program,
                            TABLE_DOC("events_statements_summary_by_program"));

  init_builtin_memory_class(&builtin_memory_prepared_stmt,
                            TABLE_DOC("prepared_statements_instances"));

  init_builtin_memory_class(&builtin_memory_scalable_buffer,
                            GEN_DOC("scalable_buffer", "scalable buffers"));
}
```

##### init_builtin_memory_class

```c
static void init_builtin_memory_class(PFS_builtin_memory_class *klass,
                                      const char *name,
                                      const char *documentation) {
  klass->m_class.m_type = PFS_CLASS_MEMORY;
  klass->m_class.m_enabled = true; /* Immutable */
  klass->m_class.m_timed = false;  /* N/A */
  klass->m_class.m_flags = PSI_FLAG_ONLY_GLOBAL_STAT;
  klass->m_class.m_volatility = PSI_VOLATILITY_PERMANENT;
  klass->m_class.m_documentation = const_cast<char *>(documentation);
  klass->m_class.m_event_name_index = 0;
  klass->m_class.m_name.set(PFS_CLASS_MEMORY, name);
  assert(klass->m_class.m_name.length() <= klass->m_class.m_name.max_length);

  klass->m_stat.reset();
}
```

##### PFS_builtin_memory_class
```c
struct PFS_builtin_memory_class {
  PFS_memory_class m_class;
  PFS_memory_shared_stat m_stat;

  inline void count_alloc(size_t size) { m_stat.count_builtin_alloc(size); }

  inline void count_free(size_t size) { m_stat.count_builtin_free(size); }
};
```

##### PFS_class_type
```c
enum PFS_class_type {
  PFS_CLASS_NONE = 0,
  PFS_CLASS_MUTEX = 1,
  PFS_CLASS_RWLOCK = 2,
  PFS_CLASS_COND = 3,
  PFS_CLASS_FILE = 4,
  PFS_CLASS_TABLE = 5,
  PFS_CLASS_STAGE = 6,
  PFS_CLASS_STATEMENT = 7,
  PFS_CLASS_TRANSACTION = 8,
  PFS_CLASS_SOCKET = 9,
  PFS_CLASS_TABLE_IO = 10,
  PFS_CLASS_TABLE_LOCK = 11,
  PFS_CLASS_IDLE = 12,
  PFS_CLASS_MEMORY = 13,
  PFS_CLASS_METADATA = 14,
  PFS_CLASS_ERROR = 15,
  PFS_CLASS_THREAD = 16,
  PFS_CLASS_LAST = PFS_CLASS_THREAD,
  PFS_CLASS_MAX = PFS_CLASS_LAST + 1
};
```



#### my_init()
> 文件：my_umask = 0640
>
> 目录：my_umask_dir = 0750
>
> 获取环境变量：UMASK、UMASK_DIR


```c
bool my_init() {
  char *str;

  if (my_init_done) return false;

  my_init_done = true;

#if defined(MY_MSCRT_DEBUG)
  set_crt_report_leaks();
#endif

  my_umask = 0640;     /* Default umask for new files */
  my_umask_dir = 0750; /* Default umask for new directories */

  /* Default creation of new files */
  if ((str = getenv("UMASK")) != nullptr)
    my_umask = (int)(atoi_octal(str) | 0600);
  /* Default creation of new dir's */
  if ((str = getenv("UMASK_DIR")) != nullptr)
    my_umask_dir = (int)(atoi_octal(str) | 0700);

  if (my_thread_global_init()) return true;

  if (my_thread_init()) return true;

  /* $HOME is needed early to parse configuration files located in ~/ */
  if ((home_dir = getenv("HOME")) != nullptr)
    home_dir = intern_filename(home_dir_buff, home_dir);

  {
    DBUG_TRACE;
    DBUG_PROCESS(my_progname ? my_progname : "unknown");
#ifdef _WIN32
    my_win_init();
#endif
    MyFileInit();

    DBUG_PRINT("exit", ("home: '%s'", home_dir));
    return false;
  }
} /* my_init */
```

##### my_thread_global_init()
> 用于MySQL的线程全局初始化，确保在多线程环境能正常工作
>
> 互斥锁初始化
>
>
```c
bool my_thread_global_init() {
  if (my_thread_global_init_done) return false;
  my_thread_global_init_done = true;

#if defined(SAFE_MUTEX)
  safe_mutex_global_init(); /* Must be called early */
#endif

#ifdef PTHREAD_ADAPTIVE_MUTEX_INITIALIZER_NP
  /*
    Set mutex type to "fast" a.k.a "adaptive"

    In this case the thread may steal the mutex from some other thread
    that is waiting for the same mutex.  This will save us some
    context switches but may cause a thread to 'starve forever' while
    waiting for the mutex (not likely if the code within the mutex is
    short).
  */
  pthread_mutexattr_init(&my_fast_mutexattr);
  pthread_mutexattr_settype(&my_fast_mutexattr, PTHREAD_MUTEX_ADAPTIVE_NP);
#endif

#ifdef PTHREAD_ERRORCHECK_MUTEX_INITIALIZER_NP
  /*
    Set mutex type to "errorcheck"
  */
  pthread_mutexattr_init(&my_errorcheck_mutexattr);
  pthread_mutexattr_settype(&my_errorcheck_mutexattr, PTHREAD_MUTEX_ERRORCHECK);
#endif

  mysql_mutex_init(key_THR_LOCK_malloc, &THR_LOCK_malloc, MY_MUTEX_INIT_FAST);
  mysql_mutex_init(key_THR_LOCK_open, &THR_LOCK_open, MY_MUTEX_INIT_FAST);
  mysql_mutex_init(key_THR_LOCK_charset, &THR_LOCK_charset, MY_MUTEX_INIT_FAST);
  mysql_mutex_init(key_THR_LOCK_lock, &THR_LOCK_lock, MY_MUTEX_INIT_FAST);
  mysql_mutex_init(key_THR_LOCK_myisam, &THR_LOCK_myisam, MY_MUTEX_INIT_SLOW);
  mysql_mutex_init(key_THR_LOCK_myisam_mmap, &THR_LOCK_myisam_mmap,
                   MY_MUTEX_INIT_FAST);
  mysql_mutex_init(key_THR_LOCK_heap, &THR_LOCK_heap, MY_MUTEX_INIT_FAST);
  mysql_mutex_init(key_THR_LOCK_net, &THR_LOCK_net, MY_MUTEX_INIT_FAST);
#ifndef NDEBUG
  mysql_mutex_init(key_THR_LOCK_threads, &THR_LOCK_threads, MY_MUTEX_INIT_FAST);
  mysql_cond_init(key_THR_COND_threads, &THR_COND_threads);
#endif

  return false;
}
```


##### my_thread_init()
> 初始化MySQL中的下线程
```c
extern "C" bool my_thread_init() {
#ifndef NDEBUG
  struct st_my_thread_var *tmp;
#endif

  if (!my_thread_global_init_done)
    return true; /* cannot proceed with uninitialized library */

#ifdef _WIN32
  install_sigabrt_handler();
#endif

#ifndef NDEBUG
  if (mysys_thread_var()) return false;

  if (!(tmp = (struct st_my_thread_var *)calloc(1, sizeof(*tmp)))) return true;

  mysql_mutex_lock(&THR_LOCK_threads);
  tmp->id = ++thread_id;
  ++THR_thread_count;
  mysql_mutex_unlock(&THR_LOCK_threads);
  set_mysys_thread_var(tmp);
#endif

  return false;
}
```

#### MyFileInit();


```c

```

#### load_defaults


```c
int load_defaults(const char *conf_file, const char **groups, int *argc,
                  char ***argv, MEM_ROOT *alloc) {
  return my_load_defaults(conf_file, groups, argc, argv, alloc,
                          &default_directories);
}
```

##### my_load_defaults

```c
int my_load_defaults(const char *conf_file, const char **groups, int *argc,
                     char ***argv, MEM_ROOT *alloc,
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
  if (*argc >= 2 && !strcmp(argv[0][1], "--no-defaults"))
    no_defaults = found_no_defaults = true;

  group.count = 0;
  group.name = "defaults";
  group.type_names = groups;

  for (; *groups; groups++) group.count++;

  ctx.alloc = alloc;
  ctx.m_args = &my_args;
  ctx.group = &group;

  if ((error = my_search_option_files(conf_file, argc, argv, &args_used,
                                      handle_default_option, (void *)&ctx, dirs,
                                      false, found_no_defaults))) {
    return error;
  }

  if (my_defaults_read_login_file) {
    /* Read options from login group. */
    if (my_default_get_login_file(my_login_file, sizeof(my_login_file)) &&
        (error = my_search_option_files(my_login_file, argc, argv, &args_used,
                                        handle_default_option, (void *)&ctx,
                                        dirs, true, found_no_defaults))) {
      alloc->Clear();
      return error;
    }
  }
  /*
    Here error contains <> 0 only if we have a fully specified conf_file
    or a forced default file
  */
  if (!(ptr = (const char **)alloc->Alloc(
            (my_args.size() + *argc + 1 + args_sep) * sizeof(char *))))
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
    memcpy((uchar *)(res + 1 + my_args.size() + args_sep),
           (char *)((*argv) + 1), (*argc - 1) * sizeof(char *));
  res[my_args.size() + *argc + args_sep] = nullptr; /* last null */

  (*argc) += my_args.size() + args_sep;
  *argv = const_cast<char **>(res);

  if (default_directories) *default_directories = dirs;

  if (found_no_defaults) return 0;

  if (found_print_defaults) {
    int i;
    printf("%s would have been started with the following arguments:\n",
           **argv);
    for (i = 1; i < *argc; i++)
      if (!my_getopt_is_args_separator(
              (*argv)[i])) /* skip arguments separator */
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










