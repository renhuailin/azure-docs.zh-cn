---
title: 列出 Azure Arc 数据控制器中创建的已启用 Azure Arc 的 PostgreSQL 超大规模服务器组
description: 列出 Azure Arc 数据控制器中创建的已启用 Azure Arc 的 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 7254ed303e45c69f291aa5c7a06f63390aaed162
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750268"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>列出 Azure Arc 数据控制器中创建的已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

本文介绍如何检索 Arc 数据控制器中创建的服务器组列表。

若要检索此列表，请在连接到 Arc 数据控制器后使用以下方法之一：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>在 CLI 中使用 Azure CLI 扩展 (az)

该命令的一般格式为：
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

此命令将返回如下所示的输出：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
有关可用于此命令的参数的更多详细信息，请运行：
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>在 CLI 中使用 kubectl
运行以下命令之一。

若要列出任意 Postgres 版本的服务器组，请运行：
```console
kubectl get postgresqls
```
此命令将返回如下所示的输出：
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

若要列出特定 Postgres 版本的服务器组，请运行：
```console
kubectl get postgresql-12
```

若要列出运行 Postgres 版本 11 的服务器组，请将 postgresql-12 替换为 postgresql-11 。

## <a name="next-steps"></a>后续步骤：

* [阅读有关如何获取连接终结点以及构建用于连接服务器组的连接字符串的文章](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [阅读有关如何显示已启用 Azure Arc 的 PostgreSQL 超大规模服务器组配置的文章](show-configuration-postgresql-hyperscale-server-group.md)
