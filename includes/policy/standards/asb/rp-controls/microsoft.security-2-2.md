---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/13/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d9b60dd87112d0ddc858a87d3475a314cecf721d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122264013"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[你的订阅应启用 Log Analytics 代理自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |为了监视安全漏洞和威胁，Azure 安全中心会从 Azure 虚拟机收集数据。 数据是使用 Log Analytics 代理收集的，该代理以前称为 Microsoft Monitoring Agent (MMA)，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到 Log Analytics 工作区以用于分析。 建议启用自动预配，将代理自动部署到所有受支持的 Azure VM 和任何新创建的 VM。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
