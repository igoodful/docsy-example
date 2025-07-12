---
title: Conn.py
description: Conn.py
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}


```python
#!/bin/python3
from fabric import Connection
from invoke import Responder
from datetime import datetime

import dbinit_pb2
import dbinit_pb2_grpc
import grpc
import threading

class BackupConn(object):
    def __init__(self, host: str, user: str, port: str, pswd: str):
        self.host = host
        self.user = user
        self.port = port
        self.pswd = pswd
        self.conn = None

    def execute(self, cmd: str, timeout=10):
        raise NotImplementedError

    def add_watcher(self, pattern=None, input=None):
        pass

    def log_execute_info_once(self):
        pass

    def close(self):
        pass

class SSHConn(BackupConn):
    def __init__(self, host, user, port, pswd):
        super().__init__(host, user, port, pswd)
        self.conn = Connection(host=host, port=port, user=user, connect_timeout=10, connect_kwargs={'password':pswd})
        self.responder = None
        self.log_info = False

    def add_watcher(self, pattern, input: str):
        self.responder = Responder(pattern=pattern, response=input)

    def log_execute_info_once(self):
        self.log_info = True

    def execute(self, cmd: str, timeout=10):
        if self.conn is not None:
            try:
                if self.log_info:
                    thd_id = threading.get_ident();
                    print("[EXEC %d %s] SSHConn start to run: %s" % (thd_id, datetime.now(), cmd), flush=True)
                if self.responder is not None:
                    self.conn.run(cmd, pty=True, watchers=[self.responder])
                    if self.log_info:
                        thd_id = threading.get_ident();
                        print("[EXEC %d %s] SSHConn end to run: %s" % (thd_id, datetime.now(), cmd), flush=True)
                else:
                    ret = self.conn.run(cmd, hide=True)
                    if self.log_info:
                        thd_id = threading.get_ident();
                        print("[EXEC %d %s] SSHConn end to run: %s" % (thd_id, datetime.now(), cmd), flush=True)
                    return [ret.stdout, ret.stderr]
            except Exception as e:
                raise
            finally:
                self.responder = None
                self.log_info = False

    def close(self):
        if self.conn is not None:
            self.conn.close()


class AgentConn(BackupConn):
    def __init__(self, rabbitmq_uri, host, agent_type='rds-dbinit'):
        import client_agent_type
        super().__init__(host, '','','')
        self.api = client_agent_type.API(rabbitmq_uri, agent_type='rds-dbinit')

    def execute(self, cmd, timeout=10):
        return self.api.common_cmd(self.host, cmd, _timeout=timeout)

class DBInitConn(BackupConn):
    def __init__(self, host, user, port):
        super().__init__(host, user, port, '')
        self._channel = grpc.insecure_channel("{0}:{1}".format(self.host, self.port))
        self._stub = dbinit_pb2_grpc.DBInitServiceStub(self._channel)

    def can_conn(self):
        if self._stub is not None:
            try:
                res = self._stub.Ping(dbinit_pb2.PingInfo(User=self.user))
                if not res.Success:
                    return False
            except Exception as e:
                raise
            return True
        return False

    def execute(self, cmd: str, timeout=10):
        if self._stub is not None:
            try:
                response = self._stub.Exec(dbinit_pb2.ExecInfo(Cmd=cmd))
                stdout = response.Stdout
                stderr = response.Stderr
                # exit_code = response.Code
                return stdout, stderr
            except Exception as e:
                raise

    def close(self):
        if self._channel is not None:
            self._channel.close()
        self._channel = None
        self._stub = None


if __name__ == '__main__':
    # conn = SSHConn('127.0.0.1', 'wjm', 22, '123')
    # ret = conn.execute('echo 1')
    # print(ret)

    # conn = AgentConn('rabbit://admin:admin@172.16.50.210/', '172.16.50.210', agent_type='rds-dbinit')
    # ret = conn.execute('echo 1')
    # print(ret)

    # conn = AgentConn('rabbit://admin:admin@172.16.50.60/', '172.16.50.60')
    # ret = conn.execute('cat 1')
    # print(ret)

    conn = DBInitConn('127.0.0.1', 'mdk', 19999)
    ret = conn.execute('echo 1')
    print(ret)


```





