---
title: 删除已启用 Azure Arc 的 SQL 托管实例
description: 删除已启用 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 19f5befde22ed7b16302b7da5df313c476b47194
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733561"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>删除已启用 Azure Arc 的 SQL 托管实例
本文介绍如何删除已启用 Azure Arc 的 SQL 托管实例。


## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>查看已启用 Azure Arc 的现有 SQL 托管实例
若要查看 SQL 托管实例，请运行以下命令：

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

输出应类似于以下内容：

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>删除已启用 Azure Arc 的 SQL 托管实例
若要删除 SQL 托管实例，请运行以下命令：

```azurecli
az sql mi-arc delete -n <NAME_OF_INSTANCE> --k8s-namespace <namespace> --use-k8s
```

输出应类似于以下内容：

```azurecli
# az sql mi-arc delete -n demo-mi --k8s-namespace <namespace> --use-k8s
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>回收 Kubernetes 永久性卷声明 (PVC)

删除 SQL 托管实例不会删除其关联的 [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。 这是设计的结果。 目的是帮助用户访问数据库文件，以防意外删除实例。 删除 PVC 不是必需的， 但建议这样做。 如果不回收这些 PVC，最终会出现错误，因为 Kubernetes 群集会没有磁盘空间可用。 若要回收 PVC，请执行以下步骤：

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 列出已删除的服务器组的 PVC
若要列出 PVC，请运行以下命令：
```console
kubectl get pvc
```

在下面的示例中，请留意删除的 SQL 托管实例的 PVC。
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. 删除每个 PVC
删除已删除的每个 SQL 托管实例的数据和日志 PVC。
此命令的一般格式为： 
```console
kubectl delete pvc <name of pvc>
```

例如：
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

其中每个 kubectl 命令都将确认成功删除 PVC。 例如：
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> 如前所述，不删除这些 PVC 可能最终导致 Kubernetes 群集引发错误。 其中一些错误可能包括无法从 Kubernetes API 创建、读取、更新、删除资源，或者无法运行 `az arcdata dc export` 等命令，因为控制器 Pod 可能会因为此存储问题而从 Kubernetes 节点中逐出（正常 Kubernetes 行为）。
>
> 例如，你可能会在日志中看到类似于以下内容的消息：  
> - 注释：microsoft.com/ignore-pod-health: true  
> - 状态：失败  
> - 原因：被逐出  
> - 消息：节点资源（临时存储）不足。 容器控制器使用的是 16372Ki，超过了其请求的 0。

## <a name="next-steps"></a>后续步骤

详细了解[已启用 Azure Arc 的 SQL 托管实例的特性和功能](managed-instance-features.md)

[首先创建数据控制器](create-data-controller.md)

已创建数据控制器？ [创建已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)