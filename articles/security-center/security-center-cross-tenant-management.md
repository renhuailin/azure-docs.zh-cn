---
title: Azure 安全中心的跨租户管理 | Microsoft Docs
description: 了解如何在安全中心使用 Azure Lighthouse 设置跨租户管理，以管理多个租户的安全状况。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: e4207bbc045f451b45f8b5da42fba08b406caeca
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237565"
---
# <a name="cross-tenant-management-in-security-center"></a>安全中心的跨租户管理

跨租户管理使你可以利用 [Azure Lighthouse](../lighthouse/overview.md) 在安全中心查看和管理多个租户的安全状况。 从单个视图中高效管理多个租户，无需登录每个租户的目录。

- 服务提供商可以在其自己的租户中管理多个客户的资源安全状况。

- 具有多个租户的组织的安全团队可在一个位置集中查看和管理其安全状况。

## <a name="set-up-cross-tenant-management"></a>设置跨租户管理

[Azure 委派资源管理](../lighthouse/concepts/architecture.md)是 Azure Lighthouse 的关键组成部分之一。 使用 Azure Lighthouse 的文档（[将客户载入到 Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)）中的说明，通过将托管租户的资源访问权限委派给你自己的租户来设置跨租户管理。


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>跨租户管理如何在安全中心工作

可以跨多个租户查看和管理订阅，方法与在单个租户中管理多个订阅相同。

在顶部菜单栏中，单击筛选器图标，然后从每个租户的目录中选择要查看的订阅。

  ![筛选租户。](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

视图和操作基本相同。 下面是一些示例：

- **管理安全策略**：在一个视图中，使用 [策略](tutorial-security-policy.md)管理大量资源的安全状况，对安全建议采取措施，并收集和管理安全相关数据。
- **提高安全分数和改进合规性状况**：跨租户可见性使你能够查看所有租户的总体安全状况以及在何处以何种方式最大程度地提高每个租户的 [安全分数](secure-score-security-controls.md)和改进其 [合规性状况](security-center-compliance-dashboard.md)。
- **修正建议**：同时监视和修正来自不同租户的许多资源的 [建议](security-center-recommendations.md)。 然后，可以立即解决所有租户中风险最高的漏洞。
- **管理警报**：检测不同租户中的 [警报](security-center-alerts-overview.md)。 对不符合可操作[修正步骤](security-center-managing-and-responding-alerts.md)的资源执行操作。

- **管理高级云防御功能和更多功能**：管理各种威胁防护服务，例如 [实时 (JIT) VM 访问](security-center-just-in-time.md)、[自适应网络强化](security-center-adaptive-network-hardening.md)、[自适应应用程序控制](security-center-adaptive-application.md)等。
 
## <a name="next-steps"></a>后续步骤
本文介绍了如何在安全中心进行跨租户管理。 若要了解 Azure Lighthouse 如何简化使用多个 Azure AD 租户的企业中的跨租户管理，请参阅[企业应用场景中的 Azure Lighthouse](../lighthouse/concepts/enterprise.md)。