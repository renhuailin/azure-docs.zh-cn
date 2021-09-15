---
title: 适用于资源管理器的 Azure Defender - 优点和功能
description: 了解适用于资源管理器的 Azure Defender 的优点和功能
author: memildin
ms.author: memildin
ms.date: 07/14/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 38eeaa02bf34f27580b29662f75455358c83ccb7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732560"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>适用于资源管理器的 Azure Defender 简介

[Azure 资源管理器](../azure-resource-manager/management/overview.md)是 Azure 的部署和管理服务。 它提供了一个管理层，用于在 Azure 帐户中创建、更新和删除资源。 部署后，可以使用访问控制、锁和标记等管理功能来保护和组织资源。

云管理层是连接到所有云资源的关键服务。 因此，它也是攻击者的潜在目标。 因此，建议安全运营团队密切监视资源管理层。 

无论操作是通过 Azure 门户、Azure REST API、Azure CLI 还是其他 Azure 编程客户端执行的，适用于资源管理器的 Azure Defender 都会自动监视组织中的资源管理操作。 Azure Defender 运行高级安全分析来检测威胁并向你发出有关可疑活动的警报。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|适用于资源管理器的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中所示的定价计费|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 中国世纪互联|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>适用于资源管理器的 Azure Defender 有哪些优点？

适用于资源管理器的 Azure Defender 可以防止出现以下问题：

- 可疑资源管理操作，例如来自恶意 IP 地址的操作、禁用在 VM 扩展中运行的反恶意软件和可疑脚本
- 使用开发工具包，例如 Microburst 或PowerZure
- 从 Azure 管理层到 Azure 资源数据平面的横向移动

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure 资源管理器概述示意图。":::

[警报参考页](alerts-reference.md#alerts-resourcemanager)上提供了由适用于资源管理器的 Azure Defender 提供的警报的完整列表。


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>如何调查来自适用于资源管理器的 Azure Defender 的警报

来自适用于资源管理器的 Azure Defender 的安全警报基于通过监视 Azure 资源管理器操作检测到的威胁。 Azure Defender 使用 Azure 资源管理器的内部日志源以及 Azure 活动日志（Azure 中的平台日志，提供对订阅级别事件的见解）。

详细了解 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。

调查来自适用于资源管理器的 Azure Defender 的安全警报：

1. 打开 Azure 活动日志。

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="如何打开 Azure 活动日志。":::

1. 将事件筛选为：
    - 警报中提到的订阅
    - 检测到的活动的时间范围
    - 相关的用户帐户（如果有）

1. 查找可疑活动。

> [!TIP]
> 为了获得更好且更丰富的调查体验，请按照[从 Azure 活动日志连接数据](../sentinel/connect-azure-activity.md)中所述，将 Azure 活动日志流式传输到 Azure Sentinel。



## <a name="next-steps"></a>后续步骤

本文介绍了适用于资源管理器的 Azure Defender。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)

如需相关材料，请参阅以下文章： 

- 安全警报可能是由安全中心生成的，也可能是由安全中心从其他安全产品接收的。 若要将所有这些警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。