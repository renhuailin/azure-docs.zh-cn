---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d9a313353c7fef2954c4696e029483652162b582
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595676"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 文件同步应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |为指定的存储同步服务资源创建专用终结点，可以从组织网络的专用 IP 地址空间中寻址存储同步服务资源，而不是通过可通过 Internet 访问的公共终结点。 单独创建专用终结点并不会禁用公共终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[为 Azure 文件同步配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |为指示的存储同步服务资源部署专用终结点。 这样就可以从组织网络的专用 IP 地址空间内部（而不是通过可从 Internet 访问的公共终结点）对存储同步服务资源进行寻址。 存在一个或多个单独的专用终结点不会禁用公共终结点。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[修改 - 将 Azure 文件同步配置为禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |组织策略已禁用 Azure 文件同步的可通过 Internet 访问的公共终结点。 仍可以通过存储同步服务的专用终结点来访问该服务。 |修改，已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[应禁用对 Azure 文件同步进行公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |禁用公共终结点可以仅限发往组织网络中已批准的专用终结点的请求能够访问存储同步服务资源。 允许向公共终结点发出请求不会固有地造成安全问题，但你可能出于满足法规、法律或组织政策要求的原因而希望禁用公共终结点。 可以通过将资源的 incomingTrafficPolicy 设置为 AllowVirtualNetworksOnly 来禁用存储同步服务的公共终结点。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
