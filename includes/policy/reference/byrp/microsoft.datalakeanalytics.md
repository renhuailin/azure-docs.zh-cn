---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2a3070a4cebec9037d2f31bf6bef86f9bc7dbed5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123104198"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[将 Data Lake Analytics 的诊断设置部署到事件中心](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4daddf25-4823-43d4-88eb-2419eb6dcc08) |在创建或更新缺少 Data Lake Analytics 的诊断设置的任何 Data Lake Analytics 时，将此诊断设置流式部署到区域事件中心。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[将 Data Lake Analytics 的诊断设置部署到 Log Analytics 工作区](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd56a5a7c-72d7-42bc-8ceb-3baf4c0eae03) |创建或更新缺少此诊断设置的任何 Data Lake Analytics 时，部署 Data Lake Analytics 的诊断设置以流式传输到区域 Log Analytics 工作区。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[应启用 Data Lake Analytics 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
