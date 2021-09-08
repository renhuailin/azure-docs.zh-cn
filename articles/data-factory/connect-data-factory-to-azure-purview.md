---
title: 将数据工厂连接到 Azure Purview
description: 了解如何将数据工厂连接到 Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/24/2021
ms.openlocfilehash: e38c990622806e5e769626acb84377fc468a25a2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966428"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>将数据工厂连接到 Azure Purview（预览）

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) 是一项统一的数据治理服务，可帮助你管理和治理本地、多云和服务型软件 (SaaS) 数据。 可以将数据工厂连接到 Azure Purview。 通过该连接，可以使用 Azure Purview 来捕获世系数据，以及发现和探索 Azure Purview 资产。

## <a name="connect-data-factory-to-azure-purview"></a>将数据工厂连接到 Azure Purview

有两种方法可以将数据工厂连接到 Azure Purview：

- [在数据工厂中连接到 Azure Purview 帐户](#connect-to-azure-purview-account-in-data-factory)
- [在 Azure Purview 中注册数据工厂](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>在数据工厂中连接到 Azure Purview 帐户

你需要拥有数据工厂的“所有者”或“参与者”角色才能连接到 Azure Purview 帐户 。

在数据工厂创作 UI 上建立连接：

1. 在 ADF 创作 UI 中转到“管理” -> “Azure Purview”，选择“连接到 Purview 帐户”。   

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="展示了如何注册 Purview 帐户的屏幕截图。":::

2. 选择“从 Azure 订阅中选择”或“手动输入” 。 从 Azure 订阅中，可以选择有权访问的帐户。

3. 连接后，即可在“Purview 帐户”选项卡中看到 Purview 帐户的名称。

Purview 连接信息存储在数据工厂资源中，如下所示。 若要以编程方式建立连接，可以更新数据工厂并添加 `purviewConfiguration` 设置。

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
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

### <a name="register-data-factory-in-azure-purview"></a>在 Azure Purview 中注册数据工厂

若要了解如何在 Azure Purview 中注册数据工厂，请参阅[如何连接 Azure 数据工厂和 Azure Purview](../purview/how-to-link-azure-data-factory.md)。

## <a name="set-up-authentication"></a>设置身份验证

数据工厂的托管标识用于对从数据工厂到 Purview 的世系数据推送操作进行身份验证。 

- 对于 2021 年 8 月 18 日或之后创建的 Purview 帐户，请向数据工厂的托管标识授予 Purview 根集合的“数据管理者”角色  。 详细了解 [Azure Purview 中的访问控制](../purview/catalog-permissions.md)和[添加角色并限制通过集合的访问](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)。

    在创作 UI 上将数据工厂连接到 Purview 时，ADF 会尝试自动添加此类角色分配。 如果你拥有 Purview 根集合的“集合管理员”角色，此操作将成功完成。

- 对于 2021 年 8 月 18 日之前创建的 Purview 帐户，请向数据工厂的托管标识授予 Purview 帐户的 Azure 内置 [Purview 数据管理者](../role-based-access-control/built-in-roles.md#purview-data-curator)角色 。 详细了解 [Azure Purview 中的访问控制 - 旧版权限](../purview/catalog-permissions.md#legacy-permission-guide)

    在创作 UI 上将数据工厂连接到 Purview 时，ADF 会尝试自动添加此类角色分配。 如果你拥有 Purview 帐户的 Azure 内置“所有者”或“用户访问管理员”角色，则此操作会成功完成。 

如果你拥有读取 Purview 角色分配信息的权限，但未被授予所需角色，那么可能会看到以下警告。 若要确保为管道世系推送正确设置连接，请转到 Purview 帐户，并检查是否向数据工厂的托管标识授予了“Purview 数据管理者”角色。 否则，请手动添加角色分配。

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="展示了在注册 Purview 帐户时出现的警告的屏幕截图。":::

## <a name="report-lineage-data-to-azure-purview"></a>向 Azure Purview 报告世系数据

将数据工厂连接到 Purview 帐户后，执行管道时，数据工厂会向 Purview 帐户推送世系信息。 有关受支持功能的详细信息，请参阅[支持的 Azure 数据工厂活动](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)。 有关端到端演练，请参阅[教程：将数据工厂世系数据推送到 Azure Purview](tutorial-push-lineage-to-purview.md)。

## <a name="discover-and-explore-data-using-purview"></a>使用 Purview 发现和探索数据

将数据工厂连接到 Purview 帐户后，可以使用数据工厂创作 UI 顶部中央的搜索栏来搜索数据并执行操作。 有关详细信息，请参阅[使用 Purview 发现和探索 ADF 中的数据](how-to-discover-explore-purview-data.md)。

## <a name="next-steps"></a>后续步骤

[教程：将数据工厂世系数据推送到 Azure Purview](tutorial-push-lineage-to-purview.md)

[使用 Purview 发现和探索 ADF 中的数据](how-to-discover-explore-purview-data.md)

[Azure Purview 数据目录世系用户指南](../purview/catalog-lineage-user-guide.md)