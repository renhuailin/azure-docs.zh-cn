---
title: 快速入门：创建基本层服务器组 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL 超大规模 (Citus) 基本层入门。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023824"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>在 Azure 门户中创建超大规模 (Citus) 基本层服务器组

Azure Database for PostgreSQL - 超大规模 (Citus) 是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 数据库。 其[基本层](concepts-hyperscale-tiers.md)是一种用于初始开发和测试的便捷部署选项。

本快速入门介绍如何使用 Azure 门户创建超大规模 (Citus) 基本层服务器组。 你将预配服务器组，并验证能否连接到该服务器组来运行查询。

> [!IMPORTANT]
> 超大规模 (Citus) 基本层目前以预览版提供。  此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 请参阅[超大规模 (Citus) 的预览功能](hyperscale-preview-features.md)，以查看其他新功能的完整列表。

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何预配 Hyperscale (Citus) 服务器组。 你已使用 psql 连接到该组，创建了架构并分布了数据。

- 遵循以下教程[生成可缩放的多租户应用程序](./tutorial-design-database-hyperscale-multi-tenant.md)
- 确定服务器组的最佳[初始大小](howto-hyperscale-scale-initial.md)
