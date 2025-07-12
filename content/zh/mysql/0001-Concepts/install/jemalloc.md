---
title: 源码安装：jemalloc-5.3.0
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 4
---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}


```bash
#!/bin/bash
version='v12'

function install(){
        echo "install ..."
}
wget https://github.com/jemalloc/jemalloc/archive/refs/tags/5.3.0.tar.gz

tar -xzvf 5.3.0.tar.gz

cd jemalloc-5.3.0/

 ./autogen.sh

 ./configure --prefix=/usr/local/jemalloc --libdir=/usr/lib

 make

 make install

ls

# ssddd
```

```python

import os

print("====")

```