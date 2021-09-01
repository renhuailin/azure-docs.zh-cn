---
title: 删除已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
description: 删除已启用 Azure Arc 的超大规模 Postgres 服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 00387e190df3bcc6f654868d078a068e9196a635
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750314"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>删除已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

本文档介绍从 Azure Arc 设置中删除服务器组的步骤。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>删除服务器组

例如，我们考虑从下面的设置中删除 postgres01 实例：

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

删除命令的一般格式为：
```azurecli
az postgres arc-server delete -n <server group name> --k8s-namespace <namespace> --use-k8s
```
执行此命令时，系统将请求你确认删除服务器组。 如果使用脚本自动执行删除操作，则需要使用 --force 参数绕过确认请求。 例如，你将运行如下命令： 
```azurecli
az postgres arc-server delete -n <server group name> --force --k8s-namespace <namespace> --use-k8s
```

有关删除命令的详细信息，请运行：
```azurecli
az postgres arc-server delete --help 
```

### <a name="delete-the-server-group-used-in-this-example"></a>删除本示例中使用的服务器组

```azurecli
az postgres arc-server delete -n postgres01 --k8s-namespace <namespace> --use-k8s
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>回收 Kubernetes 永久性卷声明 (PVC)

删除服务器组不会删除其关联的 [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。 这是设计的结果。 目的是帮助用户访问数据库文件，以防意外删除实例。 删除 PVC 不是必需的， 但建议这样做。 如果不回收这些 PVC，最终会出现错误，因为 Kubernetes 群集会认为磁盘空间不足。 若要回收 PVC，请执行以下步骤：

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 列出已删除的服务器组的 PVC

若要列出 PVC，请运行以下命令：

```console
kubectl get pvc [-n <namespace name>]
```

它将返回 PVC 列表，尤其是你删除的服务器组的 PVC。 例如：

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
此服务器组有 8 个 PVC。

### <a name="2-delete-each-of-the-pvcs"></a>2. 删除每个 PVC

删除已删除的服务器组的每个 PostgreSQL 超大规模节点（协调器和辅助角色）的数据和日志 PVC。

此命令的一般格式为： 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

例如：

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

其中每个 kubectl 命令都将确认成功删除 PVC。 例如：

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> 如前所述，不删除这些 PVC 可能最终导致 Kubernetes 群集引发错误。 其中一些错误可能包括无法从 Kubernetes API 创建、读取、更新、删除资源，或者无法运行 `az arcdata dc export` 等命令，因为控制器 Pod 可能会因该存储问题（正常 Kubernetes 行为）而被逐出 Kubernetes 节点。
>
> 例如，你可能会在日志中看到类似于以下内容的消息：  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>后续步骤
创建[已启用 Azure Arc 的超大规模 PostgreSQL](create-postgresql-hyperscale-server-group.md)
