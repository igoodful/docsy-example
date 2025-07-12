---
title: prometheus-client
description: 自定义监控采集
date: 2024-10-09
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>





```bash
pip install prometheus-client
pip3 install prometheus-client

```


```python
from prometheus_client.metrics_core import GaugeMetricFamily
from prometheus_client.metrics_core import CounterMetricFamily
from prometheus_client import generate_latest, push_to_gateway, CollectorRegistry





```



