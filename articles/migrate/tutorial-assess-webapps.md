---
title: 评估 Web 应用以迁移到 Azure 应用服务的教程
description: 了解如何在 Azure Migrate 中创建 Azure 应用服务评估
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/28/2021
ms.openlocfilehash: 1bbe267aef27151b3ef70e88e0a8e85091d8a33d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781877"
---
# <a name="tutorial-assess-aspnet-web-apps-for-migration-to-azure-app-service"></a>教程：评估 ASP.NET Web 应用以迁移到 Azure 应用服务

在迁移到 Azure 的过程中，将评估本地工作负载，以衡量云的就绪性、确定风险以及估算成本和复杂性。
本文介绍如何使用“Azure Migrate: 发现和评估”工具来评估在 IIS Web 服务器上运行的已发现 ASP.NET Web 应用，以便准备迁移到 Azure 应用服务。

在本教程中，你将了解：

> [!div class="checklist"]
> * 运行基于 Web 应用配置数据的评估。
> * 查看 Azure 应用服务评估

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。 

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- 在遵循本教程评估要迁移到 Azure 应用服务的 Web 应用之前，请确保已[遵循此教程](tutorial-discover-vmware.md)使用 Azure Migrate 设备发现了要评估的 Web 应用
- 如果要在现有项目中试用此功能，请确保已完成本文中的[先决条件](how-to-discover-sql-existing-project.md)。

## <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 在“概述”页 >“服务器、数据库和 Web 应用”中，单击“发现、评估和迁移”  。
    :::image type="content" source="./media/tutorial-assess-webapps/discover-assess-migrate.png" alt-text="Azure Migrate 的“概述”页":::
2. 在“Azure Migrate: 发现和评估”中，单击“评估”并选择“Azure 应用服务”作为评估类型。  
    :::image type="content" source="./media/tutorial-assess-webapps/assess.png" alt-text="用于选择“Azure 应用服务”作为评估类型的下拉列表":::
3. 在“创建评估”中，可以看到预先选择的评估类型为“Azure 应用服务”，而发现源默认为“从 Azure Migrate 设备发现的服务器”。  
4. 单击“编辑”查看评估属性。
     :::image type="content" source="./media/tutorial-assess-webapps/assess-webapps.png" alt-text="可用于自定义评估属性的“编辑”按钮":::
5. 下面是 Azure 应用服务评估属性包含的内容：

    **属性** | **详细信息**
    --- | ---
    **目标位置** | 要迁移到的 Azure 区域。 Azure 应用服务配置和成本建议基于你指定的位置。
    需要隔离 | 如果希望你的 Web 应用在 Azure 数据中心的私有和专用环境中运行，请使用 Dv2 系列 VM，它具有更快的处理器、SSD 存储，并且内存与内核比是标准计划的两倍。
    **预留实例** | 指定预留实例，以便在评估的成本估算中将它们考虑在内。<br/><br/> 如果选择了预留实例选项，则不能指定“折扣(%)”。
    **产品/服务** | 注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 评估会估计该产品/服务的费用。
    **货币** | 帐户的计费货币。
    **折扣 (%)** | 基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
    **EA 订阅** | 指定将将企业协议 (EA) 订阅用于成本估算。 考虑适用于该订阅的折扣。 <br/><br/> 将预留实例和折扣 (%) 属性保留为默认设置。

    :::image type="content" source="./media/tutorial-assess-webapps/webapps-assessment-properties.png" alt-text="应用服务评估属性":::

1. 在“创建评估”中 > 单击“下一步”。
1. 在“选择要评估的服务器” > “评估名称”中，指定评估的名称 。
1. 在“选择或创建组”中，选择“新建”并指定组名称 。
1. 选择设备，然后选择要添加到该组的服务器。 然后，单击“下一步”。
1. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估 。
1. 创建评估后，转到“服务器、数据库和 Web 应用” > “Azure Migrate: 发现和评估”磁贴 > 通过单击磁贴顶部的“刷新”选项来刷新磁贴数据。 等待数据刷新。
    :::image type="content" source="./media/tutorial-assess-webapps/tile-refresh.png" alt-text="刷新发现和评估工具数据":::
