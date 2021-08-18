---
title: 删除 Azure Arc 数据控制器
description: 删除 Azure Arc 数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 26784222d49e6f48ed324ce345dcb1f2ba7d4cf1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741443"
---
# <a name="delete-azure-arc-data-controller"></a>删除 Azure Arc 数据控制器

以下文章介绍如何删除 Azure Arc 数据控制器。

在继续操作之前，请先确保在数据控制器上创建的所有数据服务均已删除，如下所示：

## <a name="list--delete-existing-data-services"></a>列出并删除现有数据服务

运行以下命令，检查是否已创建任何 SQL 托管实例：

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

对于上表中的每个 SQL 托管实例，请运行删除命令，如下所示：

```azurecli
az sql mi-arc delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az sql mi-arc delete -n sqlinstance1 --k8s-namespace <namespace> --use-k8s
```

同样地，如果要检查是否存在超大规模 PostgreSQL 实例，请运行：

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

对于每个超大规模 PostgreSQL 实例，请运行删除命令，如下所示：

```azurecli
az postgres arc-server delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az postgres arc-server delete -n pg1 --k8s-namespace <namespace> --use-k8s
```

## <a name="delete-controller"></a>删除控制器

删除了所有 SQL 托管实例和超大规模 PostgreSQL 实例后，即可删除数据控制器，如下所示：

```azurecli
az arcdata dc delete -n <name> -ns <namespace>
# for example: az arcdata dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>删除 SCC（仅限 Red Hat OpenShift）

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>删除群集级别对象

除了命名空间范围内的对象之外，如果还想要删除群集级别的对象（例如 CRD、`clusterroles` 和 `clusterrolebindings`），请运行以下命令：

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>（可选）删除 Azure Arc 数据控制器命名空间


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>后续步骤

[什么是已启用 Azure Arc 的数据服务？](overview.md)
