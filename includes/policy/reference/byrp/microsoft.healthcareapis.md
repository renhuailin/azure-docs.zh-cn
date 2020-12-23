---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 95aa94f8e1459459a941c96e0d06d13d76a38879
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986380"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[适用于 FHIR 的 Azure API 应使用客户管理的密钥 (CMK) 来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |如果这是法规或合规性要求，请使用客户托管的密钥来控制在 Azure API for FHIR 中存储的数据的静态加密。 客户托管密钥还提供双重加密，方法是在使用服务托管密钥完成的默认加密层上添加另一层加密。 |audit、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[适用于 FHIR 的 Azure API 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |适用于 FHIR 的 Azure API 应该至少有一个批准的专用终结点连接。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS 不应允许每个域都能访问 API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 API for FHIR。 为了保护 API for FHIR，请删除所有域的访问权限，并显式定义允许连接的域。 |audit、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
