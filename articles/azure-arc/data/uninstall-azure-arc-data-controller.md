---
title: 删除 Azure Arc 数据控制器
description: 删除 Azure Arc 数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91660670"
---
# <a name="delete-azure-arc-data-controller"></a>删除 Azure Arc 数据控制器

以下文章介绍如何删除 Azure Arc 数据控制器。

在继续操作之前，请先确保在数据控制器上创建的所有数据服务均已删除，如下所示：

## <a name="log-in-to-the-data-controller"></a>登录到数据控制器

登录到要删除的数据控制器：

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>列出并删除现有数据服务

运行以下命令，检查是否已创建任何 SQL 托管实例：

```
azdata arc sql mi list
```

对于上表中的每个 SQL 托管实例，请运行删除命令，如下所示：

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

同样地，如果要检查是否存在超大规模 PostgreSQL 实例，请运行：

```
azdata arc postgres server list
```

对于每个超大规模 PostgreSQL 实例，请运行删除命令，如下所示：
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>删除控制器

删除了所有 SQL 托管实例和超大规模 PostgreSQL 实例后，即可删除数据控制器，如下所示：

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
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
