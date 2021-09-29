---
title: 环境的安全警报
description: 本文介绍了“如何查看 DevTest Labs 中环境的安全警报，并采取适当的措施”。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 437117e29ac09e52d2cd15740d60d942170b9c0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654188"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs 中环境的安全警报
作为实验室用户，用户现在可以查看实验室环境的 Azure Security Center 警报。 安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。 [进一步了解 Azure Security Center 内的安全警报](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>先决条件
目前，只能查看部署到实验室的“平台即服务” (PaaS) 环境的安全警报。 若要测试或使用此功能，[请将环境部署到实验室](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>查看环境的安全警报

1. 在实验室的主页上，选择左侧菜单上的“**安全警报**”。 应会看到安全警报（高、中和低）的数目。 有关详细信息，请参阅“[如何对警报进行分类](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)”。

    ![安全警报-概述](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 右键单击最后一列中的三个点 (...)，然后选择“**查看安全警报**”。 

    ![显示安全警报页的屏幕截图，其中已选择“查看安全警报”。](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 用户将看到有关警报和顾问建议的更多详细信息。 有关详细信息，请参阅“[管理和响应 Azure Security Center 的安全警报](../security-center/security-center-managing-and-responding-alerts.md)”。

    ![查看安全警报](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>后续步骤
若要了解有关环境的详细信息，请参阅以下文章：

- [使用 Azure Resource Manager 模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
