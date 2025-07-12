---
title: 打印日志
description: print log
date: 2025-02-15
weight: 30000
---

```bash
#!/bin/sh
################Script Note########################
#FileName:init.sh
#Function:init db init
################Version Definition####################
VERSION="1.0"                     #version
MODIFIED_TIME="20250215"          #modify time
DEPLOY_UNION="COMMON"             #deploy union
EDITER_MAIL="xxxx.com"            #email
################################################

export LANG=en_US
shell_option="$@"
declare user_name=$(whoami)
declare host_name=$(hostname)
declare run_datetime=$(date "+%Y%m%dT%H%M%S")
declare log_file="/tmp/ticbase_deploy_${run_datetime}.log"
touch ${log_file} 2>/dev/null
chmod ugo+rw "${log_file}" 2>/dev/null
# print_log info/debug/error/warn/succ "a mesaage"
function print_log {
	log_level=$(echo "$1" | tr "a-z" "A-Z")
	log_mesage=$2

	if [ -w "${log_file}" ]; then
		echo "$(date "+%Y%m%dT%H%M%S"):[${log_level}]:${user_name}@${host_name}:${2}" | tee -a ${log_file}
	else
		echo "$(date "+%Y%m%dT%H%M%S"):[${log_level}]:${user_name}@${host_name}:${2}"
	fi

	[ "${log_level}" = "ERROR" ] && exit 11 || return 0
}
print_log info "a mesaage"


# bash deploy.sh -cluster mycluster -version 5.0 -topology multi-node -I /path/to/id_rsa -P mypassword --SKIPCHECK
# clsname="mycluster"
# clsversion="5.0"
# clstopology="multi-node"
# rootsshid="/path/to/id_rsa"
# rootpswd="mypassword"
#  SKIPCHECK="yes"
for inopt in ${shell_option}; do
	case $(echo $inopt | tr a-z A-Z) in
	-CLUSTER | -CLS)
		CurOpt="-CLUSTER"
		continue
		;;
	-VERSION | -CLSVERSION)
		CurOpt="-VERSION"
		continue
		;;
	-TOPOLOGY | -T)
		CurOpt="-TOPOLOGY"
		continue
		;;
	-ROOTSSHIDFILE | -I)
		CurOpt="-ROOTSSHIDFILE"
		continue
		;;
	-ROOTPSWD | -P)
		CurOpt="-ROOTPSWD"
		continue
		;;
	-SKIPCHECK | --SKIPCHECK)
		declare SKIPCHECK="yes"
		continue
		;;
	-HELP | --HELP | -H) f_syntax ;;
	-*)
		CurOpt=""
		continue
		;;
	esac
	case "${CurOpt}" in
	-CLUSTER)
		declare clsname="${inopt}"
		continue
		;;
	-VERSION)
		declare clsversion="${inopt}"
		continue
		;;
	-TOPOLOGY)
		declare clstopology="${inopt}"
		continue
		;;
	-ROOTSSHIDFILE)
		declare rootsshid="${inopt}"
		continue
		;;
	-ROOTPSWD)
		declare rootpswd="${inopt}"
		continue
		;;
	esac
done

function run_sql
{
	local connect_str=$1
	local _pswd=$2
	local _stmt=$3
	local runSTMT=$(mysql ${connect_str} ${_pswd} -NBe "${_stmt}")
	_rt_code=$?
	if [ $_rt_code -ne 0 ]; then
		print_log "error" "Run stmt failed, exit"
		exit 1
	else
		echo ${runSTMT}
	fi

}

function pre_check {
	log_level=$(echo "$1" | tr "a-z" "A-Z")
	log_mesage=$2

	if [ "${user_name}" = "mysql" ]; then
                source ~/.bash_profile
		print_log "info" "login user is ${user_name}"
	else
		print_log "error" "please run script with user mysql, exit!!!"
	fi
}

pre_check



```








