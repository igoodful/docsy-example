---
title: ctr
description: ctr
date: 2025-06-02
weight: 500
viz: true
---


<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $




## 简介


#### `ctr plugins ls`


```bash
# export CONTAINERD_ADDRESS=/glc/containerd/tmp/containerd.sock
# ctr plugins ls
TYPE                                      ID                       PLATFORMS      STATUS    
io.containerd.content.v1                  content                  -              ok        
io.containerd.image-verifier.v1           bindir                   -              ok        
io.containerd.internal.v1                 opt                      -              ok        
io.containerd.warning.v1                  deprecations             -              ok        
io.containerd.snapshotter.v1              blockfile                linux/amd64    skip      
io.containerd.snapshotter.v1              btrfs                    linux/amd64    skip      
io.containerd.snapshotter.v1              devmapper                linux/amd64    skip      
io.containerd.snapshotter.v1              erofs                    linux/amd64    skip      
io.containerd.snapshotter.v1              native                   linux/amd64    ok        
io.containerd.snapshotter.v1              overlayfs                linux/amd64    ok        
io.containerd.snapshotter.v1              zfs                      linux/amd64    skip      
io.containerd.event.v1                    exchange                 -              ok        
io.containerd.monitor.task.v1             cgroups                  linux/amd64    ok        
io.containerd.metadata.v1                 bolt                     -              ok        
io.containerd.gc.v1                       scheduler                -              ok        
io.containerd.differ.v1                   erofs                    -              skip      
io.containerd.differ.v1                   walking                  linux/amd64    ok        
io.containerd.lease.v1                    manager                  -              ok        
io.containerd.service.v1                  containers-service       -              ok        
io.containerd.service.v1                  content-service          -              ok        
io.containerd.service.v1                  diff-service             -              ok        
io.containerd.service.v1                  images-service           -              ok        
io.containerd.service.v1                  introspection-service    -              ok        
io.containerd.service.v1                  namespaces-service       -              ok        
io.containerd.service.v1                  snapshots-service        -              ok        
io.containerd.shim.v1                     manager                  -              ok        
io.containerd.runtime.v2                  task                     linux/amd64    ok        
io.containerd.service.v1                  tasks-service            -              ok        
io.containerd.grpc.v1                     containers               -              ok        
io.containerd.grpc.v1                     content                  -              ok        
io.containerd.grpc.v1                     diff                     -              ok        
io.containerd.grpc.v1                     events                   -              ok        
io.containerd.grpc.v1                     images                   -              ok        
io.containerd.grpc.v1                     introspection            -              ok        
io.containerd.grpc.v1                     leases                   -              ok        
io.containerd.grpc.v1                     namespaces               -              ok        
io.containerd.sandbox.store.v1            local                    -              ok        
io.containerd.transfer.v1                 local                    -              ok        
io.containerd.cri.v1                      images                   -              ok        
io.containerd.cri.v1                      runtime                  linux/amd64    ok        
io.containerd.podsandbox.controller.v1    podsandbox               -              ok        
io.containerd.sandbox.controller.v1       shim                     -              ok        
io.containerd.grpc.v1                     sandbox-controllers      -              ok        
io.containerd.grpc.v1                     sandboxes                -              ok        
io.containerd.grpc.v1                     snapshots                -              ok        
io.containerd.streaming.v1                manager                  -              ok        
io.containerd.grpc.v1                     streaming                -              ok        
io.containerd.grpc.v1                     tasks                    -              ok        
io.containerd.grpc.v1                     transfer                 -              ok        
io.containerd.grpc.v1                     version                  -              ok        
io.containerd.monitor.container.v1        restart                  -              ok        
io.containerd.tracing.processor.v1        otlp                     -              skip      
io.containerd.internal.v1                 tracing                  -              skip      
io.containerd.ttrpc.v1                    otelttrpc                -              ok        
io.containerd.grpc.v1                     healthcheck              -              ok        
io.containerd.nri.v1                      nri                      -              ok        
io.containerd.grpc.v1                     cri                      -              ok        

# ctr --address /glc/containerd/tmp/containerd.sock plugins ls
TYPE                                      ID                       PLATFORMS      STATUS    
io.containerd.content.v1                  content                  -              ok        
io.containerd.image-verifier.v1           bindir                   -              ok        
io.containerd.internal.v1                 opt                      -              ok        
io.containerd.warning.v1                  deprecations             -              ok        
io.containerd.snapshotter.v1              blockfile                linux/amd64    skip      
io.containerd.snapshotter.v1              btrfs                    linux/amd64    skip      
io.containerd.snapshotter.v1              devmapper                linux/amd64    skip      
io.containerd.snapshotter.v1              erofs                    linux/amd64    skip      
io.containerd.snapshotter.v1              native                   linux/amd64    ok        
io.containerd.snapshotter.v1              overlayfs                linux/amd64    ok        
io.containerd.snapshotter.v1              zfs                      linux/amd64    skip      
io.containerd.event.v1                    exchange                 -              ok        
io.containerd.monitor.task.v1             cgroups                  linux/amd64    ok        
io.containerd.metadata.v1                 bolt                     -              ok        
io.containerd.gc.v1                       scheduler                -              ok        
io.containerd.differ.v1                   erofs                    -              skip      
io.containerd.differ.v1                   walking                  linux/amd64    ok        
io.containerd.lease.v1                    manager                  -              ok        
io.containerd.service.v1                  containers-service       -              ok        
io.containerd.service.v1                  content-service          -              ok        
io.containerd.service.v1                  diff-service             -              ok        
io.containerd.service.v1                  images-service           -              ok        
io.containerd.service.v1                  introspection-service    -              ok        
io.containerd.service.v1                  namespaces-service       -              ok        
io.containerd.service.v1                  snapshots-service        -              ok        
io.containerd.shim.v1                     manager                  -              ok        
io.containerd.runtime.v2                  task                     linux/amd64    ok        
io.containerd.service.v1                  tasks-service            -              ok        
io.containerd.grpc.v1                     containers               -              ok        
io.containerd.grpc.v1                     content                  -              ok        
io.containerd.grpc.v1                     diff                     -              ok        
io.containerd.grpc.v1                     events                   -              ok        
io.containerd.grpc.v1                     images                   -              ok        
io.containerd.grpc.v1                     introspection            -              ok        
io.containerd.grpc.v1                     leases                   -              ok        
io.containerd.grpc.v1                     namespaces               -              ok        
io.containerd.sandbox.store.v1            local                    -              ok        
io.containerd.transfer.v1                 local                    -              ok        
io.containerd.cri.v1                      images                   -              ok        
io.containerd.cri.v1                      runtime                  linux/amd64    ok        
io.containerd.podsandbox.controller.v1    podsandbox               -              ok        
io.containerd.sandbox.controller.v1       shim                     -              ok        
io.containerd.grpc.v1                     sandbox-controllers      -              ok        
io.containerd.grpc.v1                     sandboxes                -              ok        
io.containerd.grpc.v1                     snapshots                -              ok        
io.containerd.streaming.v1                manager                  -              ok        
io.containerd.grpc.v1                     streaming                -              ok        
io.containerd.grpc.v1                     tasks                    -              ok        
io.containerd.grpc.v1                     transfer                 -              ok        
io.containerd.grpc.v1                     version                  -              ok        
io.containerd.monitor.container.v1        restart                  -              ok        
io.containerd.tracing.processor.v1        otlp                     -              skip      
io.containerd.internal.v1                 tracing                  -              skip      
io.containerd.ttrpc.v1                    otelttrpc                -              ok        
io.containerd.grpc.v1                     healthcheck              -              ok        
io.containerd.nri.v1                      nri                      -              ok        
io.containerd.grpc.v1                     cri                      -              ok        

```



#### 子系统配置
