---
title: unittest
description: 单元测试
date: 2025-03-16
weight: 1700
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



























