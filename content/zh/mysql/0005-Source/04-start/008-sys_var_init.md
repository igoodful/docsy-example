---
title: 08. sys_var_init
description: sys_var_init
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
8.0.32 sys_var_init
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

## sys_var_init


```c++
int sys_var_init() {
        DBUG_TRACE;

        /* Must be already initialized. */
        assert(system_charset_info != nullptr);

        static_system_variable_hash = new collation_unordered_map<string, sys_var *>(system_charset_info, PSI_INSTRUMENT_ME);

        dynamic_system_variable_hash = new collation_unordered_map<string, sys_var *>(system_charset_info, PSI_INSTRUMENT_ME);

        never_persistable_vars = new collation_unordered_set<string>({PERSIST_ONLY_ADMIN_X509_SUBJECT, PERSISTED_GLOBALS_LOAD},
                                                                     system_charset_info,
                                                                     PSI_INSTRUMENT_ME);

        if (add_static_system_variable_chain(all_sys_vars.first))
                goto error;

        return 0;

error:
        LogErr(ERROR_LEVEL, ER_FAILED_TO_INIT_SYS_VAR);
        return 1;
}


bool add_static_system_variable_chain(sys_var *first) {
        assert(static_system_variable_hash->empty());

        for (sys_var *var = first; var != nullptr; var = var->next) {
                std::string name = to_string(var->name);
                if (!static_system_variable_hash->emplace(name, var).second) {
                        LogErr(ERROR_LEVEL, ER_DUPLICATE_SYS_VAR, var->name.str);
                        for (; first != var; first = first->next)
                                dynamic_system_variable_hash->erase(to_string(first->name));
                        return true;
                }
        }

        return false;
}


```




## collation_unordered_map

```c++
template <class Key, class Value>
class collation_unordered_map
    : public std::
          unordered_map<Key, Value, Collation_hasher, Collation_key_equal, Malloc_allocator<std::pair<const Key, Value>>> {
public:
        collation_unordered_map(const CHARSET_INFO *cs, PSI_memory_key psi_key)
            : std::unordered_map<Key,
                                 Value,
                                 Collation_hasher,
                                 Collation_key_equal,
                                 Malloc_allocator<std::pair<const Key, Value>>>(
                  10,
                  Collation_hasher(cs),
                  Collation_key_equal(cs),
                  Malloc_allocator<>(psi_key)) {
        }
};
```





