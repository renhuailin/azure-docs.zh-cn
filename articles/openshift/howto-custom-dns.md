---
title: 在 Azure Red Hat OpenShift (ARO) 群集中配置自定义 DNS 资源
description: 了解如何在 Azure Red Hat OpenShift (ARO) 中的所有节点上添加自定义 DNS 服务器。
author: bryanro92
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/02/2021
ms.openlocfilehash: 842e0a4d57254c4ec27bdadf2ff168fe2f4c0424
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111441963"
---
# <a name="configure-custom-dns-for-your-azure-red-hat-openshift-aro-cluster"></a>为 Azure Red Hat OpenShift (ARO) 群集配置自定义 DNS

本文提供了配置 Azure Red Hat OpenShift 群集 (ARO) 以使用自定义 DNS 服务器的必要详细信息。 其中包含用于基本 ARO 部署的群集要求。

## <a name="before-you-begin"></a>准备阶段

本文假设你要创建一个新的群集，或者已有一个应用了最新更新的群集。 如果需要 ARO 群集，请参阅适用于公用群集的 [ARO 快速入门](./tutorial-create-cluster.md)，或适用于专用群集的[专用群集教程](./howto-create-private-cluster-4x.md)。 配置群集以使用自定义 DNS 服务器的步骤对于专用和公用群集都是相同的。

### <a name="confirm-cluster-compatibility-with-custom-dns"></a>确认群集与自定义 DNS 的兼容性

通过验证 `99-master-aro-dns` 和 `99-worker-aro-dns` `machineconfigs` 是否存在，确认群集是否能够支持此功能。

```
oc get machineconfig
```

如果上述命令的结果包含以下 machineconfigs，则群集能够支持自定义 DNS。

```
NAME                 GENERATEDBYCONTROLLER                      IGNITIONVERSION   AGE
...
99-master-aro-dns                                               2.2.0             54d
99-worker-aro-dns                                               2.2.0             54d
...
```

## <a name="dns-architecture-overview"></a>DNS 体系结构概述

当 Azure Red Hat OpenShift 群集中的每个节点都开机并加入网络时，DHCP 会使用 IP 地址和要使用的 DNS 服务器等信息来配置虚拟机。

下面是有关如何获取配置的过程流概述：

![DNS](media/concepts-networking/custom-dns-pfd.jpg)

请了解这一重要的取舍选择：如果使用自己的 DNS 服务器而不是虚拟网络中的默认 DNS 服务器，你将失去 DNS 服务器提供的配置。 虚拟机名称将不再通过网络上的 DNS 进行解析。

### <a name="update-process-overview"></a>升级过程概述

为群集配置自定义 DNS 服务器分为两个步骤。

1. 修改虚拟网络 DNS 服务器配置的设置。
2. 重启群集中的节点以进行更改。


## <a name="configure-a-custom-dns-server"></a>配置自定义 DNS 服务器

你还可以通过命令行执行以下步骤，但本文档将逐步介绍如何使用门户 web 接口。

### <a name="update-dns-configuration-in-virtual-network"></a>更新虚拟网络中的 DNS 配置

登录到 Azure 门户并导航到需要更新的虚拟网络。 从虚拟网络设置列表中选择“DNS 服务器”。

![选择 DNS](media/concepts-networking/vnet-dns-config.png)

进入 DNS 配置屏幕后，请从单选按钮配置中选择“自定义”。 输入 DNS 服务器的 IP 地址。

>[!IMPORTANT]
> 如果选择指定自定义 DNS 服务器，你将无法再通过 DNS 解析虚拟网络中的节点名称。 仅可通过 IP 地址访问节点。

![指定自定义 DNS 服务器](media/concepts-networking/vnet-custom-dns.png)

选择“保存”。 

>[!NOTE]
> 如门户接口中所示，必须重启所有虚拟机才能使更改生效。

你应该会收到更新已成功的通知。

![确认 DNS 更改](media/concepts-networking/vnet-dns-confirm-saved.png)

### <a name="gracefully-reboot-your-cluster"></a>正常重启群集

这些步骤需要拥有群集的有效 kubeconfig，请参阅[此教程](./tutorial-connect-cluster.md)了解有关如何获取 kubeconfig 的详细信息。

