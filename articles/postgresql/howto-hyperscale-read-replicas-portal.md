---
title: 管理只读副本 - Azure 门户 - Azure Database for PostgreSQL - 超大规模 (Citus)
description: 了解如何通过 Azure 门户管理 Azure Database for PostgreSQL - 超大规模 (Citus) 的只读副本。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 74c9df5ee9eb6cd6a61104b4c9cce91b284dbb29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726138"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>通过 Azure 门户创建和管理 Azure Database for PostgreSQL - 超大规模 (Citus) 中的只读副本

本文介绍如何通过 Azure 门户创建和管理超大规模 (Citus) 中的只读副本。 若要详细了解只读副本，请参阅[概述](concepts-hyperscale-read-replicas.md)。


## <a name="prerequisites"></a>先决条件

[超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)作为主服务器组。

## <a name="create-a-read-replica"></a>创建只读副本

若要创建只读副本，请遵循以下步骤：

1. 选择将用作主服务器组的现有 Azure Database for PostgreSQL 服务器组。 

2. 在“服务器组”边栏的“服务器组管理”下，选择“复制” 。

3. 选择“添加副本”。

4. 输入只读副本的名称。 

5. 选择“确定”以确认创建该副本。

创建只读副本后，可以在“复制”窗口中查看它。

> [!IMPORTANT]
>
> 查看[“只读副本”概述的注意事项部分](concepts-hyperscale-read-replicas.md#considerations)。
>
> 将主服务器组设置更新为新值之前，请将副本设置更新为一个相等的或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

## <a name="delete-a-primary-server-group"></a>删除主服务器组

若要删除主服务器组，请执行删除超大规模 (Citus) 独立服务器组的相同步骤。 

> [!IMPORTANT]
>
> 删除主服务器组后，将停止复制到所有只读副本的操作。 只读副本将成为支持读取和写入的独立服务器组。

若要在 Azure 门户中删除服务器组，请遵循以下步骤：

1. 在 Azure 门户中，选择 Azure Database for PostgreSQL 主服务器组。

2. 此时会打开该服务器组的“概述”页。 选择“删除”。
 
3. 输入要删除的主服务器组的名称。 选择“删除”以确认删除主服务器组。
 

## <a name="delete-a-replica"></a>删除副本

可以像删除主服务器组一样删除只读副本。

- 在 Azure 门户中，打开只读副本的“概述”页。 选择“删除” 。
 
也可以在“复制”窗口中遵循以下步骤删除只读副本：

1. 在 Azure 门户中，选择超大规模 (Citus) 主服务器组。

2. 在“服务器组”菜单的“服务器组管理”下，选择“复制” 。

3. 选择要删除的只读副本。
 
4. 选择“删除副本”。
 
5. 输入要删除的副本的名称。 选择“删除”以确认删除该副本。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Database for PostgreSQL - 超大规模 (Citus) 中的只读副本](concepts-hyperscale-read-replicas.md)。
