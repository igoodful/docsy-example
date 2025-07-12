---
title: dbscale-service.sh
description: dbscale-service.sh
date: 2017-01-05
weight: 70000


---

{{< alert >}}

{{< /alert >}}


{{<warning>}}
<!---->

{{</warning>}}



```python
#!/bin/bash

PROG=dbscale
DBSCALE_PATH=/data/app/dbscale
DBSCALE_PID_FILE=$DBSCALE_PATH/dbscale.pid
DBSCALE_PID=`cat $DBSCALE_PID_FILE 2>/dev/null`
START_TIMEOUT=60
STOP_TIMEOUT1=60 # wait enough time to wait dbscale exit, if kill -9 dbscale, dbscale will left some info on zookeeper
STOP_TIMEOUT2=5
CONF_FILE=dbscale.conf
RET=0

force=0

check_pid() {
    [ -z $1 ] && return 1
    [ -d "/proc/$1" ] && return 0 || return 1
}

## get PID file and value ##
get_pid_file_and_value() {
    PID_FILE=$(grep 'pid-file.*=' $CONF_FILE)
    if [ -n "$PID_FILE" ];
    then
        POS=$(expr index "${PID_FILE}" "=")
        PID_FILE=${PID_FILE:$POS}
        PID_FILE=${PID_FILE// /}
        DBSCALE_PID_FILE=$DBSCALE_PATH/$PID_FILE
    fi
    DBSCALE_PID=`cat $DBSCALE_PID_FILE 2>/dev/null`
}

is_dbscale_pid() {
    RET=0
    if [ -n "$1" ]; then
        if [ -d "/proc/$1" ]; then
            TMP_STATE=`cat /proc/$1/comm |grep -c dbscale`
            if [ $TMP_STATE == 1 ]; then
                TMP_DBSCALE_PATH=`ls -alh /proc/$1/cwd |cut -d '>' -f2`
                TMP_DBSCALE_PATH=${TMP_DBSCALE_PATH// /}
                if [ "$TMP_DBSCALE_PATH" = "$DBSCALE_PATH" ]; then
                    TMP_STATE=`cat /proc/$1/cmdline |grep -ac $CONF_FILE`
                    if [ $TMP_STATE == 1 ]; then
                        RET=1
                    fi
                fi
            fi
        fi
    fi
}

if [ -n "$2" ];
then
    if [ "$2" == "-c" ] || [ "$2" == "--config-file" ]; then
        if [ -f "$3" ]; then
            CONF_FILE=$3
            get_pid_file_and_value
            if [ "$1" == "start" ]; then
                is_dbscale_pid $DBSCALE_PID
                if [ $RET == 1 ]; then
                    echo "process $DBSCALE_PID is occupied by dbscale"
                    echo "Start the same configuration file in the same directory,"
                    echo "Or the configuration file configures the same PID file."
                    exit 1
                fi
            fi
        else
            echo "Specify file is not exist, please specify file"
            echo "Please run command -h or --help to get help."
            exit 1
        fi
    else
        echo "Unrecognized option $2"
        echo "Please run command -h or --help to get help"
        exit 1
    fi
else
    get_pid_file_and_value
    if [ "$1" == "start" ]; then
        is_dbscale_pid $DBSCALE_PID
        if [ $RET == 1 ]; then
            echo "process $DBSCALE_PID is occupied by dbscale."
            echo "Start the same configuration file in the same directory,"
            echo "Or the configuration file configures the same PID file."
            exit 1
        fi
    fi
fi

helper() {
    echo "-c [ --config-file ] arg (default=dbscale.conf)      specify the configure file path."
    echo "-h [ --help ]                                        Display this help and exit."
    echo ""
}

fetch_hard_code() {
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DBSCALE_PATH/libs
    cd $DBSCALE_PATH
    ./$PROG -i
}

fetch_version() {
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DBSCALE_PATH/libs
    cd $DBSCALE_PATH
    ./$PROG --version
}

check_libs_ldd() {
    source /etc/profile
    ### check: ldd ###

    which ldd 2>&1 >/dev/null

    if [ $? != 0 ]
    then
        echo "check cmd: ldd"
        exit 1
    fi

    libspath=$DBSCALE_PATH/libs
    echo $LD_LIBRARY_PATH | grep -E "$libspath|\./libs"  2>&1 >/dev/null
    ret1=$?

    ldd dbscale | grep  'not found' 2>&1 >/dev/null
    ret2=$?
    if [ $ret2 == 0 ]
    then
        for i in `ldd dbscale | grep 'not found' | cut -d '=' -f 1 | awk -F '\t' '{print $2}'`
        do
            ls libs_system/$i  2>/dev/null  >/dev/null
            if [ $? == 0 ]
            then
                /usr/bin/cp -af libs_system/$i libs
            else
                echo "file is not exists: $i"
            fi
        done
    fi
}

dbscale_start() {
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DBSCALE_PATH/libs
    cd $DBSCALE_PATH

    if [ -f "$DBSCALE_PATH/libs/libjemalloc.so" ]; then
        export LD_PRELOAD=$DBSCALE_PATH/libs/libjemalloc.so
    elif [ -f "$DBSCALE_PATH/libs/libmimalloc.so" ]; then
        export LD_PRELOAD=$DBSCALE_PATH/libs/libmimalloc.so
    elif [ -f "$DBSCALE_PATH/libs/libtcmalloc.so" ]; then
        export LD_PRELOAD=$DBSCALE_PATH/libs/libtcmalloc.so
    else
        echo "The dbscale's lib doesn't contain libjemalloc.so."
        exit 1
    fi
    echo 10240 > /proc/sys/net/ipv4/tcp_max_syn_backlog
    echo 10240 > /proc/sys/net/core/somaxconn
    ulimit -n 1024000
    ulimit -c unlimited
    check_libs_ldd

# fetch and generate config file from zookeeper
    rm -rf generated_dbscale.conf

# only fetch config from zookeeper if it is a multiple dbscale cluster node
    IS_MUL=$(grep 'multiple-mode.*=.*1' $CONF_FILE)
    if [ -n "$IS_MUL" ]; then
      ./fetch_config_file_from_zoo --config-file $CONF_FILE

      if [ -f "generated_dbscale.conf" ]; then
         filesize=`ls -l generated_dbscale.conf | awk '{ print $5 }'`
         if [ $filesize -gt 100 ];  then
           cp generated_dbscale.conf $CONF_FILE
         fi
      fi
    fi

    ./$PROG --config-file $CONF_FILE &
    DBSCALE_PID=$!
    sleep 1

    start_time=`date '+%s'`
    while true; do
        current_time=`date '+%s'`
        if (($current_time - $start_time > $START_TIMEOUT)); then # start time out
            echo "fail."
            exit 1
        fi
        if ! check_pid $DBSCALE_PID; then
            echo "fail."
            exit 1
        fi
        if [ ! -f "$DBSCALE_PID_FILE" ]; then
            continue
        fi
        if [ "X$DBSCALE_PID" == "X"`cat $DBSCALE_PID_FILE` ]; then
            echo "done."
            return
        fi
    done
}

dbscale_stop() {
    kill -TERM $DBSCALE_PID >/dev/null 2>&1
    start_time=`date '+%s'`
    while true; do
        current_time=`date '+%s'`
        if (($current_time - $start_time > $STOP_TIMEOUT1)); then
            break
        fi
        if ! check_pid $DBSCALE_PID; then
            return
        fi
    done

    kill -KILL $DBSCALE_PID >/dev/null 2>&1
    sleep $STOP_TIMEOUT2
}

if [ ! -d "$DBSCALE_PATH" -o ! -f "$DBSCALE_PATH/$PROG" ]; then
    echo "The path of dbscale is not correct."
    exit 1
fi

case "$1" in
    start)
        is_dbscale_pid $DBSCALE_PID
        if [ $RET != 1 ]; then
            echo "Starting DBScale..."
            dbscale_start
        else
            echo "DBScale has already been running."
        fi
        ;;
    fetch_hard_code)
        fetch_hard_code
        ;;
    fetch_version)
        fetch_version
        ;;
    stop)
        is_dbscale_pid $DBSCALE_PID
        if [ $RET == 1 ]; then
            echo "Stopping DBScale..."
            dbscale_stop
            check_pid $DBSCALE_PID && echo "fail." || echo "done."
        else
            echo "DBScale is not running."
        fi
        ;;
    status)
        is_dbscale_pid $DBSCALE_PID
        if [ $RET == 1 ]; then
            echo "DBScale is running."
            exit 0
        else
            echo "DBScale is not running."
            exit 1
        fi
        ;;
    -h)
        helper
        exit 0
        ;;
    --help)
        helper
        exit 0
        ;;
    *)
        echo "Usage: $0 {start|stop|status}"
        exit 1
esac
exit 0


```


