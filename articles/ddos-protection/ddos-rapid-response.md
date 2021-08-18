---
title: Azure DDoS 快速响应
description: 了解如何在主动攻击期间引入 DDoS 专家来获取专门的支持。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 847cd81886a12d5a8d414181e2919b43aa607228
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732922"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS 快速响应

在主动攻击期间，Azure DDoS 防护标准客户有权联系 DDoS 快速响应 (DRR) 团队，该团队可在攻击过程中帮助进行攻击调查，并且可在攻击之后帮助进行分析。

## <a name="prerequisites"></a>先决条件

- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准防护计划](manage-ddos-protection.md)。

## <a name="when-to-engage-drr"></a>何时联络 DRR

应该只是在出现以下情况时才联络 DRR： 

- 在 DDoS 攻击期间，发现受保护的资源性能严重下降，或资源不可用。 
- 你认为资源受到 DDoS 攻击，但 DDoS 防护服务不能有效地缓解攻击。
- 规划的某个病毒事件导致网络流量显著增加。
- 针对具有关键业务影响的攻击。

## <a name="engage-drr-during-an-active-attack"></a>在主动攻击期间联络 DRR

1. 在从 Azure 门户中创建新支持请求时，选择为“技术”作为“问题类型”。
2. 选择“DDOS 防护”作为“服务” 。
3. 在资源下拉菜单中选择资源。 你必须选择链接到受 DDoS 防护标准保护的虚拟网络的 DDoS 计划才能联络 DRR。

    ![选择资源](./media/ddos-rapid-response/choose-resource.png)

4. 在下一个“问题”页面上，选择“A - 严重影响”作为“严重性”，选择为“受到攻击”作为“问题类型”  。

    ![严重性和问题类型](./media/ddos-rapid-response/severity-and-problem-type.png)

5. 完成其他详细信息并提交该支持请求。

DDR 会遵循 Azure Rapid Response 支持模型。 有关快速响应的详细信息，请参阅[支持范围和响应能力](https://azure.microsoft.com/support/plans/response/)。

若要详细了解，请参阅 [DDoS 防护标准文档](./ddos-protection-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何[通过模拟进行测试](test-through-simulations.md)。
- 了解如何[查看和配置 DDoS 防护遥测](telemetry.md)。
- 了解如何[查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)。
