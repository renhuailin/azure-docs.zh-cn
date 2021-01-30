---
title: 将数据工厂连接到 Azure Purview
description: 了解如何将数据工厂连接到 Azure 监控范围
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 36eac4c60e0d7fd54ec304f0f17c2ecb454f0629
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063399"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>将数据工厂连接到 Azure 监控范围 (预览) 
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何将数据工厂连接到 Azure 监控范围，以及如何报告 Azure 数据工厂活动的数据沿袭复制数据、数据流和执行 SSIS 包。


## <a name="connect-data-factory-to-azure-purview"></a>将数据工厂连接到 Azure 监控范围
Azure 监控范围是一项新的云服务，可供数据用户集中管理跨云和本地环境的数据管理数据。 你可以将数据工厂连接到 Azure 监控范围，并通过连接，使用 Azure 监控范围捕获复制、数据流和执行 SSIS 包的沿袭数据。 可以通过两种方式将数据工厂连接到 Azure 监控范围：
### <a name="register-azure-purview-account-to-data-factory"></a>将 Azure 监控范围帐户注册到数据工厂
1. 在 ADF 门户中，请参阅 **管理**  ->  **Azure 监控范围**。 选择“连接到 Purview 帐户”。 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="用于注册监控范围帐户的屏幕截图。":::
2. 可以选择“从 Azure 订阅中选择”或“手动输入” 。 从 Azure 订阅中，可以选择有权访问的帐户。 
3. 连接后，你应该能够在选项卡 **监控范围帐户** 中看到监控范围帐户的名称。 
4. 可以使用 Azure 数据工厂门户顶部中心的搜索栏来搜索数据。 

如果在将 Azure 监控范围帐户注册到数据工厂后在 Azure 数据因素门户中看到警告，请按照以下步骤解决此问题：

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="有关注册监控范围帐户的警告的屏幕截图。":::

1. 中转到 Azure 门户并找到你的数据工厂。 选择 "标记" 部分，并查看是否存在名为 **catalogUri** 的标记。 如果没有，请在 ADF 门户中断开连接并重新连接 Azure 监控范围帐户。

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="注册监控范围帐户的标记的屏幕截图。":::

2. 检查是否授予了将 Azure 监控范围帐户注册到数据工厂的权限。 请参阅 [如何连接 Azure 数据工厂和 Azure 监控范围](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>在 Azure 监控范围中注册数据工厂
有关如何在 Azure 监控范围中注册数据工厂的详细说明，请参阅 [如何连接 Azure 数据工厂和 Azure 监控范围](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory)。 

## <a name="report-lineage-data-to-azure-purview"></a>将沿袭数据报告给 Azure 监控范围
当客户在 Azure 数据工厂中运行 "复制"、"数据流" 或 "执行 SSIS 包" 活动时，客户可以获取依赖关系，并大致概述数据源和目标之间的整个工作流过程。
有关如何从 Azure 数据工厂收集沿袭的详细说明，请参阅 [数据工厂沿袭](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)。

## <a name="next-steps"></a>后续步骤
[目录沿袭用户指南](../purview/catalog-lineage-user-guide.md)

[教程：将数据工厂沿袭数据推送到 Azure 监控范围](turorial-push-lineage-to-purview.md)