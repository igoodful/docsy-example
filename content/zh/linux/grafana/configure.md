---
title: 配置
description: 配置
date: 2023-11-05
weight: 30000


---
{{< alert >}}
[官网下载地址](https://grafana.com/grafana/download)



{{< /alert >}}






## 默认配置

`cat defaults.ini.bak`
```ini
app_mode = production
instance_name = ${HOSTNAME}
force_migration = false
[paths]

data = data
temp_data_lifetime = 24h
# 日志路径
logs = data/log
# 插件路径
plugins = data/plugins
provisioning = conf/provisioning
[server]
# 访问协议，默认http
protocol = http
min_tls_version = ""
# 监听的ip地址，，默认是0.0.0.0
http_addr =
# 监听的端口，默认是3000
http_port = 3000
# 这个设置是root_url的一部分，当你通过浏览器访问grafana时的公开的domian名称，默认是localhost
domain = localhost
enforce_domain = false
# 这是一个web上访问grafana的全路径url，默认是%(protocol)s://%(domain)s:%(http_port)s/
root_url = %(protocol)s://%(domain)s:%(http_port)s/
serve_from_sub_path = false
# 是否记录web请求日志，默认是false
router_logging = false
# 前端文件（HTML，JS和CSS文件）的目录路径。 默认为public
static_root_path = public
enable_gzip = false
cert_file =
cert_key =
socket_gid = -1
socket_mode = 0660
socket = /tmp/grafana.sock
cdn_url =
read_timeout = 0
[server.custom_response_headers]
[grpc_server]
network = "tcp"
address = "127.0.0.1:10000"
use_tls = false
cert_file =
key_file =


[database]
# 数据库可以是mysql、postgres、sqlite3，默认是sqlite3
type = sqlite3
# 只是mysql、postgres需要，默认是127.0.0.1:3306
host = 127.0.0.1:3306
# grafana的数据库名称，默认是grafana
name = grafana
# 数据库用户名
user = root
# 数据库密码
password =
url =
max_idle_conn = 2
max_open_conn =
conn_max_lifetime = 14400
log_queries =
# mysql、postgres使用,不要开启
ssl_mode = disable
ssl_sni =
isolation_level =
ca_cert_path =
client_key_path =
client_cert_path =
server_cert_name =
# 只是sqlite3需要，定义sqlite3的存储路径
path = grafana.db
cache_mode = private
wal = false
locking_attempt_timeout_sec = 0
query_retries = 0
transaction_retries = 5
instrument_queries = false

[remote_cache]
type = database
connstr =
prefix =
encryption =

[dataproxy]
logging = false
timeout = 30
dialTimeout = 10
keep_alive_seconds = 30
tls_handshake_timeout_seconds = 10
expect_continue_timeout_seconds = 1
max_conns_per_host = 0
max_idle_connections = 100
idle_conn_timeout_seconds = 90
send_user_header = false
response_limit = 0
row_limit = 1000000
user_agent =

[analytics]
reporting_enabled = true
reporting_distributor = grafana-labs
check_for_updates = true
check_for_plugin_updates = true
google_analytics_ua_id =
google_analytics_4_id =
google_analytics_4_send_manual_page_views = false
google_tag_manager_id =
rudderstack_write_key =
rudderstack_data_plane_url =
rudderstack_sdk_url =
rudderstack_config_url =
rudderstack_integrations_url =
intercom_secret =
application_insights_connection_string =
application_insights_endpoint_url =
feedback_links_enabled = true


[security]
disable_initial_admin_creation = false
admin_user = admin
admin_password = admin
admin_email = admin@localhost
secret_key = SW2YcwTIb9zpOOhoPsMm
encryption_provider = secretKey.v1
available_encryption_providers =
disable_gravatar = false
data_source_proxy_whitelist =
disable_brute_force_login_protection = false
cookie_secure = false
cookie_samesite = lax
# 页面嵌入，建议开启
allow_embedding = false
strict_transport_security = false
strict_transport_security_max_age_seconds = 86400
strict_transport_security_preload = false
strict_transport_security_subdomains = false
x_content_type_options = true
x_xss_protection = true
content_security_policy = false
content_security_policy_template = """script-src 'self' 'unsafe-eval' 'unsafe-inline' 'strict-dynamic' $NONCE;object-src 'none';font-src 'self';style-src 'self' 'unsafe-inline' blob:;img-src * data:;base-uri 'self';connect-src 'self' grafana.com ws://$ROOT_PATH wss://$ROOT_PATH;manifest-src 'self';media-src 'none';form-action 'self';"""
content_security_policy_report_only = false
content_security_policy_report_only_template = """script-src 'self' 'unsafe-eval' 'unsafe-inline' 'strict-dynamic' $NONCE;object-src 'none';font-src 'self';style-src 'self' 'unsafe-inline' blob:;img-src * data:;base-uri 'self';connect-src 'self' grafana.com ws://$ROOT_PATH wss://$ROOT_PATH;manifest-src 'self';media-src 'none';form-action 'self';"""
angular_support_enabled = true
csrf_always_check = false
disable_frontend_sandbox_for_plugins = grafana-incident-app

[security.encryption]
data_keys_cache_ttl = 15m
data_keys_cache_cleanup_interval = 1m


[snapshots]
enabled = true
external_enabled = true
external_snapshot_url = https://snapshots.raintank.io
external_snapshot_name = Publish to snapshots.raintank.io
public_mode = false
snapshot_remove_expired = true

[dashboards]
versions_to_keep = 20
min_refresh_interval = 5s
default_home_dashboard_path =

[datasources]
datasource_limit = 5000
concurrent_query_count = 10

[sql_datasources]
max_open_conns_default = 100
max_idle_conns_default = 100
max_conn_lifetime_default = 14400

[users]
# 是否允许普通用户登录，如果设置为false，则禁止用户登录，默认是true，则admin可以创建用户，并登录grafana
allow_sign_up = false
# 如果设置为false，则禁止用户创建新组织，默认是true
allow_org_create = false
# 当设置为true的时候，会自动的把新增用户增加到id为1的组织中，当设置为false的时候，新建用户的时候会新增一个组织
auto_assign_org = true
auto_assign_org_id = 1
auto_assign_org_role = Viewer
verify_email_enabled = false
# 首页user框中的背景文字
login_hint = email or username
password_hint = password
# 默认页面的背景：light，dark
default_theme = dark
default_language = en-US
home_page =
external_manage_link_url =
external_manage_link_name =
external_manage_info =
viewers_can_edit = false
editors_can_admin = false
user_invite_max_lifetime_duration = 24h
hidden_users =

[secretscan]
enabled = false
interval = 5m
base_url = https://secret-scanning.grafana.net
oncall_url =
revoke = true

[service_accounts]
token_expiration_day_limit =

[auth]
login_cookie_name = grafana_session
disable_login = false
login_maximum_inactive_lifetime_duration =
login_maximum_lifetime_duration =
token_rotation_interval_minutes = 10
disable_login_form = false
disable_signout_menu = false
signout_redirect_url =
oauth_auto_login = false
oauth_state_cookie_max_age = 600
oauth_skip_org_role_update_sync = false
api_key_max_seconds_to_live = -1
sigv4_auth_enabled = false
sigv4_verbose_logging = false
azure_auth_enabled = false
oauth_allow_insecure_email_lookup = false
id_response_header_enabled = false
id_response_header_prefix = X-Grafana
id_response_header_namespaces = user api-key service-account

[sso_settings]
reload_interval = 1m
configurable_providers = github gitlab google generic_oauth azuread okta

[auth.anonymous]
enabled = false
org_name = Main Org.
org_role = Viewer
hide_version = false
device_limit =

[auth.github]
name = GitHub
icon = github
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_id
client_secret =
scopes = user:email,read:org
auth_url = https://github.com/login/oauth/authorize
token_url = https://github.com/login/oauth/access_token
api_url = https://api.github.com/user
signout_redirect_url =
allowed_domains =
team_ids =
allowed_organizations =
role_attribute_path =
role_attribute_strict = false
allow_assign_grafana_admin = false
skip_org_role_sync = false
tls_skip_verify_insecure = false
tls_client_cert =
tls_client_key =
tls_client_ca =
use_refresh_token = false

[auth.gitlab]
name = GitLab
icon = gitlab
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_id
client_secret =
scopes = openid email profile
auth_url = https://gitlab.com/oauth/authorize
token_url = https://gitlab.com/oauth/token
api_url = https://gitlab.com/api/v4
signout_redirect_url =
allowed_domains =
allowed_groups =
role_attribute_path =
role_attribute_strict = false
allow_assign_grafana_admin = false
skip_org_role_sync = false
tls_skip_verify_insecure = false
tls_client_cert =
tls_client_key =
tls_client_ca =
use_pkce = true
use_refresh_token = true

[auth.google]
name = Google
icon = google
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_client_id
client_secret =
scopes = openid email profile
auth_url = https://accounts.google.com/o/oauth2/v2/auth
token_url = https://oauth2.googleapis.com/token
api_url = https://openidconnect.googleapis.com/v1/userinfo
signout_redirect_url =
allowed_domains =
validate_hd = false
hosted_domain =
allowed_groups =
role_attribute_path =
role_attribute_strict = false
allow_assign_grafana_admin = false
skip_org_role_sync = true
tls_skip_verify_insecure = false
tls_client_cert =
tls_client_key =
tls_client_ca =
use_pkce = true
use_refresh_token = true

[auth.grafananet]
enabled = false
allow_sign_up = true
client_id = some_id
client_secret =
scopes = user:email
allowed_organizations =
use_refresh_token = false

[auth.grafana_com]
name = Grafana.com
icon = grafana
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_id
client_secret =
scopes = user:email
allowed_organizations =
skip_org_role_sync = false
use_refresh_token = false

[auth.azuread]
name = Microsoft
icon = microsoft
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_client_id
client_secret =
scopes = openid email profile
auth_url = https://login.microsoftonline.com/<tenant-id>/oauth2/v2.0/authorize
token_url = https://login.microsoftonline.com/<tenant-id>/oauth2/v2.0/token
signout_redirect_url =
allowed_domains =
allowed_groups =
allowed_organizations =
role_attribute_strict = false
allow_assign_grafana_admin = false
force_use_graph_api = false
tls_skip_verify_insecure = false
tls_client_cert =
tls_client_key =
tls_client_ca =
use_pkce = true
skip_org_role_sync = false
use_refresh_token = true

[auth.okta]
name = Okta
icon = okta
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_id
client_secret =
scopes = openid profile email groups
auth_url = https://<tenant-id>.okta.com/oauth2/v1/authorize
token_url = https://<tenant-id>.okta.com/oauth2/v1/token
api_url = https://<tenant-id>.okta.com/oauth2/v1/userinfo
signout_redirect_url =
allowed_domains =
allowed_groups =
role_attribute_path =
role_attribute_strict = false
allow_assign_grafana_admin = false
skip_org_role_sync = false
tls_skip_verify_insecure = false
tls_client_cert =
tls_client_key =
tls_client_ca =
use_pkce = true
use_refresh_token = false

[auth.generic_oauth]
name = OAuth
icon = signin
enabled = false
allow_sign_up = true
auto_login = false
client_id = some_id
client_secret =
scopes = user:email
empty_scopes = false
email_attribute_name = email:primary
email_attribute_path =
login_attribute_path =
name_attribute_path =
role_attribute_path =
role_attribute_strict = false
groups_attribute_path =
id_token_attribute_name =
team_ids_attribute_path =
auth_url =
token_url =
api_url =
signout_redirect_url =
teams_url =
allowed_domains =
allowed_groups =
team_ids =
allowed_organizations =
tls_skip_verify_insecure = false
tls_client_cert =
tls_client_key =
tls_client_ca =
use_pkce = false
auth_style =
allow_assign_grafana_admin = false
skip_org_role_sync = false
use_refresh_token = false

[auth.basic]
enabled = true
password_policy = false

[auth.proxy]
enabled = false
header_name = X-WEBAUTH-USER
header_property = username
auto_sign_up = true
sync_ttl = 60
whitelist =
headers =
headers_encoded = false
enable_login_token = false

[auth.jwt]
enabled = false
enable_login_token = false
header_name =
email_claim =
username_claim =
jwk_set_url =
jwk_set_file =
cache_ttl = 60m
expect_claims = {}
key_file =
key_id =
role_attribute_path =
role_attribute_strict = false
groups_attribute_path =
auto_sign_up = false
url_login = false
allow_assign_grafana_admin = false
skip_org_role_sync = false
signout_redirect_url =

[auth.ldap]
enabled = false
config_file = /etc/grafana/ldap.toml
allow_sign_up = true
skip_org_role_sync = false
sync_cron = "0 1 * * *"
active_sync_enabled = true

[aws]
allowed_auth_providers = default,keys,credentials
assume_role_enabled = true
list_metrics_page_limit = 500
external_id =
session_duration = "15m"
forward_settings_to_plugins = cloudwatch, grafana-athena-datasource, grafana-redshift-datasource, grafana-x-ray-datasource, grafana-timestream-datasource, grafana-iot-sitewise-datasource, grafana-iot-twinmaker-app, grafana-opensearch-datasource, aws-datasource-provisioner, elasticsearch, prometheus

[azure]
cloud = AzureCloud
managed_identity_enabled = false
managed_identity_client_id =
workload_identity_enabled = false
workload_identity_tenant_id =
workload_identity_client_id =
workload_identity_token_file =
user_identity_enabled = false
user_identity_token_url =
user_identity_client_id =
user_identity_client_secret =
forward_settings_to_plugins = grafana-azure-monitor-datasource, prometheus, grafana-azure-data-explorer-datasource, mssql

[rbac]
permission_cache = true
reset_basic_roles = false
permission_validation_enabled = true

# 邮件服务器设置
[smtp]
# 是否开启
enabled = false
# ip和端口
host = localhost:25
user =
password =
cert_file =
key_file =
skip_verify = false
# 发送邮箱名
from_address = admin@grafana.localhost
# 发送人
from_name = Grafana
ehlo_identity =
startTLS_policy =
enable_tracing = false

[smtp.static_headers]
[emails]
welcome_email_on_sign_up = false
templates_pattern = emails/*.html, emails/*.txt
content_types = text/html

[log]
# 可以是console、file、syslog，默认是console、file
mode = console file
# 日志级别
level = info
filters =
user_facing_default_error = "please inspect Grafana server log for details"

[log.console]
level =
format = console

[log.file]
level =
# 日志格式，支持console、text、json
format = text
# 是否开启自动轮转
log_rotate = true
# 单个日志文件的最大行数，默认是1000000
max_lines = 1000000
# 单个日志文件的最大大小，默认是28，表示256MB
max_size_shift = 28
# 每天是否进行日志轮转，默认是true
daily_rotate = true
# 日志过期时间，默认是7,7天后删除
max_days = 7

[log.syslog]
level =
format = text
network =
address =
facility =
tag =

[log.frontend]
enabled = false
custom_endpoint =
log_endpoint_requests_per_second_limit = 3
log_endpoint_burst_limit = 15
instrumentations_errors_enabled = true
instrumentations_console_enabled = false
instrumentations_webvitals_enabled = false
internal_logger_level = 0
api_key =

[quota]
enabled = false
org_user = 10
org_dashboard = 100
org_data_source = 10
org_api_key = 10
org_alert_rule = 100
user_org = 10
global_user = -1
global_org = -1
global_dashboard = -1
global_api_key = -1
global_session = -1
global_alert_rule = -1
global_file = 1000
global_correlations = -1
alerting_rule_group_rules = 100

[unified_alerting]
enabled =
disabled_orgs =
admin_config_poll_interval = 60s
alertmanager_config_poll_interval = 60s
ha_redis_address =
ha_redis_username =
ha_redis_password =
ha_redis_db =
ha_redis_prefix =
ha_redis_peer_name =
ha_redis_max_conns = 5
ha_listen_address = "0.0.0.0:9094"
ha_advertise_address = ""
ha_peers = ""
ha_peer_timeout = 15s
ha_label =
ha_gossip_interval = 200ms
ha_push_pull_interval = 60s
execute_alerts = true
evaluation_timeout = 30s
max_attempts = 1
min_interval = 10s
max_state_save_concurrency = 1
state_periodic_save_interval = 5m
disable_jitter = false

[unified_alerting.screenshots]
capture = false
capture_timeout = 10s
max_concurrent_screenshots = 5
upload_external_image_storage = false

[unified_alerting.reserved_labels]
disabled_labels =

[unified_alerting.state_history]
enabled = true
backend =
primary =
secondaries =
loki_remote_url =
loki_remote_read_url =
loki_remote_write_url =
loki_tenant_id =
loki_basic_auth_username =
loki_basic_auth_password =
[unified_alerting.state_history.external_labels]
[unified_alerting.upgrade]
clean_upgrade = false
[remote.alertmanager]
enabled = false
url =
tenant =
password =
sync_interval = 5m

[alerting]
enabled =
execute_alerts = true
error_or_timeout = alerting
nodata_or_nullvalues = no_data
concurrent_render_limit = 5
evaluation_timeout_seconds = 30
notification_timeout_seconds = 30
max_attempts = 3
min_interval_seconds = 1
max_annotation_age =
max_annotations_to_keep =
[annotations]
cleanupjob_batchsize = 100
tags_length = 500
[annotations.dashboard]
max_age =
max_annotations_to_keep =
[annotations.api]
max_age =
max_annotations_to_keep =
[explore]
enabled = true
[help]
enabled = true
[profile]
enabled = true
[news]
news_feed_enabled = true
[query]
concurrent_query_limit =
[query_history]
enabled = true
[metrics]
enabled              = true
interval_seconds     = 10
disable_total_stats = false
total_stats_collector_interval_seconds = 1800
basic_auth_username =
basic_auth_password =
[metrics.environment_info]
[metrics.graphite]
address =
prefix = prod.grafana.%(instance_name)s.
[grafana_net]
url = https://grafana.com
[grafana_com]
url = https://grafana.com
api_url = https://grafana.com/api
[tracing.jaeger]
address =
always_included_tag =
sampler_type = const
sampler_param = 1
sampling_server_url =
zipkin_propagation = false
disable_shared_zipkin_spans = false
[tracing.opentelemetry]
custom_attributes =
sampler_type =
sampler_param =
sampling_server_url =
[tracing.opentelemetry.jaeger]
address =
propagation =
[tracing.opentelemetry.otlp]
address =
propagation =
[external_image_storage]
provider =
[external_image_storage.s3]
endpoint =
path_style_access =
bucket_url =
bucket =
region =
path =
access_key =
secret_key =
[external_image_storage.webdav]
url =
username =
password =
public_url =
[external_image_storage.gcs]
key_file =
bucket =
path =
enable_signed_urls = false
signed_url_expiration =
[external_image_storage.azure_blob]
account_name =
account_key =
container_name =
sas_token_expiration_days =
[external_image_storage.local]
[rendering]
server_url =
callback_url =
renderer_token = -
concurrent_render_request_limit = 30
render_key_lifetime = 5m
[panels]
enable_alpha = false
disable_sanitize_html = false
[plugins]
enable_alpha = false
app_tls_skip_verify_insecure = false
allow_loading_unsigned_plugins =
plugin_admin_enabled = true
plugin_admin_external_manage_enabled = false
plugin_catalog_url = https://grafana.com/grafana/plugins/
plugin_catalog_hidden_plugins =
log_backend_requests = false
public_key_retrieval_disabled = false
public_key_retrieval_on_startup = false
disable_plugins =
install_token =
hide_angular_deprecation =
forward_host_env_vars =
[live]
max_connections = 100
allowed_origins =
ha_engine =
ha_engine_address = "127.0.0.1:6379"
ha_engine_password = ""
[plugin.grafana-image-renderer]
rendering_timezone =
rendering_language =
rendering_viewport_device_scale_factor =
rendering_ignore_https_errors =
rendering_verbose_logging =
rendering_dumpio =
rendering_timing_metrics =
rendering_args =
rendering_chrome_bin =
rendering_mode =
rendering_clustering_mode =
rendering_clustering_max_concurrency =
rendering_clustering_timeout =
rendering_viewport_max_width =
rendering_viewport_max_height =
rendering_viewport_max_device_scale_factor =
grpc_host =
grpc_port =
[enterprise]
license_path =
[feature_toggles]
enable =
[date_formats]
full_date = YYYY-MM-DD HH:mm:ss
interval_second = HH:mm:ss
interval_minute = HH:mm
interval_hour = MM/DD HH:mm
interval_day = MM/DD
interval_month = YYYY-MM
interval_year = YYYY
use_browser_locale = false
default_timezone = browser
[expressions]
enabled = true
[geomap]
default_baselayer_config =
enable_custom_baselayers = true
[support_bundles]
enabled = true
server_admin_only = true
public_keys = ""
[storage]
allow_unsanitized_svg_upload = false
[search]
dashboard_loading_batch_size = 200
full_reindex_interval = 5m
index_update_interval = 10s
[navigation.app_sections]
[navigation.app_standalone_pages]
[secure_socks_datasource_proxy]
enabled = false
root_ca_cert =
client_key =
client_cert =
server_name =
proxy_address =
show_ui = true
allow_insecure = false
[feature_management]
allow_editing = false
update_webhook =
update_webhook_token =
hidden_toggles =
read_only_toggles =
[public_dashboards]
enabled = true

```



## 配置mysql数据库

- 创建用户和数据库
```sql
create database grafana default character set utf8mb4;
create user grafana@'%' identified with mysql_native_password by 'grafana';
grant all on grafana.* to grafana@'%' with grant option;
```

- 修改配置
```ini
[database]
type = mysql
host = 127.0.0.1:3306
name = grafana
user = grafana
password = grafana
```










