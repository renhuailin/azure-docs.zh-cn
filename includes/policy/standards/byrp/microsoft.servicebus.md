---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c58308a7dc9426c7af7b29ab26f9b9809e27dd1c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123104126"
---
## <a name="azure-security-benchmark"></a>Azure 安全基准

[Azure 安全基准](/security/benchmark/azure/introduction)提供有关如何在 Azure 上保护云解决方案的建议。 若要查看此服务如何完全映射到 Azure 安全基准，请参阅 [Azure 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - Azure 安全基准](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|日志记录和威胁检测 |LT-4 |为 Azure 资源启用日志记录 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure 安全基准 v1

[Azure 安全基准](/security/benchmark/azure/introduction)提供有关如何在 Azure 上保护云解决方案的建议。 若要查看此服务如何完全映射到 Azure 安全基准，请参阅 [Azure 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - Azure 安全基准](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|日志记录和监视 |2.3 |为 Azure 资源启用审核日志记录 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure 基础基准检验 1.3.0

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - CIS Microsoft Azure 基础基准 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md)。
有关此符合性标准的详细信息，请参阅 [CIS Microsoft Azure 基础基准](https://www.cisecurity.org/benchmark/azure/)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|日志记录和监视 |5.3 |确保已为所有支持诊断日志的服务启用了诊断日志。 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="fedramp-high"></a>FedRAMP 高

若要查看各项 Azure 服务可用的 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - FedRAMP High](../../../../articles/governance/policy/samples/fedramp-high.md)。
有关此合规性标准的详细信息，请参阅 [FedRAMP High](https://www.fedramp.gov/)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|访问控制 |AC-4 |信息流强制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 |远程访问 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 (1) |自动化监视/控制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|审核和责任 |AU-6 (4) |集中评审和分析 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-6 (5) |集成/扫描和监视功能 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-12 |审核生成 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-12 (1) |系统范围/时间相关的审核线索 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|系统和通信保护 |SC-7 |边界保护 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-7 (3) |接入点 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-12 |加密密钥建立和管理 |[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |

## <a name="fedramp-moderate"></a>FedRAMP 中等

若要查看各项 Azure 服务可用的 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - FedRAMP Moderate](../../../../articles/governance/policy/samples/fedramp-moderate.md)。
有关此合规性标准的详细信息，请参阅 [FedRAMP Moderate](https://www.fedramp.gov/)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|访问控制 |AC-4 |信息流强制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 |远程访问 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 (1) |自动化监视/控制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|审核和责任 |AU-12 |审核生成 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|系统和通信保护 |SC-7 |边界保护 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-7 (3) |接入点 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-12 |加密密钥建立和管理 |[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
有关此合规性标准的详细信息，请参阅 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|审核日志 |1208.09aa3System.1 - 09.aa |对于管理活动、系统和应用程序启动/关闭/错误、文件更改和安全策略更改，保留审核日志。 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>限制性的新西兰 ISM

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - 限制性的新西兰 ISM](../../../../articles/governance/policy/samples/new-zealand-ism.md)。
有关此合规性标准的详细信息，请参阅[限制性的新西兰 ISM](https://www.nzism.gcsb.govt.nz/)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|访问控制和密码 |AC-17 |16.6.9 要记录的事件 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|网关安全性 |GS-2 |19.1.11 使用网关 |[应从服务总线命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |

## <a name="nist-sp-800-53-rev-4"></a>NIST SP 800-53 修订版 4

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - NIST SP 800-53 Rev. 4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)。
有关此符合性标准的详细信息，请参阅 [NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|访问控制 |AC-4 |信息流强制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 |远程访问 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 (1) |自动化监视/控制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|审核和责任 |AU-6 (4) |集中评审和分析 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-6 (5) |集成/扫描和监视功能 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-12 |审核生成 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-12 (1) |系统范围/时间相关的审核线索 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|系统和通信保护 |SC-7 |边界保护 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-7 (3) |接入点 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-12 |加密密钥建立和管理 |[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |

## <a name="nist-sp-800-53-rev-5"></a>NIST SP 800-53 Rev. 5

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - NIST SP 800-53 Rev. 5](../../../../articles/governance/policy/samples/nist-sp-800-53-r5.md)。
有关此符合性标准的详细信息，请参阅 [NIST SP 800-53 Rev. 5](https://nvd.nist.gov/800-53)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|访问控制 |AC-4 |信息流强制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 |远程访问 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|访问控制 |AC-17 (1) |监视和控制 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|审核和责任 |AU-6 (4) |集中评审和分析 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-6 (5) |审核记录的集成分析 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-12 |审核记录生成 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|审核和责任 |AU-12 (1) |系统范围和时间相关的审核跟踪 |[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|系统和通信保护 |SC-7 |边界保护 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-7 (3) |接入点 |[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|系统和通信保护 |SC-12 |加密密钥建立和管理 |[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |

