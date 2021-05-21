---
title: 将数据工厂连接到 Azure Purview
description: 了解如何将数据工厂连接到 Azure Purview
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727934"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>将数据工厂连接到 Azure Purview（预览）
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文将介绍如何将数据工厂连接到 Azure Purview，以及如何报告 Azure 数据工厂活动“复制数据”、“数据流”和“执行 SSIS 包”的数据世系。


## <a name="connect-data-factory-to-azure-purview"></a>将数据工厂连接到 Azure Purview
Azure Purview是一种新的云服务，供数据用户跨云和本地环境中的数据资产集中管理数据治理。 可以将数据工厂连接到 Azure Purview，此连接便于你使用 Azure Purview 来捕获“复制数据”、“数据流”和“执行 SSIS 包”的世系数据。 有两种方法可以将数据工厂连接到 Azure Purview：
### <a name="register-azure-purview-account-to-data-factory"></a>向数据工厂注册 Azure Purview 帐户
1. 在 ADF 门户中，依次转到“管理” -> “Azure Purview”。 选择“连接到 Purview 帐户”。 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="展示了如何注册 Purview 帐户的屏幕截图。":::
2. 可以选择“从 Azure 订阅中选择”或“手动输入” 。 从 Azure 订阅中，可以选择有权访问的帐户。 
3. 连接后，你应该能够在“Purview 帐户”选项卡中看到 Purview 帐户的名称。 
4. 可以使用 Azure 数据工厂门户顶部中间的搜索栏来搜索数据。 

如果在向数据工厂注册 Azure Purview 帐户后在 Azure 数据工厂门户中看到警告，请按以下步骤操作来修复此问题：

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="展示了在注册 Purview 帐户时出现的警告的屏幕截图。":::

1. 转到 Azure 门户，然后查找数据工厂。 选择“标记”部分，然后查看是否有名为 catalogUri 的标记。 如果没有，请在 ADF 门户中断开 Azure Purview 帐户连接并重新连接。

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="展示了关于注册 Purview 帐户的标记的屏幕截图。":::

2. 检查是否有权向数据工厂注册 Azure Purview 帐户。 请参阅[如何连接 Azure 数据工厂和 Azure Purview](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>在 Azure Purview 中注册数据工厂
若要了解如何在 Azure Purview 中注册数据工厂，请参阅[如何连接 Azure 数据工厂和 Azure Purview](../purview/how-to-link-azure-data-factory.md)。 

## <a name="report-lineage-data-to-azure-purview"></a>向 Azure Purview 报告世系数据
当客户在 Azure 数据工厂中运行“复制数据”、“数据流”或“执行 SSIS 包”活动时，客户可以获取数据源与目标之间的依赖关系，并大致了解它们之间的整个工作流过程。
若要了解如何从 Azure 数据工厂收集世系，请参阅[数据工厂世系](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)。

## <a name="next-steps"></a>后续步骤
[目录世系用户指南](../purview/catalog-lineage-user-guide.md)

[教程：将数据工厂世系数据推送到 Azure Purview](turorial-push-lineage-to-purview.md)