以下代码片段为主节点和工作器节点创建 noop `machineconfig`。 这可以为工作器节点或主节点启动滚动重启。 有关 Machine Config Operator (MCO) 的详细信息，请参阅[源代码](https://github.com/openshift/machine-config-operator)或 [MCO 的 OpenShift 文档](https://docs.openshift.com/container-platform/4.6/architecture/control-plane.html)。
#### <a name="machineconfig-definitions"></a>MachineConfig 定义

工作器重启：

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 25-machineconfig-worker-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-noop-worker-restart.txt
```

主服务器重启：

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: 25-machineconfig-master-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-master-noop-restart.txt
```

#### <a name="reboot-worker-nodes"></a>重启工作器节点

创建工作器重启文件，此示例调用文件 `worker-restarts.yml` 并应用该文件。

```
[user@bastion ~]$ vim worker-restarts.yml
[user@bastion ~]$ oc apply -f worker-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-worker-reboot created
```

MCO 将移动工作负载，然后逐一重启每个节点。 工作器重新联机后，我们将按照相同步骤重启主节点。 你可以通过查询节点并验证它们是否处于 `Ready` 状态来检验工作器的状态。

>[!NOTE]
> 根据群集的工作负载大小，每个节点可能需要几分钟才能重启。


示例工作器节点未完全就绪：

```
NAME                                  STATUS                     ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready                      worker   5h35m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready,SchedulingDisabled   worker   5h34m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready                      worker   5h35m   v1.19.0+a5a0987
```

节点重启时，你将看到它更改为“NotReady”状态：

```
dns-docs-tm45t-worker-eastus2-ln2kq   NotReady,SchedulingDisabled   worker   5h38m   v1.19.0+a5a0987
```

完全就绪：

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready    master   5h45m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   5h41m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   5h41m   v1.19.0+a5a0987
```

#### <a name="reboot-master-nodes"></a>重启主节点

现在，请为主节点重复相同的过程：

```
[user@bastion ~]$ vim master-restarts.yml
[user@bastion ~]$ oc apply -f master-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-master-reboot created
```

确认所有节点已返回到 `Ready` 状态：

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
dns-docs-tm45t-master-0               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   6h3m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   6h2m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   6h3m   v1.19.0+a5a0987
```

#### <a name="confirm-changes-on-a-node-optional"></a>确认节点上的更改（可选）

若要验证节点上的新 DNS 服务器，我们将使用 `oc debug` pod。

```
[user@bastion ~]$ oc debug node/dns-docs-tm45t-worker-eastus2-ln2kq
Starting pod/dns-docs-tm45t-worker-eastus2-ln2kq-debug ...
To use host binaries, run `chroot /host`
chroot Pod IP: 10.0.2.6
If you don't see a command prompt, try pressing enter.
sh-4.4# chroot /host
sh-4.4# uptime
 18:40:16 up 1 min,  0 users,  load average: 0.82, 0.32, 0.12
sh-4.4# cat /etc/resolv.conf.dnsmasq
# Generated by NetworkManager
search reddog.microsoft.com
nameserver 192.168.0.1
```
## <a name="modifying-the-custom-dns-server"></a>修改自定义 DNS 服务器

在已有自定义 DNS 的群集上修改自定义 DNS 的步骤按照相同的[过程](#update-process-overview)进行。

### <a name="modify-dns"></a>修改 DNS

请按照[此处](#update-dns-configuration-in-virtual-network)概述的步骤更新虚拟网络上的 DNS 配置。

### <a name="reboot-nodes"></a>重启节点

我们不会创建 `machineconfig`，而是要删除第一次创建的 `machineconfig`。 我们将从工作器节点开始。

```
oc delete machineconfig 25-machineconfig-worker-reboot
```

输出：
```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-worker-reboot" deleted
```

等待所有工作器节点重启。 这类似于上述的[重启工作器节点](#reboot-worker-nodes)。

现在我们将重启主节点。

```
oc delete machineconfig 25-machineconfig-master-reboot
```

输出：

```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-master-reboot" deleted
```

等待所有主节点重启并返回到“就绪”状态。