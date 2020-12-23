---
title: Azure DDoS 快速响应
description: 了解如何在主动攻击期间为特定支持人员提供 DDoS 专家。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 3655b117b641a6179bd6539aaf3f75af07f21a1a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992363"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS 快速响应

在主动访问期间，Azure DDoS 保护标准客户有权访问 DDoS 快速响应 (DRR) 团队，他们可以在攻击期间和攻击后进行攻击调查。

## <a name="prerequisites"></a>先决条件

- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md)。

## <a name="when-to-engage-drr"></a>何时参与 DRR

仅当以下情况时才应参与 DRR： 

- 在 DDoS 攻击期间，如果发现受保护资源的性能严重降级，或者资源不可用。 在配置监视器以检测资源可用性和性能问题时，查看上面的步骤2。
- 假设资源受到 DDoS 攻击，但 DDoS 防护服务不能有效地缓解攻击。
- 规划的某个病毒事件导致网络流量显著增加。
- 针对具有关键业务影响的攻击。

## <a name="engage-drr-during-an-active-attack"></a>在主动攻击期间 DRR

1. 在创建新的支持请求时从 Azure 门户中，选择 " **问题类型** " 作为 "技术"。
2. 选择 **服务** 作为 **DDOS 防护**。
3. 在资源下拉菜单中选择一个资源。 _你必须选择链接到受 DDoS 防护标准保护的虚拟网络的 DDoS 计划，以 DRR。_

    ![选择资源](./media/ddos-rapid-response/choose-resource.png)

4. 在下一个 **问题** 页面上，选择 **严重性** 作为关键影响，将 **问题类型** 选择为 "受攻击"。

    ![PSeverity 和问题类型](./media/ddos-rapid-response/severity-and-problem-type.png)

5. 完成其他详细信息并提交支持请求。

DRR 遵循 Azure Rapid Response 支持模型。 有关快速响应的详细信息，请参阅 [支持范围和响应能力](https://azure.microsoft.com/en-us/support/plans/response/) 。

若要了解详细信息，请参阅 [DDoS 保护标准文档](./ddos-protection-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何 [通过模拟进行测试](test-through-simulations.md)。
- 了解如何 [查看和配置 DDoS 保护遥测](telemetry-monitoring-alerting.md)。
- 了解如何 [配置 DDoS 攻击缓解报告和流日志](reports-and-flow-logs.md)。