## sys_var
```c++
class sys_var {
public:
        sys_var *next;
        LEX_CSTRING name;
        sys_var *m_persisted_alias;
        bool m_is_persisted_deprecated;
        enum flag_enum {
                GLOBAL          = 0x0001,
                SESSION         = 0x0002,
                ONLY_SESSION    = 0x0004,
                SCOPE_MASK      = 0x03FF,  // 1023
                READONLY        = 0x0400,  // 1024
                ALLOCATED       = 0x0800,  // 2048
                INVISIBLE       = 0x1000,  // 4096
                TRI_LEVEL       = 0x2000,  // 8192
                NOTPERSIST      = 0x4000,
                HINT_UPDATEABLE = 0x8000,
                PERSIST_AS_READ_ONLY = 0x10000,
                SENSITIVE = 0x20000
        };
        static const int PARSE_EARLY  = 1;
        static const int PARSE_NORMAL = 2;

        enum binlog_status_enum {
                VARIABLE_NOT_IN_BINLOG,
                SESSION_VARIABLE_IN_BINLOG
        } binlog_status;

protected:
        typedef bool (*on_check_function)(sys_var *self, THD *thd, set_var *var);
        typedef bool (*pre_update_function)(sys_var *self, THD *thd, set_var *var);
        typedef bool (*on_update_function)(sys_var *self, THD *thd, enum_var_type type);

        int flags;
        int m_parse_flag;
        const SHOW_TYPE show_val_type;
        my_option option;
        PolyLock *guard;
        ptrdiff_t offset;
        on_check_function on_check;
        pre_update_function pre_update;
        on_update_function on_update;
        const char *const deprecation_substitute;
        bool is_os_charset;
        struct get_opt_arg_source source;
        char user[USERNAME_CHAR_LENGTH + 1];
        char host[HOSTNAME_LENGTH + 1];
        ulonglong timestamp;

public:
        sys_var(sys_var_chain *chain,
                const char *name_arg,
                const char *comment,
                int flag_args,
                ptrdiff_t off,
                int getopt_id,
                enum get_opt_arg_type getopt_arg_type,
                SHOW_TYPE show_val_type_arg,
                longlong def_val,
                PolyLock *lock,
                enum binlog_status_enum binlog_status_arg,
                on_check_function on_check_func,
                on_update_function on_update_func,
                const char *substitute,
                int parse_flag,
                sys_var *persisted_alias     = nullptr,
                bool is_persisted_deprecated = false);

        virtual ~sys_var() = default;

        const char *get_deprecation_substitute() {
                return deprecation_substitute;
        }

        virtual void cleanup() {
        }

        virtual sys_var_pluginvar *cast_pluginvar() {
                return nullptr;
        }

        bool check(THD *thd, set_var *var);
        const uchar *value_ptr(THD *running_thd, THD *target_thd, enum_var_type type, std::string_view keycache_name);
        const uchar *value_ptr(THD *thd, enum_var_type type, std::string_view keycache_name);
        virtual void update_default(longlong new_def_value) {
                option.def_value = new_def_value;
        }
        virtual longlong get_default() {
                return option.def_value;
        }
        virtual longlong get_min_value() {
                return option.min_value;
        }
        virtual ulonglong get_max_value() {
                return option.max_value;
        }

        virtual ulong get_var_type() {
                return (option.var_type & GET_TYPE_MASK);
        }
        virtual void set_arg_source(get_opt_arg_source *) {
        }
        virtual void set_is_plugin(bool) {
        }
        virtual enum_variable_source get_source() {
                return source.m_source;
        }
        virtual const char *get_source_name() {
                return source.m_path_name;
        }
        virtual void set_source(enum_variable_source src) {
                option.arg_source->m_source = src;
        }
        virtual bool set_source_name(const char *path) {
                return set_and_truncate(option.arg_source->m_path_name, path, sizeof(option.arg_source->m_path_name));
        }
        virtual bool set_user(const char *usr) {
                return set_and_truncate(user, usr, sizeof(user));
        }
        virtual const char *get_user() {
                return user;
        }
        virtual const char *get_host() {
                return host;
        }
        virtual bool set_host(const char *hst) {
                return set_and_truncate(host, hst, sizeof(host));
        }
        virtual ulonglong get_timestamp() const {
                return timestamp;
        }
        virtual void set_user_host(THD *thd);
        my_option *get_option() {
                return &option;
        }
        virtual void set_timestamp() {
                timestamp = my_micro_time();
        }
        virtual void set_timestamp(ulonglong ts) {
                timestamp = ts;
        }
        virtual bool is_non_persistent() {
                return flags & NOTPERSIST;
        }

        bool set_default(THD *thd, set_var *var);
        bool update(THD *thd, set_var *var);
        virtual void saved_value_to_string(THD *thd, set_var *var, char *def_val) = 0;

        SHOW_TYPE show_type() {
                return show_val_type;
        }
        int scope() const {
                return flags & SCOPE_MASK;
        }
        const CHARSET_INFO *charset(THD *thd);
        bool is_readonly() const {
                return flags & READONLY;
        }
        bool not_visible() const {
                return flags & INVISIBLE;
        }
        bool is_trilevel() const {
                return flags & TRI_LEVEL;
        }
        bool is_persist_readonly() const {
                return flags & PERSIST_AS_READ_ONLY;
        }
        bool is_parse_early() const {
                return (m_parse_flag == PARSE_EARLY);
        }
        bool is_sensitive() const {
                return flags & SENSITIVE;
        }

        bool is_hint_updateable() const {
                return flags & HINT_UPDATEABLE;
        }

        bool is_struct() {
                return option.var_type & GET_ASK_ADDR;
        }

        bool is_written_to_binlog(enum_var_type type) {
                return type != OPT_GLOBAL && binlog_status == SESSION_VARIABLE_IN_BINLOG;
        }
        virtual bool check_update_type(Item_result type) = 0;

        bool check_scope(enum_var_type query_type) {
                switch (query_type) {
                        case OPT_PERSIST:
                        case OPT_PERSIST_ONLY:
                        case OPT_GLOBAL:
                                return scope() & (GLOBAL | SESSION);
                        case OPT_SESSION:
                                return scope() & (SESSION | ONLY_SESSION);
                        case OPT_DEFAULT:
                                return scope() & (SESSION | ONLY_SESSION);
                }
                return false;
        }
        bool is_global_persist(enum_var_type type) {
                return (type == OPT_GLOBAL || type == OPT_PERSIST || type == OPT_PERSIST_ONLY);
        }

        bool is_settable_at_command_line() {
                return option.id != -1;
        }

        bool register_option(std::vector<my_option> *array, int parse_flags) {
                return is_settable_at_command_line() && (m_parse_flag & parse_flags) && (array->push_back(option), false);
        }
        void do_deprecated_warning(THD *thd);
        Item *copy_value(THD *thd);

        void save_default(THD *thd, set_var *var) {
                global_save_default(thd, var);
        }

        bool check_if_sensitive_in_context(THD *, bool suppress_errors = true) const;

private:

        inline static bool set_and_truncate(char *dst, const char *string, size_t sizeof_dst) {
                if (dst == string)
                        return false;
                size_t string_length = strlen(string), length;
                length               = std::min(sizeof_dst - 1, string_length);
                memcpy(dst, string, length);
                dst[length] = 0;
                return length < string_length;  // truncated
        }

private:
        virtual bool do_check(THD *thd, set_var *var) = 0;
        virtual void session_save_default(THD *thd, set_var *var) = 0;

        virtual void global_save_default(THD *thd, set_var *var) = 0;
        virtual bool session_update(THD *thd, set_var *var)      = 0;
        virtual bool global_update(THD *thd, set_var *var)       = 0;

protected:

        virtual const uchar *session_value_ptr(THD *running_thd, THD *target_thd, std::string_view keycache_name);
        virtual const uchar *global_value_ptr(THD *thd, std::string_view keycache_name);
        uchar *session_var_ptr(THD *thd);
        uchar *global_var_ptr();

        friend class Sys_var_alias;
};
```






