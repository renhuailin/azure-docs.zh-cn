---
title: 将数据工厂连接到 Azure Purview
description: 了解如何将数据工厂连接到 Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/10/2021
ms.openlocfilehash: 1d03eb65ebc976691ee76dc4dd208baa7445d92e
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201631"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>将数据工厂连接到 Azure Purview（预览）

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) 是一项统一的数据治理服务，可帮助你管理和治理本地、多云和服务型软件 (SaaS) 数据。 可以将数据工厂连接到 Azure Purview。 该连接让你可以使用 Azure Purview 来捕获世系数据，以及发现和探索 Azure Purview 资产。

## <a name="connect-data-factory-to-azure-purview"></a>将数据工厂连接到 Azure Purview

有两种方法可以将数据工厂连接到 Azure Purview：

- [在数据工厂中连接到 Azure Purview 帐户](#connect-to-azure-purview-account-in-data-factory)
- [在 Azure Purview 中注册数据工厂](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>在数据工厂中连接到 Azure Purview 帐户

若要建立连接，你需要拥有数据工厂的“所有者”或“参与者”角色。 

1. 在 ADF 创作 UI 中转到“管理” -> “Azure Purview”，选择“连接到 Purview 帐户”。   

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="展示了如何注册 Purview 帐户的屏幕截图。":::

2. 选择“从 Azure 订阅中选择”或“手动输入” 。 从 Azure 订阅中，可以选择有权访问的帐户。

3. 连接后，即可在“Purview 帐户”选项卡中看到 Purview 帐户的名称。

将数据工厂连接到 Purview 时，ADF UI 还会尝试在 Purview 帐户上授予数据工厂的托管标识“Purview 数据策展者”角色。 托管标识用于对从数据工厂到 Purview 的世系数据推送操作进行身份验证。 如果你拥有 Purview 帐户的“所有者”或“用户访问管理员”角色，则此操作会自动执行。  否则，你会看到如下所示的警告：

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="展示了在注册 Purview 帐户时出现的警告的屏幕截图。":::

若要解决此问题，请转到 Azure 门户 -> 你的 Purview 帐户 ->“访问控制(IAM)”，检查是否已将“Purview 数据策展者”角色授予数据工厂的托管标识。 否则，请手动添加角色分配。

### <a name="register-data-factory-in-azure-purview"></a>在 Azure Purview 中注册数据工厂

若要了解如何在 Azure Purview 中注册数据工厂，请参阅[如何连接 Azure 数据工厂和 Azure Purview](../purview/how-to-link-azure-data-factory.md)。 

## <a name="report-lineage-data-to-azure-purview"></a>向 Azure Purview 报告世系数据

将数据工厂连接到 Purview 帐户后，如果运行“复制”、“数据流”或“执行 SSIS 包”活动，则可获取数据流程创建的数据集之间的世系，大致了解数据源和数据目标之间的完整工作流程。 有关受支持功能的详细信息，请参阅[支持的 Azure 数据工厂活动](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)。 有关端到端演练，请参阅[教程：将数据工厂世系数据推送到 Azure Purview](tutorial-push-lineage-to-purview.md)。

## <a name="discover-and-explore-data-using-purview"></a>使用 Purview 发现和探索数据

将数据工厂连接到 Purview 帐户后，可以使用 Azure 数据工厂创作 UI 顶部中央的搜索栏来搜索数据并执行操作。 有关详细信息，请参阅[使用 Purview 发现和探索 ADF 中的数据](how-to-discover-explore-purview-data.md)。

## <a name="next-steps"></a>后续步骤

[教程：将数据工厂世系数据推送到 Azure Purview](tutorial-push-lineage-to-purview.md)

[使用 Purview 发现和探索 ADF 中的数据](how-to-discover-explore-purview-data.md)

[Azure Purview 数据目录世系用户指南](../purview/catalog-lineage-user-guide.md)