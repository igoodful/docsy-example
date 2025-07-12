---
title: adm_install.sh
description: adm_install.sh
date: 2023-10-12
weight: 60000


---

{{< alert >}}


{{< /alert >}}





```bash
#! /usr/bin/env bash

# 部署平台的主机ip，多个主机用空格隔开 示例：(IP1 IP2 IP3)
# 主机ip不能是 127.0.0.1 或 localhost
ADM_IP_ARRAY=()
ADM_WEB_PORT=80

ADM_PACKAGE_VERSION=GreatADM-CDBC-6.0.2-RC-3-7e4b4f16

# 主机连接信息，如果用户不是root，需要是sudo免密的用户，sudo免密配置示例：
# vi /etc/sudoers
# add
# admin   ALL=(ALL)   NOPASSWD:ALL
SSH_USER=mysql
SSH_PORT=22
SSH_AUTH=QWer12#$              # password or pubkey
SSH_PASSWORD=abc123            # password for passowrd auth, when use pubkey auth, is passphrase
SSH_KEY_FILE=/root/.ssh/id_rsa # pubkey auth

# ADM 元数据数据库地址. 格式: <IP>:<PORT>
# ip地址不能是 127.0.0.1 或 localhost.
# 示例: 172.16.50.210:13336
# 如果不提供，将会使用sqlite
# !!! 高可用部署，必须指定数据库信息，不能使用sqlite
# !!! Don't use '' or "" quotation marks, password may contains special char.
ADM_METADB_HOST=172.17.130.48:3333
ADM_METADB_USER=admin
ADM_METADB_PASSWORD=123456
ADM_METADB_DBNAME=adm77

DAS_METADB_HOST=$ADM_METADB_HOST
DAS_METADB_USER=$ADM_METADB_USER
DAS_METADB_PASSWORD=$ADM_METADB_PASSWORD
DAS_METADB_DBNAME=das77

# 目标主机上面安装
DOCKER_DATA_PATH=/adm/docker

# 指定数据库软件包，用来安装时同步到平台。
# 不指定时，会自动find ./ -name "GreatDBPackage-*.tar.*" 查找数据库软件包
DB_PACKAGE_PATH=""

# 平台日志、监控等数据存放目录
ADM_BASE_DIR=/adm/cdbc

# 默认用户名、密码，仅在第一次安装时使用
FIRST_SUPERUSER=adm@greatdb.com
FIRST_SUPERUSER_PASSWORD=Q!w2e3r4

# 邮件服务器配置、使用有监控告警时必须配置
EMAILS_FROM_EMAIL=igoodful@qq.com
SMTP_HOST=smtp.qq.com
SMTP_PASSWORD=123456
SMTP_PORT=25
SMTP_TLS=true
SMTP_USER=igoodful@qq.com

# 平台资源限制
ADM_CPU=16
ADM_MEMORY=20G
DAS_CPU=4

# 监控数据保留日期配置 默认32d
MONITOR_DATA_MAXIMUM_TIME=32d

# 监控数据大小限制
# Defaults to 2TB. Units supported: MB, GB, TB, PB, EB. Ex: "512MB".
MONITOR_DATA_MAXIMUM_SIZE=2TB

# The ADM can be absolutely unique by specifying the ADM_ID, when multiple sets of ADMs are deployed
# The default is 1 (configurable range 1-3), which can be specified at deployment time
# This configuration ensures that the mysql server_id is unique in multi-equipment room deployment
ADM_ID=1

# 启动的容器名称
ADM_SERVICE_NAME=adm-cdbc

# adm镜像相对地址，不需要修改
ADM_IMAGE=./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar

# 安装时，镜像需要的env文件，不需要动
CONFIG_FILE=greatadm.conf

MACHINE=$(uname -m)

function usage() {
	cat <<eof
Usage:
$0 COMMAND [-v] [-h]
Description:
GreatADM installer / uninstaller

Commands:
  install      install ADM
  upgrade      upgrade ADM
  uninstall    delete ADM and cleanup ADM meta data

Options:
  -v           show verbose messages
  -h           show this help mesages
eof
	exit 1
}

function info() {
	echo -e "\e[1;32m$*\e[0m"
}

function waring() {
	echo -e "\e[1;33m$*\e[0m"
}

function error() {
	echo -e "\e[1;31m$*\e[0m"
	exit 1
}

function verbose() {
	[ "$VERBOSE" = TRUE ] && echo "$*"
}

function _ssh() {
	local user=$1
	local ip=$2
	local cmd=$3

	local quoted_cmd

	if [ "$user" != root ]; then
		quoted_cmd=$(quote "$cmd")
		cmd="sudo -sk <<< $quoted_cmd" #  support multicommands in one line
	fi

	verbose "execute [$cmd] on $ip"

	if [ "$SSH_AUTH" = password ]; then
		sshpass -p "$SSH_PASSWORD" ssh -p "$SSH_PORT" -o StrictHostKeyChecking=no -o PreferredAuthentications=password,publickey "$user"@"$ip" "$cmd"
	elif [ "$SSH_AUTH" = pubkey ]; then
		sshpass -p "$SSH_PASSWORD" ssh -p "$SSH_PORT" -o StrictHostKeyChecking=no -o PreferredAuthentications=publickey,password -i "$SSH_KEY_FILE" "$user"@"$ip" "$cmd"
	else
		error "unsupport auth type [$SSH_AUTH], only password/pubkey is valid"
	fi
}

function _scp() {
	local user=$1
	local ip=$2
	local local_path=$3
	local target_path=$4

	if [ "$SSH_AUTH" = password ]; then
		sshpass -p "$SSH_PASSWORD" scp -r -P "$SSH_PORT" -o StrictHostKeyChecking=no -o PreferredAuthentications=password,publickey "$local_path" "$user"@"$ip":"$target_path"
	elif [ "$SSH_AUTH" = pubkey ]; then
		sshpass -p "$SSH_PASSWORD" scp -r -P "$SSH_PORT" -o StrictHostKeyChecking=no -o PreferredAuthentications=publickey,password -i "$SSH_KEY_FILE" "$local_path" "$user"@"$ip":"$target_path"
	else
		error "unsupport auth type [$SSH_AUTH], only password/pubkey is valid"
	fi
}

function install_docker() {
	local user=$1
	local ip=$2
	info "install docker start on $ip"

	[ ${DOCKER_DATA_PATH: -1} = "/" ] && error "$DOCKER_DATA_PATH error Cannot end with /"

	SYSTEMDDIR=/usr/lib/systemd/system
	SERVICEFILE=docker.service
	DOCKERBIN=/usr/bin/
	SERVICENAME=docker

	FILETARGZ="docker-20.10.8.tgz"
	info "copy ${FILETARGZ} to $ip:/tmp"
	_scp "$user" "$ip" "$FILETARGZ" "/tmp"

	info "mkdir -p ${DOCKER_DATA_PATH} on $ip"
	_ssh "$user" "$ip" "mkdir -p ${DOCKER_DATA_PATH}"

	info "Decompress docker files: tar xvpf /tmp/${FILETARGZ} --strip-components=1 -C ${DOCKERBIN} on $ip"
	_ssh "$user" "$ip" "tar xvpf /tmp/${FILETARGZ} --strip-components=1 -C ${DOCKERBIN}"

	info "docker.service: create docker systemd file"
	_ssh "$user" "$ip" "cat >${SYSTEMDDIR}/${SERVICEFILE} <<EOF
[Unit]
Description=Docker Application Container Engine
Documentation=http://docs.docker.com
#After=network.target docker.socket flannel.service
#Requires=flannel.service

[Service]
Type=notify
#EnvironmentFile=-/run/flannel/docker
#WorkingDirectory=/usr/local/kubernetes/bin/
ExecStart=/usr/bin/dockerd -H tcp://localhost:4243 -H unix:///var/run/docker.sock --selinux-enabled=false --log-opt max-size=100m --log-opt max-file=3 --data-root $DOCKER_DATA_PATH
ExecReload=/bin/kill -s HUP \\\$MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s


[Install]
WantedBy=multi-user.target
EOF"

	_ssh "$user" "$ip" "systemctl daemon-reload"

	_ssh "$user" "$ip" "systemctl status ${SERVICENAME}"

	info "Service restart: ${SERVICENAME} on $ip"
	_ssh "$user" "$ip" "systemctl restart ${SERVICENAME}"

	info "Service status: ${SERVICENAME} on $ip"
	_ssh "$user" "$ip" "systemctl status ${SERVICENAME}"

	info "Service enabled: ${SERVICENAME} on $ip"
	# shellcheck disable=SC2086
	_ssh "$user" "$ip" "systemctl enable ${SERVICENAME}"

	info "docker info on $ip"
	if ! _ssh "$user" "$ip" "docker info"; then
		error "install docker on $ip error"
	fi

	info "done on $ip"
}

function install_sshpass() {
	info "cp sshpass /usr/local/bin/sshpass"
	cp sshpass /usr/local/bin/sshpass

	info "chmod 755 /usr/local/bin/sshpass"
	chmod 755 /usr/local/bin/sshpass
	if ! sshpass -V; then
		error "install sshpass error"
	fi
	info "done"
}

function precheck() {
	info "Checking ${ADM_PACKAGE_VERSION}-${MACHINE}.tar.xz and decompress"

	if ! ls ${ADM_PACKAGE_VERSION}/ok &>/dev/null; then
		if ls ${ADM_PACKAGE_VERSION}-${MACHINE}.tar.xz &>/dev/null; then
			info "Decompress ${ADM_PACKAGE_VERSION}-${MACHINE}.tar.xz"
			tar xvJf ${ADM_PACKAGE_VERSION}-${MACHINE}.tar.xz
			touch ${ADM_PACKAGE_VERSION}/ok
		else
			error "cant found ${ADM_PACKAGE_VERSION}-${MACHINE}.tar.xz"
		fi
	fi
	info "Check ${ADM_PACKAGE_VERSION}/ok is successful"
	info "Chdir to ${ADM_PACKAGE_VERSION}"
	cd ${ADM_PACKAGE_VERSION} || error "cant chdir to ${ADM_PACKAGE_VERSION}"

	[ -f "$ADM_IMAGE" ] || error "ADM_IMAGE $ADM_IMAGE not exist"
	info "Checking Install Images Integrity"
	REPO_TAG=$(tar -xpf "$ADM_IMAGE" manifest.json --warning=no-timestamp -O 2>/dev/null | awk -F'Config":"' '{print $2}' | cut -c 1-12)
	[ -z "$REPO_TAG" ] && error "Get ADM image repo tag info failed" || info "OK! ADM image repo tag is $REPO_TAG"

	local db_host="${ADM_METADB_HOST%:*}"
	local db_port="${ADM_METADB_HOST#*:}"

	info "Checking ADM_IP_ARRAY"
	if [ ${#ADM_IP_ARRAY[@]} -eq 0 ]; then
		# shellcheck disable=SC2153
		error "ADM_IP_ARRAY empty. Please edit this file ADM_IP_ARRAY env"
	fi
	# shellcheck disable=SC2128
	info "Will install on servers: ${ADM_IP_ARRAY}"

	if [ ${#ADM_IP_ARRAY[@]} -gt 1 ]; then
		if [ -z "$ADM_METADB_HOST" ] || [ -z "$ADM_METADB_USER" ] || [ -z "$ADM_METADB_PASSWORD" ] || [ -z "$ADM_METADB_DBNAME" ]; then
			error "ADM_METADB info is empty, and ADM_IP_ARRAY is not single host"
		fi
	fi

	info "Checking sshpass"
	if ! which sshpass &>/dev/null; then
		install_sshpass
	else
		info "sshpass check successful"
	fi

	info "Checking the Firewall Status"
	for ip in "${ADM_IP_ARRAY[@]}"; do
		firewall_status=$(_ssh "$SSH_USER" "$ip" "systemctl is-active firewalld 2>/dev/null")
		if [[ ${firewall_status} == "active" ]]; then
			info "Firewall is active, Stoping the Firewall"
			stop_firewall=$(_ssh "$SSH_USER" "$ip" "systemctl stop firewalld 2>&1")
			if [[ ${stop_firewall} ]]; then
				waring ${stop_firewall}
			else
				info "Stop the Firewall Successful"
			fi
		else
			info "Firewall is not active."
		fi

		firewall_disable_status=$(_ssh "$SSH_USER" "$ip" "systemctl is-enabled firewalld 2>/dev/null")
		if [[ ${firewall_disable_status} == "enabled" ]]; then
			info "Firewall is enabled, Disable the Firewall"
			disable_firewall=$(_ssh "$SSH_USER" "$ip" "systemctl disable firewalld 2>&1")
			waring ${disable_firewall}
		else
			info "Firewall is disabled."
		fi
	done

	info "Checking the Installation Environment"
	for ip in "${ADM_IP_ARRAY[@]}"; do
		if ! _ssh "$SSH_USER" "$ip" "docker info &> /dev/null"; then
			install_docker "$SSH_USER" "$ip"
		else
			info "check docker successful on $ip"
		fi
		_ssh "$SSH_USER" "$ip" "netstat -tunlp | grep -v grep | grep -q ':$ADM_WEB_PORT '" && error "Error! check port $ADM_WEB_PORT failed on $ip"
		_ssh "$SSH_USER" "$ip" "docker ps -a --format '{{.Names}}' | grep -w '^$ADM_SERVICE_NAME$'" &>/dev/null && error "Error! check container name failed on $ADM_SERVICE_NAME, Please delete the container '$ADM_SERVICE_NAME' by 'docker rm -f $ADM_SERVICE_NAME'"
		_ssh "$SSH_USER" "$ip" "systemctl enable docker" &>/dev/null
		_ssh "$SSH_USER" "$ip" "systemctl is-enabled docker|grep disabled" &>/dev/null && error "Error! check docker is not enable, Please systemctl enable docker "
	done

}

function precreate() {
	# shellcheck disable=SC2128
	info "Now need create sub dirs under '$ADM_BASE_DIR' dir on ${ADM_IP_ARRAY}"

	for ip in "${ADM_IP_ARRAY[@]}"; do
		_ssh "$SSH_USER" "$ip" "mkdir -p $ADM_BASE_DIR/{data/download,data/monitor/loki/tmp_rules,data/monitor/loki/rules/fake,data/monitor/prometheus/data,data/monitor/prometheus/rules,data/monitor/alertmanager,logs/} && chown -R 10203:10203 $ADM_BASE_DIR"

		_ssh "$SSH_USER" "$ip" "cat >$ADM_BASE_DIR/$CONFIG_FILE <<EOF
ADM_ADDRESS=$ip
ADM_WEB_PORT=$ADM_WEB_PORT

ADM_METADB_HOST=$ADM_METADB_HOST
ADM_METADB_USER=$ADM_METADB_USER
ADM_METADB_PASSWORD=$ADM_METADB_PASSWORD
ADM_METADB_DBNAME=$ADM_METADB_DBNAME

DAS_METADB_HOST=$DAS_METADB_HOST
DAS_METADB_USER=$DAS_METADB_USER
DAS_METADB_PASSWORD=$DAS_METADB_PASSWORD
DAS_METADB_DBNAME=$DAS_METADB_DBNAME

FIRST_SUPERUSER=$FIRST_SUPERUSER
FIRST_SUPERUSER_PASSWORD=$FIRST_SUPERUSER_PASSWORD

EMAILS_FROM_EMAIL=$EMAILS_FROM_EMAIL
SMTP_HOST=$SMTP_HOST
SMTP_PASSWORD=$SMTP_PASSWORD
SMTP_PORT=$SMTP_PORT
SMTP_TLS=$SMTP_TLS
SMTP_USER=$SMTP_USER

MONITOR_DATA_MAXIMUM_TIME=$MONITOR_DATA_MAXIMUM_TIME
MONITOR_DATA_MAXIMUM_SIZE=$MONITOR_DATA_MAXIMUM_SIZE

ADM_ID=$ADM_ID
MAX_WORKERS=$ADM_CPU
DAS_MAX_WORKERS=$DAS_CPU

EOF"
	done
}

function quote() {
	alias t=$1
	alias t | sed 's/^\(alias \)*t=//'
}

function load_adm_image() {

	local user=$1
	local ip=$2

	local cmd="docker images --format '{{.ID}}' | grep -w $REPO_TAG"

	if _ssh "$user" "$ip" "$cmd"; then
		info "The image '$REPO_TAG' already exists. skip $ip."
	else
		info "load ADM image... $ADM_IMAGE start on $ip, Please wait 5-10 minutes"
		adm_image_name=$(basename "$ADM_IMAGE")
		_scp "$user" "$ip" "$ADM_IMAGE" "/tmp/$adm_image_name"

		local cmd="docker load -i /tmp/$adm_image_name; rm -f /tmp/$adm_image_name; docker images --format '{{.ID}}' | grep -w $REPO_TAG"

		if _ssh "$user" "$ip" "$cmd"; then
			info "load ADM image $ADM_IMAGE succeess on $ip"
		else
			error "load ADM image $ADM_IMAGE failed on $ip"
		fi
	fi
}

function load_db_packages() {

	local user=$1
	local ip=$2
	if [ "X$DB_PACKAGE_PATH" = "X" ]; then
		packages=($(find ../ -name "GreatDBPackage-*.tar.*"))
		if [ ${#packages[@]} -ne 0 ]; then
			for package in "${packages[@]}"; do
				_scp "$user" "$ip" "$package" "$ADM_BASE_DIR/data/download"
			done
			sync_cmd="docker exec -i $ADM_SERVICE_NAME python /app/app/initialize/sync_package.py"

			if _ssh "$user" "$ip" "$sync_cmd"; then
				info "Sync db package success on $ip $ADM_SERVICE_NAME"
			else
				waring "Sync db package failed on $ip $ADM_SERVICE_NAME"
			fi
		else
			waring "Cant found db packages to sync"
		fi

	else
		if _scp "$user" "$ip" "$DB_PACKAGE_PATH" "$ADM_BASE_DIR/data/download"; then
			info "scp $DB_PACKAGE_PATH to $ip:$ADM_BASE_DIR/data/download success"
			sync_cmd="docker exec -i $ADM_SERVICE_NAME python /app/app/initialize/sync_package.py"

			if _ssh "$user" "$ip" "$sync_cmd"; then
				info "Sync db package success on $ip $ADM_SERVICE_NAME"
			else
				waring "Sync db package failed on $ip $ADM_SERVICE_NAME"
			fi
		else
			waring "scp $DB_PACKAGE_PATH to $ip:$ADM_BASE_DIR/data/download failed"
		fi

	fi
}

function trusteeship_node() {
	for ip in "${ADM_IP_ARRAY[@]}"; do
		cmd="docker exec -i $ADM_SERVICE_NAME python /app/app/initialize/trusteeship_node.py ${ip} ${SSH_PORT} $SSH_USER ${SSH_PASSWORD} "
		if _ssh "$SSH_USER" "$ip" "$cmd"; then
			info "trusteeship node success on $ip $ADM_SERVICE_NAME"
		else
			waring "trusteeship node failed on $ip $ADM_SERVICE_NAME"
		fi
	done
}

function init_adm() {
	local user=$1
	local ip=$2
	info "Connecting to the METADB $ADM_METADB_HOST ..."

	cmd="docker rm -f init-adm-data &> /dev/null;
         docker run --rm --name init-adm-data \
         --env-file $ADM_BASE_DIR/$CONFIG_FILE \
         -e ADM_ADDRESS=$ip \
         -v $ADM_BASE_DIR/:/data/ \
         $REPO_TAG \
         python /app/app/initialize/backend_pre_start.py"
	if _ssh "$user" "$ip" "$cmd"; then
		info "The connection ADM METADB:$ADM_METADB_DBNAME is successful on $ADM_METADB_HOST"
	else
		info $cmd
		error "The connection ADM METADB:$ADM_METADB_DBNAME is failed on $ADM_METADB_HOST"
	fi

	cmd="docker rm -f init-das-data &> /dev/null;
         docker run --rm --name init-das-data \
         --env-file $ADM_BASE_DIR/$CONFIG_FILE \
         -e DAS_ADDRESS=$ip \
         -e PYTHONPATH=/das \
         -w /das \
         -v $ADM_BASE_DIR/:/data/ \
         $REPO_TAG \
         python /das/app/initialize/backend_pre_start.py"
	if _ssh "$user" "$ip" "$cmd"; then
		info "The connection DAS METADB:$ADM_METADB_DBNAME is successful on $DAS_METADB_HOST"
	else
		info $cmd
		error "The connection DAS METADB:$ADM_METADB_DBNAME is failed on $DAS_METADB_HOST"
	fi

	cmd="docker rm -f init-adm-database &> /dev/null;
         docker run -i --rm --name init-adm-database \
         --env-file $ADM_BASE_DIR/$CONFIG_FILE \
         -e ADM_ADDRESS=$ip \
         -v $ADM_BASE_DIR/:/data/ \
         $REPO_TAG \
         alembic upgrade head"

	info "Run ADM migrations..."

	if _ssh "$user" "$ip" "$cmd"; then
		info "Migrations ADM success on $ip"
	else
		info $cmd
		error "Migrations ADM failed on $ip"
	fi

	cmd="docker rm -f init-das-database &> /dev/null;
         docker run -i --rm --name init-das-database \
         --env-file $ADM_BASE_DIR/$CONFIG_FILE \
         -e DAS_ADDRESS=$ip \
         -e PYTHONPATH=/das \
         -w /das \
         -v $ADM_BASE_DIR/:/data/ \
         $REPO_TAG \
         alembic upgrade head"

	info "Run DAS migrations..."

	if _ssh "$user" "$ip" "$cmd"; then
		info "Migrations DAS success on $ip"
	else
		info $cmd
		error "Migrations DAS failed on $ip"
	fi

	info "Initialize metadata"
	cmd="docker rm -f init-adm-metadata &> /dev/null;
         docker run -i --rm --name init-adm-metadata \
         --env-file $ADM_BASE_DIR/$CONFIG_FILE \
         -e ADM_ADDRESS=$ip \
         -v $ADM_BASE_DIR/:/data/ \
         $REPO_TAG \
         python /app/app/initialize/initial_data.py"
	if _ssh "$user" "$ip" "$cmd"; then
		info "Initialize success on $ip"
	else
		info $cmd
		error "Initialize failed on $ip"
	fi

}

function start_adm() {
	local user=$1
	local ip=$2

	local cmd

	cmd="docker run -d --read-only --name $ADM_SERVICE_NAME -p ${ADM_WEB_PORT}:8080 --cpu-period 100000 --cpu-quota ${ADM_CPU}00000 --memory=$ADM_MEMORY \
         --env-file $ADM_BASE_DIR/$CONFIG_FILE \
         -e ADM_ADDRESS=$ip \
         -e DAS_ADDRESS=$ip \
         -v $ADM_BASE_DIR/:/data/ \
         --restart on-failure:5 $REPO_TAG"
	info $cmd

	if _ssh "$user" "$ip" "$cmd"; then
		info "Start $ADM_SERVICE_NAME container with image $REPO_TAG succeess on $ip"
	else
		error "Start $ADM_SERVICE_NAME container with image $REPO_TAG failed on $ip"
	fi

	info "waiting... for ADM web console ready, max wait time is 5 min"
	start_time=$(date +%s)
	while true; do
		_ssh "$user" "$ip" "docker ps --format '{{.Names}}' | grep -q -w $ADM_SERVICE_NAME" || {
			error "check $ADM_SERVICE_NAME container failed on $ip"
		}
		curl -s "http://$ip:$ADM_WEB_PORT/" &>/dev/null && {
			info "Start successful on $ip"
			return
		}

		if [ $(($(date +%s) - start_time)) -gt 300 ]; then
			error "wait $ADM_SERVICE_NAME start timeout on $ip"
		else
			sleep 3
		fi
	done
}

function rename_adm_container() {
	local user=$1
	local ip=$2

	local cmd="{ docker ps -a --format '{{.Names}}' | grep -w -q '^$ADM_SERVICE_NAME$' ; } && docker stop $ADM_SERVICE_NAME || true"
	if _ssh "$user" "$ip" "$cmd"; then
		info "stop $ADM_SERVICE_NAME container succeess on $ip"
	else
		error "stop $ADM_SERVICE_NAME container failed on $ip"
	fi

	local cmd="{ docker ps -a --format '{{.Names}}' | grep -w -q '^$ADM_SERVICE_NAME$' ; } && docker rename $ADM_SERVICE_NAME $ADM_SERVICE_NAME-old-$RANDOM || true"
	if _ssh "$user" "$ip" "$cmd"; then
		info "rename '$ADM_SERVICE_NAME' container to '$ADM_SERVICE_NAME-$RANDOM' succeess on $ip"
	else
		error "rename old $ADM_SERVICE_NAME container failed on $ip"
	fi
}

function remove_adm_container() {
	local user=$1
	local ip=$2

	local cmd="{ docker ps -a --format '{{.Names}}' | grep -w -q '^$ADM_SERVICE_NAME$' ; } && docker rm -f $ADM_SERVICE_NAME || true"
	if _ssh "$user" "$ip" "$cmd"; then
		info "remove $ADM_SERVICE_NAME container succeess on $ip"
	else
		error "remove $ADM_SERVICE_NAME container failed on $ip"
	fi

	local cmd="rm -rf $ADM_BASE_DIR"
	if _ssh "$user" "$ip" "$cmd"; then
		info "remove $ADM_SERVICE_NAME log files succeess on $ip. path: $ADM_BASE_DIR"
	else
		error "remove $ADM_SERVICE_NAME log failed on $ip. path: $ADM_BASE_DIR"
	fi
}

function drop_adm_database() {

	local db_host="${ADM_METADB_HOST%:*}"
	local db_port="${ADM_METADB_HOST#*:}"

	if which mysql &>/dev/null; then
		info "mysql command exist, try to drop database"
		for db in $ADM_METADB_DBNAME; do
			info "drop database $db"
			local cmd="mysql --default_auth=mysql_native_password -h$db_host -u$ADM_METADB_USER -P$db_port -p$(quote ${ADM_METADB_PASSWORD}) -Nse 'drop database $db; select sleep(0.5);' &>/dev/null"
			eval ${cmd}
			[ $? -ne 0 ] && error "Unable to drop database $db"
			info "Drop metadb $db succeeded"
		done
	else
		info "mysql command not exist, need to drop database mannually"
	fi
}

function read_script_variables() {
	local script_file="$1" # 脚本文件路径
	shift                  # 移除第一个参数（脚本路径），保留剩余的参数（变量名数组）
	local variables=("$@") # 变量名数组

	# 遍历变量名数组，获取变量对应的值
	for var_name in "${variables[@]}"; do
		# 使用正则表达式匹配变量名和对应的值
		local result=$(grep "^\s*${var_name}=" ${script_file} | grep -Ev "${var_name}=\\\$${var_name}")

		# 在脚本文件内容中查找匹配的行
		if [[ -n "${result}" ]]; then

			eval ${result}

			local v="${!var_name}"
			if [[ ${var_name} != "DB_PACKAGE_PATH" && (-z "${v}" || ${#v[@]} -eq 0) ]]; then
				error "The value of the ${var_name} is empty"
			fi

		else
			error "The value of the ${var_name} is empty"
		fi
	done

	for var_name in "${variables[@]}"; do
		if [ ${var_name} == "ADM_IP_ARRAY" ]; then
			info "ADM_IP_ARRAY=(${ADM_IP_ARRAY[@]})"
		else
			info "${var_name}=${!var_name}"
		fi
	done
}

# 定义函数，执行选择的升级方式
function perform_upgrade() {
	info "选择升级方式:"
	info "1. 读取已有ADM安装脚本中的配置策略"
	info "2. 读取当前ADM安装脚本中的配置策略"

	# 读取用户输入的升级方式选择
	read -p "请输入你的选择:" choice

	case $choice in
	1)
		# 提示用户输入ADM安装配置文件路径
		read -p "请输入已有的ADM安装脚本(例:/adm/adm_install.sh):" script_path

		# 判断输入的脚本路径是否存在
		if [[ ! -f ${script_path} ]]; then
			error "The script${script_path} does not exist"
		fi

		# 需要解析的变量
		var_array=(
			"ADM_IP_ARRAY"
			"ADM_WEB_PORT"
			"SSH_USER"
			"SSH_PORT"
			"SSH_AUTH"
			"SSH_PASSWORD"
			"SSH_KEY_FILE"
			"ADM_METADB_HOST"
			"ADM_METADB_USER"
			"ADM_METADB_PASSWORD"
			"ADM_METADB_DBNAME"
			"DAS_METADB_HOST"
			"DAS_METADB_USER"
			"DAS_METADB_PASSWORD"
			"DAS_METADB_DBNAME"
			"DOCKER_DATA_PATH"
			"DB_PACKAGE_PATH"
			"ADM_BASE_DIR"
			"FIRST_SUPERUSER"
			"FIRST_SUPERUSER_PASSWORD"
			"EMAILS_FROM_EMAIL"
			"SMTP_HOST"
			"SMTP_PASSWORD"
			"SMTP_PORT"
			"SMTP_TLS"
			"SMTP_USER"
			"ADM_CPU"
			"ADM_MEMORY"
			"DAS_CPU"
			"MONITOR_DATA_MAXIMUM_TIME"
			"MONITOR_DATA_MAXIMUM_SIZE"
			"ADM_ID"
			"ADM_SERVICE_NAME"
			"CONFIG_FILE"
		)
		# 调用函数，并传递脚本路径和变量名数组作为参数
		read_script_variables ${script_path} "${var_array[@]}"

		read -p "请确认配置策略，是否需要升级(Y or N):" is_upgrade

		case ${is_upgrade} in
		"Y")
			info "信息验证无误，开始升级"
			;;
		"N")
			error "停止升级"
			;;
		*)
			error "无效的选择"
			;;
		esac
		;;
	2)
		info "读取脚本中新的ADM平台安装策略升级"
		;;
	*)
		error "无效的选择"
		;;
	esac

}

function install() {
	# install start
	precheck
	info "Install Start..."
	precreate

	# load image
	pid_array=()
	for ip in "${ADM_IP_ARRAY[@]}"; do
		load_adm_image "$SSH_USER" "$ip" &
		pid_array+=($!)
	done

	failed_ip_array=()
	for i in "${!ADM_IP_ARRAY[@]}"; do
		wait "${pid_array[i]}" || failed_ip_array+=("${ADM_IP_ARRAY[i]}")
	done

	[ ${#failed_ip_array[@]} -ne 0 ] && error "load image failed on ${failed_ip_array[*]}"

	# init adm
	init_adm "$SSH_USER" "${ADM_IP_ARRAY[0]}"

	# start adm
	pid_array=()
	for ip in "${ADM_IP_ARRAY[@]}"; do
		start_adm "$SSH_USER" "$ip" &
		pid_array+=($!)
	done

	failed_ip_array=()
	for i in "${!ADM_IP_ARRAY[@]}"; do
		wait "${pid_array[i]}" || failed_ip_array+=("${ADM_IP_ARRAY[i]}")
	done

	[ ${#failed_ip_array[@]} -ne 0 ] && error "start image failed on ${failed_ip_array[*]}"
	load_db_packages "$SSH_USER" "${ADM_IP_ARRAY[0]}"
	trusteeship_node

	# init grafana data
	pid_array=()
	for ip in "${ADM_IP_ARRAY[@]}"; do
		init_grafana_data="docker exec -i $ADM_SERVICE_NAME python /app/app/initialize/initial_grafana_data.py"

		if _ssh "$SSH_USER" "$ip" "$init_grafana_data"; then
			info "Initialize grafana_data succeess on $ip $ADM_SERVICE_NAME"
		else
			error "Initialize grafana_data failed on $ip $ADM_SERVICE_NAME"
		fi
		pid_array+=($!)
	done

	info "Please use the the following url ( http://${ADM_IP_ARRAY[0]}:${ADM_WEB_PORT} ) to access adm console  through a browser:"
	info "The default username and password refer to the configuration of FIRST_SUPERUSER and FIRST_SUPERUSER_PASSWORD in adm.conf when you installed"
	info "done"
}

function upgrade() {
	perform_upgrade

	info "Upgrade Start..."
	# remove container
	info "Backup ADM container..."
	pid_array=()
	for ip in "${ADM_IP_ARRAY[@]}"; do
		rename_adm_container "$SSH_USER" "$ip" &
		pid_array+=($!)
	done

	failed_ip_array=()
	for i in "${!ADM_IP_ARRAY[@]}"; do
		wait "${pid_array[i]}" || failed_ip_array+=("${ADM_IP_ARRAY[i]}")
	done

	install

}
function uninstall() {
	# stop adm container
	info "Uninstall start..."
	# remove container
	pid_array=()
	for ip in "${ADM_IP_ARRAY[@]}"; do
		remove_adm_container "$SSH_USER" "$ip" &
		pid_array+=($!)
	done

	failed_ip_array=()
	for i in "${!ADM_IP_ARRAY[@]}"; do
		wait "${pid_array[i]}" || failed_ip_array+=("${ADM_IP_ARRAY[i]}")
	done

	[ ${#failed_ip_array[@]} -ne 0 ] && error "remove adm container failed on ${failed_ip_array[*]}"

	drop_adm_database

	info "done"
}

subcommand=$1
shift
if [ "$subcommand" = "install" ]; then
	verbose "install adm command"
elif [ "$subcommand" = "upgrade" ]; then
	verbose "upgrade adm command"
elif [ "$subcommand" = "uninstall" ]; then
	verbose "uninstall adm command"
else
	echo "unknown subcommand $subcommand"
	usage
fi

while getopts ':hv' OPT; do
	case $OPT in
	v) VERBOSE=TRUE ;;
	h | ?) usage ;;
	esac
done

eval "$subcommand"

```