1. 单击 Azure 应用服务评估旁的数字。
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-navigation.png" alt-text="导航到已创建的评估":::
1. 单击要查看的评估名称。

## <a name="review-an-assessment"></a>查看评估

**若要查看评估**：

1. 转到“服务器、数据库和 Web 应用” > “Azure Migrate: 发现和评估”，然后单击 Azure 应用服务评估旁边的数字。
2. 单击要查看的评估名称。
:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-summary.png" alt-text="应用服务评估概述":::
3. 查看评估摘要。 还可以编辑评估属性，或重新计算评估。

#### <a name="azure-app-service-readiness"></a>Azure 应用服务就绪情况

表示已评估的 Web 应用的分布。 可以向下钻取以了解有关在迁移到 Azure 应用服务之前可予以修正的迁移问题/警告的详细信息。 [了解详细信息](concepts-azure-webapps-assessment-calculation.md) 还可以查看推荐应用服务 SKU，并计划迁移到 Azure 应用服务。

#### <a name="azure-app-service-cost-details"></a>Azure 应用服务成本详细信息

[应用服务计划](/azure/app-service/overview-hosting-plans)会根据所用的计算资源量产生[费用](https://azure.microsoft.com/pricing/details/app-service/windows/)。

### <a name="review-readiness"></a>查看就绪情况

1. 单击“Azure 应用服务就绪情况”。
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-readiness.png" alt-text="Azure 应用服务就绪情况详细信息":::
1. 查看表中的 Azure 应用服务就绪情况列，了解评估的 Web 应用：
    1. 如果未找到兼容性问题，则会将目标部署类型的就绪情况标记为“就绪”。
    1. 如果存在不严重的兼容性问题，如不会阻止迁移到特定目标部署类型的降级或不受支持的功能，则就绪情况标记为“就绪(有条件)”（超链接），其中包含“警告”详细信息和建议的修正指导。
    1. 如果存在可能会阻止迁移到特定目标部署类型的任何兼容性问题，则就绪情况标记为“未就绪”，其中包含“问题”详细信息和建议的修正指导。
    1. 如果发现仍在进行中或 Web 应用存在任何发现问题，则就绪情况标记为“未知”，因为评估无法计算该 Web 应用的就绪情况。
1. 查看按以下对照表确定的推荐 Web 应用 SKU：

    需要隔离 | 预留实例 | 应用服务计划/SKU
    --- | --- | ---
    是  | 是 | I1
    是  | 否  | I1
    否  | 是 | P1v3
    否  | 否 | P1v2

    Azure 应用服务就绪情况 | 确定应用服务 SKU | 确定成本估算
    --- | --- | ---
    就绪  | 是 | 是
    就绪但有条件  | 是  | 是
    未准备就绪  | 否 | 否
    未知  | 否 | 否

1. 单击表中的应用服务计划超链接可查看应用服务计划详细信息（如计算资源）以及属于同一计划的其他 Web 应用。

### <a name="review-cost-estimates"></a>查看成本估算

评估摘要显示在应用服务中托管 Web 应用的估算每月成本。 在应用服务中，按应用服务计划而不是 Web 应用付费。 可将一个或多个应用配置为在相同的计算资源中（或相同的应用服务计划中）运行。 放入此应用服务计划的任何应用都在应用服务计划定义的这些计算资源中运行。
为了优化成本，Azure Migrate 评估将多个 Web 应用分配给每个建议的应用服务计划。 分配给每个计划实例的 Web 应用的数量如下表所示。

**应用服务计划** | 每个应用服务计划的 Web 应用数
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-cost.png" alt-text="成本详细信息":::

## <a name="next-steps"></a>后续步骤

[详细了解](concepts-azure-webapps-assessment-calculation.md)如何计算 Azure 应用服务评估。