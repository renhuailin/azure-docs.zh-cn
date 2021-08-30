---
title: 已启用 Azure Arc 的超大规模 PostgreSQL 支持的 Postgres 版本
description: 已启用 Azure Arc 的超大规模 PostgreSQL 支持的 Postgres 版本
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0a00475eacdeb741eca20d4a6c43282df9cfd17d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741454"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL 支持的 Postgres 版本

本文介绍可在已启用 Azure Arc 的超大规模 PostgreSQL 中使用的 Postgres 版本。
受支持版本的列表会不断变化。 目前，支持的主版本如下：
- Postgres 12（默认）
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>如何选择版本？
建议查看应用程序的设计目标版本，以及每个版本的功能。 有关详细信息，请在官方 Postgres 站点上阅读每个版本的信息：
- [Postgres 12（默认）](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>如何在已启用 Azure Arc 的超大规模 PostgreSQL 中创建特定的版本？
创建时，可以通过传递 --engine-version 参数来指示要创建的版本。 如果不指示版本信息，则默认会创建 Postgres 版本 12 的服务器组。

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>如何在其他版本可用时收到通知？
请回头阅读本文。 本文的内容会相应地更新。 也可以在 Kubernetes 群集上的 Arc 数据控制器中列出自定义资源定义 (CRD) 的类型。
运行以下命令：
```console
kubectl get crds
```

此命令将返回如下所示的输出：
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

在此示例中，此输出指示有两种类型的 CRD 与 Postgres 相关：
- postgresql-12s.arcdata.microsoft.com 是 Postgres 12 的 CRD
- postgresql-11s.arcdata.microsoft.com 是 Postgres 11 的 CRD

这些值是 CRD，而不是服务器组。 存在 CRD 并不意味着你已创建或未创建该版本的服务器组。
CRD 指示可创建的资源类型。

## <a name="next-steps"></a>后续步骤：
- [了解如何创建已启用 Azure Arc 的超大规模 PostgreSQL](create-postgresql-hyperscale-server-group.md)
- [了解如何获取 Arc 数据控制器中创建的已启用 Azure Arc 的超大规模 PostgreSQL服务器组列表](list-server-groups-postgres-hyperscale.md)
