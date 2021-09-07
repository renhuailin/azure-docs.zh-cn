---
title: 将 Synapse 工作区连接到 Azure Purview 
description: 将 Synapse 工作区连接到 Azure Purview 帐户。
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 08/24/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 5cf2149591776b66a2bb646c8de23e0669b31f25
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033838"
---
# <a name="quickstartconnect-a-synapse-workspace-to-an-azure-purview-account"></a>快速入门：将 Synapse 工作区连接到 Azure Purview 帐户

在本快速入门中，你要将 Azure Purview 帐户注册到 Synapse 工作区。 通过该连接，可以发现 Azure Purview 资产，通过 Synapse 功能与这些资产进行交互，并将世系信息推送到 Purview。

可以在 Synapse 中执行以下任务：
- 使用顶部搜索框根据关键字找到 Purview 资产 
- 根据元数据、[世系](../../purview/catalog-lineage-user-guide.md)、注释了解数据 
- 将这些数据连接到具有链接服务或集成数据集的工作区 
- 使用 Synapse Apache Spark、Synapse SQL 和数据流分析这些数据集 
- 执行管道，并[将世系信息推送到 Purview](../../purview/how-to-lineage-azure-synapse-analytics.md)

## <a name="prerequisites"></a>必备知识 
- [Azure Purview 帐户](../../purview/create-catalog-portal.md) 
- [Synapse 工作区](../quickstart-create-workspace.md) 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>用于连接 Azure Purview 帐户的权限 

若要将 Azure Purview 帐户连接到 Synapse 工作区，需要在 Synapse 工作区中具有来自 Azure 门户 IAM 的“参与者”角色，并且需要具备该 Azure Purview 帐户的访问权限。 如需了解详细信息，请参阅 [Azure Purview 权限](../../purview/catalog-permissions.md)。

## <a name="connect-an-azure-purview-account"></a>连接 Azure Purview 帐户  

按照以下步骤连接 Azure Purview 帐户：

1. 转到  [https://web.azuresynapse.net](https://web.azuresynapse.net)，然后登录到 Synapse 工作区。 
2. 转到“管理” -> “Azure Purview”，选择“连接到 Purview 帐户”  。
3. 可以选择“从 Azure 订阅中选择”或“手动输入” 。 从 Azure 订阅中，可以选择有权访问的帐户。
4. 连接后，即可在“Azure Purview 帐户”选项卡中看到 Purview 帐户的名称。 

Purview 连接信息存储在 Synapse 工作区资源中，如下所示。 若要以编程方式建立连接，可以更新 Synapse 工作区并添加 `purviewConfiguration` 设置。

```json
{
    "name": "ContosoSynapseWorkspace",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

## <a name="set-up-authentication"></a>设置身份验证

Synapse 工作区的托管标识用于对从 Synapse 工作区到 Purview 的世系推送操作进行身份验证。

- 对于 2021 年 8 月 18 日或之后创建的 Purview 帐户，向 Synapse 工作区的托管标识授予 Purview 根集合的“数据管理者”角色  。 详细了解 [Azure Purview 中的访问控制](../../purview/catalog-permissions.md)和[添加角色并限制通过集合的访问](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)。

    将 Synapse 工作区连接到 Synapse Studio 中的 Purview 时，Synapse 会尝试自动添加此类角色分配。 如果你拥有 Purview 根集合的“集合管理员”角色，此操作将成功完成。

- 对于 2021 年 8 月 18 日之前创建的 Purview 帐户，向 Synapse 工作区的托管标识授予 Purview 帐户的 Azure 内置 [Purview 数据管理者](../../role-based-access-control/built-in-roles.md#purview-data-curator)角色 。 详细了解 [Azure Purview 中的访问控制 - 旧版权限](../../purview/catalog-permissions.md#legacy-permission-guide)。

    将 Synapse 工作区连接到 Synapse Studio 中的 Purview 时，Synapse 会尝试自动添加此类角色分配。 如果你拥有 Purview 帐户的 Azure 内置“所有者”或“用户访问管理员”角色，则此操作会成功完成 。

如果你拥有读取 Purview 角色分配信息的权限，但未被授予所需角色，那么可能会看到以下警告。 若要确保为管道世系推送正确设置连接，请转到 Purview 帐户，并检查是否向 Synapse 工作区的托管标识授予了“Purview 数据管理者”角色。 否则，请手动添加角色分配。

:::image type="content" source="./media/register-purview-account-warning.png" alt-text="展示了在注册 Purview 帐户时出现的警告的屏幕截图。":::

## <a name="report-lineage-to-azure-purview"></a>向 Azure Purview 报告世系

将 Synapse 工作区连接到 Purview 帐户后，执行管道时，Synapse 会向 Purview 帐户报告世系信息。 有关支持的详细功能和端到端演练，请参阅[来自 Azure Synapse Analytics 的元数据和世系](../../purview/how-to-lineage-azure-synapse-analytics.md)。

## <a name="discover-and-explore-data-using-purview"></a>使用 Purview 发现和探索数据

将 Synapse 工作区连接到 Purview 帐户后，可以使用 Synapse 工作区顶部中央的搜索栏来搜索数据并执行操作。 从[使用 Azure Purview 发现、连接和浏览 Synapse 中的数据](how-to-discover-connect-analyze-azure-purview.md)中了解详细信息。

## <a name="nextsteps"></a>后续步骤 

[使用 Azure Purview 发现、连接和浏览 Synapse 中的数据](how-to-discover-connect-analyze-azure-purview.md)

[来自 Azure Synapse Analytics 的元数据和世系](../../purview/how-to-lineage-azure-synapse-analytics.md)

[在 Azure Purview 中注册并扫描 Azure Synapse 资产](../../purview/register-scan-azure-synapse-analytics.md)

[从 Power BI 获取世系再转至 Azure Purview](../../purview/how-to-lineage-powerbi.md)

[连接 Azure Data Share 和 Azure Purview](../../purview/how-to-link-azure-data-share.md)