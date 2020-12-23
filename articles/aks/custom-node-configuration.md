---
title: '自定义 Azure Kubernetes Service 的节点配置 (AKS) 节点池 (预览) '
description: 了解如何自定义 Azure Kubernetes Service (AKS) 群集节点和节点池的配置。
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: f1e9d65baacb9c712b92ef6f00abda169031b47e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581644"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>自定义 Azure Kubernetes Service (AKS) 节点池的节点配置 (预览) 

自定义节点配置使你可以配置或优化操作系统 (OS) 设置或 kubelet 参数，以满足工作负荷的需求。 当你创建 AKS 群集或向群集中添加节点池时，你可以自定义常用 OS 和 kubelet 设置的子集。 若要配置此子集之外的设置，请 [使用守护程序集自定义所需的配置，而不会失去对节点的 AKS 支持](support-policies.md#shared-responsibility)。

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>注册 `CustomNodeConfigPreview` 预览功能

若要创建可自定义 kubelet 参数或 OS 设置的 AKS 群集或节点池，必须 `CustomNodeConfigPreview` 在订阅上启用功能标志。

`CustomNodeConfigPreview`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

准备就绪后，请使用 [az provider register][az-provider-register]命令刷新 *ContainerService* 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建可自定义 kubelet 参数或 OS 设置的 AKS 群集或节点池，需要最新的 *AKS-preview* Azure CLI 扩展。 使用 [az extension add][az-extension-add]命令安装 *aks-preview* Azure CLI 扩展。 或使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>使用自定义节点配置

### <a name="kubelet-custom-configuration"></a>Kubelet 自定义配置

下面列出了支持的 Kubelet 参数和接受的值。

| 参数 | 允许的值/间隔 | 默认 | 描述 |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | 无、静态 | 无 | 静态策略允许具有整数 CPU 请求的可 [保证盒箱](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) 中的容器访问节点上的独占 cpu。 |
| `cpuCfsQuota` | true、false | true |  对于指定 CPU 限制的容器，启用/禁用 CPU CFS 配额强制。 | 
| `cpuCfsQuotaPeriod` | 以毫秒为单位的间隔 (毫秒)  | `100ms` | 设置 CPU CFS 配额周期值。 | 
| `imageGcHighThreshold` | 0-100 | 85 | 始终运行映像垃圾回收后的磁盘使用百分比。 **将** 触发垃圾回收的最小磁盘使用量。 若要禁用映像垃圾回收，请将设置为100。 | 
| `imageGcLowThreshold` | 0-100，无高于 `imageGcHighThreshold` | 80 | 从不运行映像垃圾回收之前磁盘使用量的百分比。 **可** 触发垃圾回收的最小磁盘使用情况。 |
| `topologyManagerPolicy` | 无，最大努力，受限制，单 numa 节点 | 无 | 优化 NUMA 节点对齐，请 [在此处](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)查看详细信息。 仅 kubernetes v 1.18 +。 |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | 无 | 允许的 unsafe sysctls 或 unsafe sysctl 模式的列表。 | 

### <a name="linux-os-custom-configuration"></a>Linux OS 自定义配置

下面列出了受支持的 OS 设置和可接受的值。

#### <a name="file-handle-limits"></a>文件句柄限制

在为大量流量提供服务时，所提供的流量通常来自大量的本地文件。 你可以调整以下内核设置和内置限制，使你能够以某些系统内存为代价来处理更多的情况。

| 设置 | 允许的值/间隔 | 默认 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Linux 内核将分配的文件句柄的最大数目通过增加此值，可以增加允许的最大打开文件数。 |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | 系统允许的最大文件监视次数。 每个 *监视* 在32位内核上约为90字节，而在64内核上约160个字节。 | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | Aio-nr 显示当前系统范围内的异步 io 请求数。 aio-最大值-nr 允许你更改 aio-nr 可以增长到的最大值。 |
| `fs.nr_open` | 8192-20000500 | 1048576 | 进程可分配的文件句柄的最大数量。 |


#### <a name="socket-and-network-tuning"></a>套接字和网络优化

对于预期会处理大量并发会话的代理节点，你可以使用下面的 TCP 和网络选项子集，你可以根据节点池进行调整。 

| 设置 | 允许的值/间隔 | 默认 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-3240000 | 16384 | 可为任何给定侦听套接字排队的最大连接请求数。 传递给 [侦听 (2) ](http://man7.org/linux/man-pages/man2/listen.2.html) 函数的积压工作（backlog）参数值的上限。 如果积压工作参数大于 `somaxconn` ，则它会以静默方式截断到此限制。
| `net.core.netdev_max_backlog` | 1000-3240000 | 1000 | 当接口接收数据包时，在输入端排队的最大数据包数量可以处理这些数据包。 |
| `net.core.rmem_max` | 212992-134217728 | 212992 | 最大接收套接字缓冲区大小（以字节为单位）。 |
| `net.core.wmem_max` | 212992-134217728 | 212992 | 最大发送套接字缓冲区大小（以字节为单位）。 | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | 辅助缓冲区最大大小 (选项内存缓冲区) 每个套接字允许。 在少数情况下，套接字选项内存用于存储与套接字的使用相关的附加结构。 | 
| `net.ipv4.tcp_max_syn_backlog` | 128-3240000 | 16384 | 尚未收到来自连接客户端的确认的排队连接请求的最大数量。 如果超出此数量，内核将开始删除请求。 |
| `net.ipv4.tcp_max_tw_buckets` | 8000-1440000 | 32768 | `timewait`系统同时容纳的最大插槽数。 如果超过此数目，则会立即销毁时间等待套接字并输出警告。 |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | 不能再由任何应用程序) 连接引用的孤立 (的时间将在本地终止之前保持 FIN_WAIT_2 状态。 |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | TCP `keepalive` 在启用时发送消息的频率 `keepalive` 。 |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | `keepalive`TCP 发出多少探测，直到它确定连接中断。 |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | 探测发出的频率。再乘以 `tcp_keepalive_probes` 它可以在探测开始后终止未响应的连接。 | 
| `net.ipv4.tcp_tw_reuse` | 0 或 1 | 0 | `TIME-WAIT`当安全地利用协议视点时，允许将套接字用于新连接。 | 
| `net.ipv4.ip_local_port_range` | 第一个： 1024-60999，最后： 32768-65000] | 第一：32768，最后：60999 | TCP 和 UDP 流量用来选择本地端口的本地端口范围。 由两个数字组成：第一个数字是代理节点上的 TCP 和 UDP 流量允许的第一个本地端口，第二个是最后一个本地端口号。 | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-80000 | 4096 | ARP 缓存中可能的最小项数。 如果条目数低于此设置，则不会触发垃圾回收。 | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-90000 | 8192 | ARP 缓存中可能的最大软条目数。 此设置可能是最重要的，因为在达到这一软最大值后，将触发大约5秒的 ARP 垃圾回收。 |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-100000 | 16384 | ARP 缓存中的最大项数。 |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` 是在 Linux 中跟踪 NAT 连接项的模块。 `nf_conntrack`模块使用哈希表记录 TCP 协议的已 *建立连接* 记录。 `nf_conntrack_max` 哈希表中的最大节点数，即，模块支持的最大连接数 `nf_conntrack` 或连接跟踪表的大小。 | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` 是在 Linux 中跟踪 NAT 连接项的模块。 `nf_conntrack`模块使用哈希表记录 TCP 协议的已 *建立连接* 记录。 `nf_conntrack_buckets` 哈希表的大小。 | 

#### <a name="worker-limits"></a>工作线程限制

与文件描述符限制一样，进程可以创建的辅助角色或线程的数量受内核设置和用户限制的限制。 AKS 上的用户限制是不受限制的。 

| 设置 | 允许的值/间隔 | 默认 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | 进程可以加速工作线程。 可创建的所有线程的最大数目均设置为 "内核" 设置 `kernel.threads-max` 。 | 

#### <a name="virtual-memory"></a>虚拟内存

以下设置可用于优化 Linux 内核 (VM) 子系统的虚拟内存和 `writeout` 磁盘的脏数据的操作。

| 设置 | 允许的值/间隔 | 默认 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | 此文件包含进程可能具有的最大内存映射区域数。 内存映射区域用作直接调用、和的副作用， `malloc` `mmap` 也是 `mprotect` `madvise` 在加载共享库时。 | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | 此百分比值控制内核回收内存的趋势，该内存用于缓存目录和 inode 对象。 |
| `vm.swappiness` | 0 - 100 | 60 | 此控件用于定义内核交换内存页的方式。 较高的值会增加入侵量，较小的值会减少交换量。 如果值为0，则指示内核不启动交换，直到可用和文件支持的页数小于区域中的高水位标记。 | 
| `swapFileSizeMB` | 1 MB- (/dev/sdb 的 [临时磁盘](../virtual-machines/managed-disks-overview.md#temporary-disk) 的大小)  | 无 | SwapFileSizeMB 指定将在此节点池的代理节点上创建交换文件的大小（MB）。 | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [透明 Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) 是一项 Linux 核心功能，旨在通过更有效地使用处理器的内存映射硬件来提高性能。 启用后，内核会尝试尽可能地进行分配 `hugepages` ，如果 `mmap` 区域为 2 mb，则任何 Linux 进程都将接收 2 mb 页面。 在某些情况下 `hugepages` ，当系统范围内启用时，应用程序可能会最终分配更多的内存资源。 应用程序可能 `mmap` 是一个较大的区域，只需触摸1个字节，在这种情况下，可能会分配一个 2 MB 页面，而不是一个4k 页面。 这种情况的原因是可以禁用 `hugepages` 系统范围的，或者只是将它们包含在 `MADV_HUGEPAGE madvise` 区域内。 | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | 此值控制内核是否应充分利用内存压缩以提高其 `hugepages` 可用性。 | 

> [!IMPORTANT]
> 为了便于搜索和可读性，OS 设置按名称显示在此文档中，但应使用 [camelCase 的大小写约定](https://docs.microsoft.com/dotnet/standard/design-guidelines/capitalization-conventions)添加到配置 json 文件或 AKS API。

创建 `kubeletconfig.json` 具有以下内容的文件：

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
创建 `linuxosconfig.json` 具有以下内容的文件：

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

使用上一步中创建的 JSON 文件创建新群集，指定 kublet 和 OS 配置。 

> [!NOTE]
> 创建群集时，可以指定 kubelet 配置和/或 OS 配置。 如果在创建群集时指定配置，则只会对初始节点池中的节点应用该配置。 未在 JSON 文件中配置的任何设置都将保留默认值。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

使用所创建的 JSON 文件，添加指定 Kubelet 参数的新节点池。

> [!NOTE]
> 将节点池添加到现有群集时，可以指定 kubelet 配置和/或 OS 配置。 如果在添加节点池时指定配置，则仅新节点池中的节点会应用该配置。 未在 JSON 文件中配置的任何设置都将保留默认值。

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>后续步骤

- 了解 [如何配置 AKS 群集](cluster-configuration.md)。
- 了解如何 [升级群集中的节点映像](node-image-upgrade.md) 。
- 若要了解如何将群集升级到最高版本的 Kubernetes，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集](upgrade-cluster.md)。
- 若要查找有关一些常用 AKS 问题的答案，请参阅 [AKS 常见问题解答](faq.md)。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why