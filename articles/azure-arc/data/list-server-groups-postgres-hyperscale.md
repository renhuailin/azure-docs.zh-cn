---
title: 列出 Arc 数据控制器中创建的已启用 Azure Arc 的超大规模 PostgreSQL服务器组
description: 列出 Arc 数据控制器中创建的已启用 Azure Arc 的超大规模 PostgreSQL服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90934823"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>列出 Arc 数据控制器中创建的已启用 Azure Arc 的超大规模 PostgreSQL服务器组

本文介绍如何检索 Arc 数据控制器中创建的服务器组列表。

若要检索此列表，请在连接到 Arc 数据控制器后使用以下方法之一：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>在 CLI 中使用 azdata
该命令的一般格式为：
```console
azdata arc postgres server list
```

此命令将返回如下所示的输出：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
有关可用于此命令的参数的更多详细信息，请运行：
```console
azdata arc postgres server list --help
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
* [阅读有关如何显示已启用 Azure Arc 的超大规模 PostgreSQL 服务器组配置的文章](show-configuration-postgresql-hyperscale-server-group.md)
