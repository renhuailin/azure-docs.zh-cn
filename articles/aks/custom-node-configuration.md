---
title: 自定义 Azure Kubernetes 服务 (AKS) 节点池的节点配置（预览版）
description: 了解如何自定义 Azure Kubernetes 服务 (AKS) 群集节点和节点池的配置。
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c872848791e2de3591c8e3e94d4aa5744c7bbcdc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656372"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>自定义 Azure Kubernetes 服务 (AKS) 节点池的节点配置（预览版）

通过自定义节点配置，你可以配置或优化操作系统 (OS) 设置或 kubelet 参数，以满足工作负荷的需求。 创建 AKS 群集或向群集添加节点池时，你可以自定义一个常用 OS 和 kubelet 设置的子集。 若要配置此子集之外的设置，请[使用守护程序集自定义所需配置，而不会失去对节点的 AKS 支持](support-policies.md#shared-responsibility)。

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>注册 `CustomNodeConfigPreview` 预览版功能

若要创建可自定义 kubelet 参数或 OS 设置的 AKS 群集或节点池，必须在订阅中启用 `CustomNodeConfigPreview` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `CustomNodeConfigPreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建可自定义 kubelet 参数或 OS 设置的 AKS 群集或节点池，你需要最新的 aks-preview Azure CLI 扩展。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>使用自定义节点配置

### <a name="kubelet-custom-configuration"></a>Kubelet 自定义配置

下面列出了支持的 Kubelet 参数和接受的值。

| 参数 | 允许的值/间隔 | 默认 | 说明 |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | 无、静态 | 无 | 静态策略允许 CPU 请求数为整数的 [Guaranteed Pod](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) 中的容器访问节点上的独占 CPU。 |
| `cpuCfsQuota` | true、false | true |  为指定 CPU 限制的容器启用/禁用 CPU CFS 配额强制。 | 
| `cpuCfsQuotaPeriod` | 以毫秒为单位的间隔（毫秒） | `100ms` | 设置 CPU CFS 配额周期值。 | 
| `imageGcHighThreshold` | 0-100 | 85 | 自此起始终运行映像垃圾回收的磁盘使用量百分比。 会触发垃圾回收的最低磁盘使用率。 如果要禁用映像垃圾回收，则设置为 100。 | 
| `imageGcLowThreshold` | 0-100，不大于 `imageGcHighThreshold` | 80 | 在此之前从不运行映像垃圾回收的磁盘使用量百分比。 可触发垃圾回收的最低磁盘使用率。 |
| `topologyManagerPolicy` | 无、最大努力、受限制、单个 numa 节点 | 无 | 有关优化 Numa 节点对齐的信息，请在[此处](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)详细了解。 |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | 无 | 允许的不安全 sysctl 或不安全 sysctl 模式的列表。 | 
| `containerLogMaxSizeMB` | 以兆字节 (MB) 为单位的大小 | 10 MB | 容器日志文件在轮换前的最大大小（例如，10 MB）。 | 
| `containerLogMaxFiles` | ≥ 2 | 5 | 一个容器可以存在的最大容器日志文件数。 | 
| `podMaxPids` | -1 到内核 PID 限制 | -1（无穷大）| 一个 Pod 中可以运行的最大进程 ID 数 |

### <a name="linux-os-custom-configuration"></a>Linux OS 自定义配置

下面列出了支持的 OS 设置和接受的值。

#### <a name="file-handle-limits"></a>文件句柄限制

在为大量流量提供服务时，你所服务的流量通常来自大量本地文件。 你可以略微调整以下内核设置和内置限制，以便只占用部分系统内存来处理更大的量。

| 设置 | 允许的值/间隔 | 默认 | 说明 |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Linux 内核将分配的最大文件句柄数，通过增加该值，可以增加允许打开的最大文件数。 |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | 系统允许的最大文件监视数。 每个监视在 32 位内核上约为 90 字节，在 64 位内核上约为 160 字节。 | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | Aio-nr 显示当前系统范围内的异步 IO 请求数。 使用 aio-max-nr，你可以更改 aio-nr 可以增加到的最大值。 |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | 进程可分配的最大文件句柄数。 |


#### <a name="socket-and-network-tuning"></a>套接字和网络优化

对于预期会处理大量并发会话的代理节点，你可以使用下面的 TCP 和网络选项子集，可按节点池调整这些选项。 

| 设置 | 允许的值/间隔 | 默认 | 说明 |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | 可为任何给定侦听套接字排队的最大连接请求数。 传递给 [listen(2)](http://man7.org/linux/man-pages/man2/listen.2.html) 函数的积压工作 (backlog) 形式参数值的上限。 如果积压工作 (backlog) 实际参数大于 `somaxconn`，则以静默方式截断为此上限。
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | 接口接收数据包的速度快于内核的处理速度时，在 INPUT 端排队的数据包的最大数量。 |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | 接收套接字缓冲区最大大小（以字节为单位）。 |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | 发送套接字缓冲区最大大小（以字节为单位）。 | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | 每个套接字允许的辅助缓冲区（选项内存缓冲区）最大大小。 在少数情况下，套接字选项内存用于存储与套接字使用相关的附加结构。 | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | 尚未收到连接客户端确认的最大排队连接请求数。 如果超出此数量，内核会开始删除请求。 |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | 系统同时容纳 `timewait` 套接字的最大数量。 如果超过此数量，则会立即销毁 time-wait 套接字并显示警告消息。 |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | 孤立连接（任何应用程序均不再引用）在本地终止之前保持 FIN_WAIT_2 状态的时间长度。 |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | 启用 `keepalive` 时，TCP 发出 `keepalive` 消息的频率。 |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | TCP 在确定连接已中断前，发出 `keepalive` 探测的数量。 |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | 探测发出的频率。乘以 `tcp_keepalive_probes`，等于在启动探测后终止未响应连接所需的时间。 | 
| `net.ipv4.tcp_tw_reuse` | 0 或 1 | 0 | 从协议角度看安全时，允许将 `TIME-WAIT` 套接字重用于新连接。 | 
| `net.ipv4.ip_local_port_range` | 第一个：1024 - 60999，最后一个：32768 - 65000 | 第一个：32768，最后一个：60999 | TCP 和 UDP 流量用来选择本地端口的本地端口范围。 由两个数字组成：第一个数字是代理节点上允许 TCP 和 UDP 流量使用的第一个本地端口，第二个数字是最后一个本地端口号。 | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | ARP 缓存中可能的最小项数。 如果项数低于此设置，不会触发垃圾回收。 | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | ARP 缓存中项数可能的软最大值。 此设置可能是最重要的，因为达到这一软最大值大约 5 秒后会触发 ARP 垃圾回收。 |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | ARP 缓存中项数的硬最大值。 |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` 是在 Linux 中跟踪 NAT 连接项的模块。 `nf_conntrack` 模块使用哈希表来记录 TCP 协议的已建立连接记录。 `nf_conntrack_max` 是哈希表中的最大节点数，即 `nf_conntrack` 模块支持的最大连接数或连接跟踪表大小。 | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` 是在 Linux 中跟踪 NAT 连接项的模块。 `nf_conntrack` 模块使用哈希表来记录 TCP 协议的已建立连接记录。 `nf_conntrack_buckets` 是哈希表的大小。 | 

#### <a name="worker-limits"></a>工作线程限制

与文件描述符限制一样，进程可以创建的辅助角色或线程的数量受内核设置和用户限制所限制。 AKS 中的用户数不受限制。 

| 设置 | 允许的值/间隔 | 默认 | 说明 |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | 进程可以启动工作线程。 可创建的所有线程的最大数量均使用内核设置 `kernel.threads-max` 进行设置。 | 

#### <a name="virtual-memory"></a>虚拟内存

以下设置可用于调整 Linux 内核虚拟内存 (VM) 子系统的操作以及向磁盘进行脏数据的 `writeout`。

| 设置 | 允许的值/间隔 | 默认 | 说明 |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | 此文件包含进程可能具有的最大内存映射区域数。 内存映射区域被用作 `mmap`、`mprotect` 和 `madvise` 直接调用 `malloc` 的副作用，也是加载共享库时的副作用。 | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | 此百分比值控制内核回收内存（用于缓存目录和 Inode 对象）的趋势。 |
| `vm.swappiness` | 0 - 100 | 60 | 此控制用于定义内核交换内存页的积极程度。 增加值会提高积极程度，降低值会减少交换量。 值 0 指示内核不发起交换，直到可用且文件支持的页数小于区域中的高水印。 | 
| `swapFileSizeMB` | 1 MB - [临时磁盘](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) 的大小 | 无 | SwapFileSizeMB 指定将在此节点池的代理节点上创建的交换文件的大小（以 MB 为单位）。 | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [透明大页](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge)是一项 Linux 内核功能，它旨在通过更高效地利用处理器的内存映射硬件来提高性能。 启用后，内核会尝试尽可能地分配 `hugepages`，如果 `mmap` 区域自然对齐 2 MB，则任何 Linux 进程都将收到 2 MB 大小的页面。 在某些情况下，如果在系统范围内启用 `hugepages`，应用程序最终可能会分配更多的内存资源。 应用程序可能会对一个较大的区域执行 `mmap`，但只接触其中 1 个字节，在这种情况下，可能会无故分配一个 2 MB 大小的页面，而不是 4k 大小的页面。 这种情况可能是在系统范围内禁用 `hugepages` 或者只将它们限定在 `MADV_HUGEPAGE madvise` 区域内的原因。 | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | 此值可控制内核是否应充分利用内存压缩，以使更多 `hugepages` 可用。 | 

> [!IMPORTANT]
> 为了便于搜索和阅读，OS 设置在此文档中直接显示名称，但在添加到配置 json 文件或 AKS API 中时，应遵循 [camelCase 大小写约定](/dotnet/standard/design-guidelines/capitalization-conventions)。

创建具有以下内容的 `kubeletconfig.json` 文件：

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
创建具有以下内容的 `linuxosconfig.json` 文件：

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

使用上一步中创建的 JSON 文件创建新群集，并指定 kubelet 和 OS 配置。 

> [!NOTE]
> 创建群集时，可以指定 kubelet 配置和/或 OS 配置。 如果在创建群集时指定配置，该配置只会对初始节点池中的节点应用。 JSON 文件中未配置的任何设置都将保留默认值。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

使用创建的 JSON 文件添加新节点池，同时指定 Kubelet 参数。

> [!NOTE]
> 将节点池添加到现有群集时，可以指定 kubelet 配置和/或 OS 配置。 如果在添加节点池时指定配置，则该配置只会对新节点池中的节点应用。 JSON 文件中未配置的任何设置都将保留默认值。

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>后续步骤

- 了解[如何配置 AKS 群集](cluster-configuration.md)。
- 了解如何在群集中[升级节点映像](node-image-upgrade.md)。
- 若要了解如何将群集升级到最高版本的 Kubernetes，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集](upgrade-cluster.md)。
- 若要查找有关一些常用 AKS 问题的答案，请参阅 [AKS 常见问题解答](faq.md)。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ../azure-monitor/containers/container-insights-log-query.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
