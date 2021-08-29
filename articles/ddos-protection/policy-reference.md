---
title: Azure DDoS 防护标准的内置策略定义
description: 列出 Azure DDoS 防护标准的 Azure Policy 内置策略定义。 这些内置的策略定义提供了管理 Azure 资源的常用方法。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 7c709849dfd2c9d3950d5f347a8a4cef7f3de101
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607409"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure DDoS 防护标准的 Azure Policy 内置定义

此页是 Azure DDoS 防护标准的 [Azure Policy](../governance/policy/overview.md) 内置策略定义的索引。 有关其他服务的其他 Azure Policy 内置定义，请参阅 [Azure Policy 内置定义](../governance/policy/samples/built-in-policies.md)。

每个内置策略定义链接（指向 Azure 门户中的策略定义）的名称。 使用“版本”列中的链接查看 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy)上的源。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS 保护标准

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[虚拟网络应受 Azure DDoS 防护标准保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|使用 Azure DDoS 防护标准来保护虚拟网络免受容量耗尽攻击和协议攻击。 有关详细信息，请访问 [https://aka.ms/ddosprotectiondocs](./ddos-protection-overview.md)。|Modify、Audit、Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[公共 IP 地址应启用 Azure DDoS 防护标准的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|在诊断设置中启用公共 IP 地址的资源日志，以便流式传输到 Log Analytics 工作区。 详细了解利用通知、报告和流日志降低 DDoS 攻击所采取的攻击流量和操作。|AuditIfNotExists、DeployIfNotExists、Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy)中查看这些内置项。
- 查看 [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)。
- 查看[了解策略效果](../governance/policy/concepts/effects.md)。
