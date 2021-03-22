---
title: Azure 安全中心的主仪表板或“概述”页
description: 了解安全中心概述页面的功能
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099771"
---
# <a name="azure-security-centers-overview-page"></a>Azure 安全中心的概述页面

打开 Azure 安全中心时，首先显示的是概述页面。 

此交互式仪表板提供了混合云工作负载安全状况的统一视图。 此外，它还显示安全警报、覆盖范围信息等等。

你可以选择该页上的任何元素以获取更多详细信息。

:::image type="content" source="media/overview-page/overview.png" alt-text="安全中心概述页面":::

## <a name="features-of-the-overview-page"></a>概述页面的功能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="安全中心概述页面的顶部栏":::

顶部菜单栏提供：
- **订阅** - 可以通过选择此按钮来查看和筛选订阅列表。 安全中心会调整显示信息，以反映所选订阅的安全状况。
- **新增功能** - 打开 [发行说明](release-notes.md)以便随时了解新功能、bug 修补程序和弃用的功能。
- 已连接的云帐户的 **高级编号**，以显示下方主磁贴中的信息背景。 以及评估的资源、可用建议和安全警报的数量。 选择评估的资源编号以访问[资产清单](asset-inventory.md)。 详细了解如何连接 [AWS 帐户](quickstart-onboard-aws.md)和 [GCP 项目](quickstart-onboard-gcp.md)。


页面中央有四个中心磁贴，每个磁贴都链接到专用面板以获取更多详细信息：
- **安全分数** - 安全中心会持续评估资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。 [了解详细信息](secure-score-security-controls.md)。
- **合规性** - 安全中心会对你的 Azure 环境进行持续的评估，以便了解你的合规性情况。 安全中心根据安全最佳做法分析混合云环境中的风险因素。 这些评估会从支持的一组标准映射到符合性控件。 [了解详细信息](security-center-compliance-dashboard.md)。
- **Azure Defender** - 这是集成到安全中心内的云工作负载保护平台 (CWPP)，用于为 Azure 和混合工作负载提供高级的智能保护。 该磁贴显示（当前所选订阅）已连接资源的覆盖范围和最新的警报，并按严重性进行颜色编码。 [了解详细信息](azure-defender.md)。
- **防火墙管理器** - 此磁贴显示 [Azure 防火墙管理器](../firewall-manager/overview.md)中的中心和网络的状态。 


“见解”窗格为环境提供自定义项，包括：
- 受攻击最多的资源
- 最可能增加安全分数的安全控件
- 受影响资源最多的活动建议
- Azure 安全中心专家最近撰写的博客文章

## <a name="next-steps"></a>后续步骤

此页面介绍了安全中心概述页面。 如需相关信息，请参阅：

- [利用资产清单和管理工具浏览和管理资源](asset-inventory.md)
- [Azure 安全中心的安全评分](secure-score-security-controls.md)