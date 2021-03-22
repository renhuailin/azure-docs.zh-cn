---
title: 升级到 Azure Defender - Azure 安全中心
description: 本快速入门介绍如何升级到安全中心的 Azure Defender，以提高安全性。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099346"
---
# <a name="quickstart-set-up-azure-security-center"></a>快速入门：设置 Azure 安全中心

Azure 安全中心为你的混合和多云工作负载提供统一的安全管理和威胁防护。 免费功能只能为 Azure 资源提供有限的安全性，而启用 Azure Defende 能将这些功能扩展到本地和其他云。 借助 Azure Defende，可以查找和修复安全漏洞、应用访问和应用程序控制来阻止恶意活动、使用分析和智能检测威胁，以及在受到攻击时迅速做出响应。 可以免费试用 Azure Defender。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

本快速入门部分将指导你完成启用 Azure 安全中心和 Azure Defender 的所有建议步骤。 如果完成了所有这些快速入门步骤，那么你便完成了以下任务：

> [!div class="checklist"]
> * 在 Azure 订阅中启用安全中心
> * 在 Azure 订阅中启用 Azure Defender
> * 设置自动数据收集
> * 为安全警报设置电子邮件通知
> * 将混合和多云计算机连接到 Azure

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

若要在订阅中启用 Azure Defende，必须拥有“订阅所有者”、“订阅参与者”或“安全管理员”角色。

## <a name="enable-security-center-on-your-azure-subscription"></a>在 Azure 订阅中启用安全中心

> [!TIP]
> 若要在管理组中的所有订阅上启用安全中心，请参阅[在多个 Azure 订阅上启用安全中心](onboard-management-group.md)。

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。

1. 从门户的菜单中，选择“安全中心”。 

    此时会打开安全中心的“概述”页面。

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="安全中心的“概述”仪表板" lightbox="./media/security-center-get-started/overview.png":::

    “安全中心 - 概述”提供统一的视图用于查看混合云工作负荷的安全局势，可让你发现和评估工作负荷的安全性，以及识别和缓解风险。 安全中心会自动免费启用以前尚未由你或其他订阅用户加入的所有 Azure 订阅。

可以通过选择“订阅”菜单项来查看和筛选订阅列表。 安全中心会调整显示信息，以反映所选订阅的安全状况。 

在首次启动安全中心后的几分钟内，可以看到：

- 有关如何改善已连接资源的安全性的“建议”。
- 当前正在由安全中心评估的资源的清单，以及每个资源的安全状况。

若要充分利用安全中心，请继续阅读快速入门部分的后续步骤。



## <a name="next-steps"></a>后续步骤
在本快速入门中，你已经启用了 Azure 安全中心。 下一步是启用 Azure Defender，以便为混合云工作负载实施统一的安全管理和威胁防护。

> [!div class="nextstepaction"]
> [快速入门：启用 Azure Defender](enable-azure-defender.md)