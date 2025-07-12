---
title: cmake
description: cmake
date: 2023-11-05
weight: 400


viz: true
---

{{< alert >}}

- [CMake](https://www.bookstack.cn/read/CMake-Cookbook/content-chapter7-7.1-chinese.md)
- [cmake](https://sfumecjf.github.io/cmake-examples-Chinese/01-basic/1.3%20%20Static%20Library.html)
- [cmake](https://zhuanlan.zhihu.com/p/664125090)
- [cmake](https://blog.csdn.net/weixin_43717839/article/details/128032486?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-128032486-blog-128409831.235%5Ev38%5Epc_relevant_anti_vip_base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-128032486-blog-128409831.235%5Ev38%5Epc_relevant_anti_vip_base&utm_relevant_index=9)
- [cmake](https://mp.weixin.qq.com/s?__biz=MzU4NDY3MTg1OA==&mid=2247484057&idx=2&sn=d5e5c65aaacfe8cbe3348342976cd9f1&chksm=fc3c3c8cd3e8ba9df5949ee0ade8975adb2261bd22cac1c391ea5de582e7b11b5f8027e05301&scene=132&exptype=timeline_recommend_article_extendread_samebiz&show_related_article=1&subscene=0&scene=132#wechat_redirect)
- [cmake-examples](https://github.com/ttroy50/cmake-examples/tree/master)


{{< /alert >}}


- CmakeLists.txt才是cmake的正统文件，而.cmake文件是一个模块文件


 ```viz-dot
digraph "zlib" {
node [
  fontsize = "12"
];
    rankdir="LR";
    "node1" [ label = "源文件", shape = octagon ];
    "node2" [ label = "CMakeLists.txt", shape = octagon ];
    "node3" [ label = "CMake", shape = septagon ];
    "node4" [ label = "Makefile", shape = septagon ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = septagon ];
    "node6" [ label = "gcc/g++", shape = septagon ];
    "node7" [ label = "exe/lib", shape = septagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```
