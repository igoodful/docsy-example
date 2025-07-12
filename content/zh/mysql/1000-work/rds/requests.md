---
title: Cannot uninstall 'requests'
date: 2017-01-05
description: >
  海迪SQL是一款面向Web开发人员的功能强大且可靠的工具.


---

{{< alert >}}


{{< /alert >}}

 ### ERROR: Cannot uninstall 'requests'

```sql
TASK [os : install Docker Python SDK (required by Ansible Docker modules, e.g. docker_container)] ****************************************************************************************************************
fatal: [controller-2]: FAILED! => changed=false
  cmd:
  - /bin/pip2
  - install
  - --no-index
  - --find-links=/data/tmp/ansible-dep-wheels-py2/docker-py
  - docker
  msg: |-
    stdout: Looking in links: /data/tmp/ansible-dep-wheels-py2/docker-py
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/docker-4.4.1-py2.py3-none-any.whl
    Requirement already satisfied: six>=1.4.0 in /usr/lib/python2.7/site-packages (from docker) (1.9.0)
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/requests-2.25.1-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/websocket_client-0.57.0-py2.py3-none-any.whl
    Requirement already satisfied: backports.ssl-match-hostname>=3.5; python_version < "3.5" in /usr/lib/python2.7/site-packages (from docker) (3.5.0.1)
    Requirement already satisfied: ipaddress>=1.0.16; python_version < "3.3" in /usr/lib/python2.7/site-packages (from docker) (1.0.16)
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/chardet-4.0.0-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/urllib3-1.26.2-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/idna-2.10-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/certifi-2020.12.5-py2.py3-none-any.whl
    Installing collected packages: chardet, urllib3, idna, certifi, requests, websocket-client, docker
      Attempting uninstall: chardet
        Found existing installation: chardet 2.2.1
        Uninstalling chardet-2.2.1:
          Successfully uninstalled chardet-2.2.1
      Attempting uninstall: urllib3
        Found existing installation: urllib3 1.10.2
        Uninstalling urllib3-1.10.2:
          Successfully uninstalled urllib3-1.10.2
      Attempting uninstall: idna
        Found existing installation: idna 2.4
        Uninstalling idna-2.4:
          Successfully uninstalled idna-2.4
      Attempting uninstall: requests
        Found existing installation: requests 2.6.0

    :stderr: DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.
    ERROR: Cannot uninstall 'requests'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
fatal: [controller-1]: FAILED! => changed=false
  cmd:
  - /bin/pip2
  - install
  - --no-index
  - --find-links=/data/tmp/ansible-dep-wheels-py2/docker-py
  - docker
  msg: |-
    stdout: Looking in links: /data/tmp/ansible-dep-wheels-py2/docker-py
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/docker-4.4.1-py2.py3-none-any.whl
    Requirement already satisfied: six>=1.4.0 in /usr/lib/python2.7/site-packages (from docker) (1.9.0)
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/requests-2.25.1-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/websocket_client-0.57.0-py2.py3-none-any.whl
    Requirement already satisfied: backports.ssl-match-hostname>=3.5; python_version < "3.5" in /usr/lib/python2.7/site-packages (from docker) (3.5.0.1)
    Requirement already satisfied: ipaddress>=1.0.16; python_version < "3.3" in /usr/lib/python2.7/site-packages (from docker) (1.0.16)
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/chardet-4.0.0-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/urllib3-1.26.2-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/idna-2.10-py2.py3-none-any.whl
    Processing /data/tmp/ansible-dep-wheels-py2/docker-py/certifi-2020.12.5-py2.py3-none-any.whl
    Installing collected packages: chardet, urllib3, idna, certifi, requests, websocket-client, docker
      Attempting uninstall: chardet
        Found existing installation: chardet 2.2.1
        Uninstalling chardet-2.2.1:
          Successfully uninstalled chardet-2.2.1
      Attempting uninstall: urllib3
        Found existing installation: urllib3 1.10.2
        Uninstalling urllib3-1.10.2:
          Successfully uninstalled urllib3-1.10.2
      Attempting uninstall: idna
        Found existing installation: idna 2.4
        Uninstalling idna-2.4:
          Successfully uninstalled idna-2.4
      Attempting uninstall: requests
        Found existing installation: requests 2.6.0

    :stderr: DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.
    ERROR: Cannot uninstall 'requests'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.

PLAY RECAP ***************************************************************************************************
controller-1               : ok=20   changed=6    unreachable=0    failed=1    skipped=5    rescued=0    ignored=0
controller-2               : ok=20   changed=6    unreachable=0    failed=1    skipped=5    rescued=0    ignored=0



```












