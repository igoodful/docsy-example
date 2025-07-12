---
title: logging
description: 日志记录
date: 2024-10-09
weight: 1600
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>





```python
#!/usr/bin/python
# coding:utf-8

import logging

logging.basicConfig(format='%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')

logging.root.setLevel('INFO')


logger = logging.getLogger(__name__)
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")

```



























