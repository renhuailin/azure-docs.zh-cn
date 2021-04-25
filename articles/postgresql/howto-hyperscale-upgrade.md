---
title: 升级服务器组 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 本文介绍如何在 Azure Database for PostgreSQL 超大规模 (Citus) 中升级 PostgreSQL 和 Citus。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518892"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>升级超大规模 (Citus) 服务器组

这些说明介绍了如何在所有服务器组节点上升级到 PostgreSQL 新的主版本。

## <a name="test-the-upgrade-first"></a>首先测试升级

升级 PostgreSQL 会带来比你想象的更多的更改，因为超大规模 (Citus) 还将升级[数据库扩展](concepts-hyperscale-extensions.md)，包括 Citus 扩展。
强烈建议在升级生产环境之前，使用新的 PostgreSQL 和 Citus 版本测试应用程序。

一种简单的测试方法是使用[时间点还原](concepts-hyperscale-backup.md#restore)服务器组的副本。 升级副本并针对它测试应用程序。 验证一切正常后，升级源服务器组。

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>在 Azure 门户中升级服务器组

1. 在超大规模 (Citus) 服务器组的“概述”部分中，选择“升级”按钮 。
1. 随即出现一个对话框，其中显示 PostgreSQL 和 Citus 的当前版本。
   在“升级到”列表中选择一个新的 PostgreSQL 版本。
1. 验证升级后的 Citus 版本中的值是否符合预期。
   该值根据所选的 PostgreSQL 版本而变化。
1. 选择“升级”按钮继续操作。

## <a name="next-steps"></a>后续步骤

* 了解[支持的 PostgreSQL 版本](concepts-hyperscale-versions.md)。
* 查看超大规模 (Citus) 服务器组中每个 PostgreSQL 版本打包了[哪些扩展](concepts-hyperscale-extensions.md)。
