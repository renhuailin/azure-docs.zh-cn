---
title: 快速入门：添加管理员入门
description: 在本教程中，你将了解如何将其他管理用户添加到工作区。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7a03715a89b5319e341d1704719b020e1b61ef1d
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219413"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>向 Synapse 工作区添加管理员

在本教程中，你将了解如何将管理员添加到 Synapse 工作区。 此用户全权控制整个工作区。

## <a name="overview"></a>概述

到目前为止，在本快速入门指南中，我们已重点讲解了你在工作区中可以执行的活动。 你在步骤 1 中创建了工作区，因此你是 Synapse 工作区的管理员。 现在，我们将另一位用户 Ryan (`ryan@contoso.com`) 设为管理员。 完成此操作后，Ryan 将能够执行你在工作区中可以执行的所有操作。

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC：工作区的“所有者”角色

1. 打开 Azure 门户并打开 Synapse 工作区。
1. 在左侧选择“访问控制(IAM)”。
1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。
1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | “所有者” |
    | 将访问权限分配到 | USER |
    | 成员 | ryan@contoso.com |

    ![Azure 门户中的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. 选择“保存”。 
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC：工作区的 Synapse“管理员”角色

在工作区为 `ryan@contoso.com` 分配 Synapse RBAC Synapse“管理员”角色。

1. 在 Synapse Studio 中打开工作区。
1. 在左侧，选择“管理”以打开管理中心。
1. 在“安全性”下选择“访问控制” 。
1. 选择 **添加** 。
1. 将“范围”设置为“工作区”。 
1. 将 `ryan@contoso.com` 添加为 Synapse“管理员”角色。 
1. 然后，选择“应用”。
 
## <a name="azure-rbac-role-assignments-on-the-workspaces-primary-storage-account"></a>Azure RBAC：在工作区的主存储帐户上分配角色

1. 在 Azure 门户中打开工作区的主存储帐户。
1. 在左侧选择“访问控制(IAM)”。
1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。
1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 1 | 所有者 |
    | 角色 2| Azure 存储 Blob 数据参与者|
    | 将访问权限分配到 | USER |
    | 成员 | ryan@contoso.com |

    ![Azure 门户中的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="dedicated-sql-pools-db_owner-role"></a>专用的 SQL 池：db_owner 角色

在工作区的每个专用 SQL 池上为 `ryan@contoso.com` 分配 db_owner 。

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](get-started.md)
* [创建工作区](quickstart-create-workspace.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)
