---
title: client_agent_type.py
description: client_agent_type.py
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
# -*- coding: utf-8 -*-

import oslo_messaging as messaging
from oslo_log import log as logging
from oslo_config import cfg

CONF = cfg.CONF
LOG = logging.getLogger(__name__)


class API(object):

    def __init__(self, default_config_files, agent_type='rds-taskmanager'):
        """rpc 实例化
            agent类型
            rds-taskmanager: 存储数据节点agent
            rds-dbinit: 集群内数据节点agent

        :param default_config_files: RPC 服务器地址
        :type default_config_files: str
        :param agent_type: rds-taskmanager 或者 rds-dbinit
        :type agent_type: str
        """
        self._context = {}
        target = messaging.Target(topic=agent_type, exchange='greatrds')
        transport = messaging.get_rpc_transport(CONF, url=default_config_files)
        self._client = messaging.RPCClient(transport, target)

    def common_cmd(self, server, cmd, _timeout=None, **kwargs):
        """call 同步调用方法, 方法在消息发出后需要等待消息结果返回

        例子:
            api = API('rabbit://admin:admin@127.0.0.1/')
            ret = api.common_cmd('172.16.70.240', cmd='echo ok')

        :param server: 指定RPC服务器IP地址
        :type server: str
        :param cmd: 远程执行命令
        :type cmd: str
        :param _timeout: 执行命令超时时间(秒)
        :type _timeout: int or float
        :return (stdout, stderr)
        """
        kwargs.update({'cmd': cmd})
        cctxt = self._client.prepare(server=server, timeout=_timeout)
        return cctxt.call(ctxt=self._context, method='common_cmd', **kwargs)

    def async_common_cmd(self, server, cmd, **kwargs):
        """cast 方法把消息发出去就算了，不需要返回值；
        """
        kwargs.update({'cmd': cmd})
        cctxt = self._client.prepare(server=server)
        return cctxt.cast(ctxt=self._context, method='common_cmd', **kwargs)

    def echo(self, server):
        cctxt = self._client.prepare(server=server, timeout=5)
        return cctxt.call(ctxt=self._context, method='common_cmd', cmd='echo ok')


if __name__ == '__main__':
    # api = API('rabbit://admin:admin@172.16.70.234/', agent_type='rds-taskmanager')
    # ret = api.echo('172.16.70.111')
    # print(ret)
    api = API('rabbit://admin:123456@172.16.50.210:5672', agent_type='rds-dbinit')
    ret = api.echo('172.16.50.138')
    # print(ret)
    ret = api.common_cmd('172.16.50.210', cmd="echo -e '\taaaaaaaa' > /tmp/1")
    print(ret)
    ret = api.common_cmd('172.16.70.234', cmd='cat /tmp/1')
    print(ret)


```

