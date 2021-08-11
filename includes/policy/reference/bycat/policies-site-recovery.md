---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f9e095d3cde03e2a29372732f4c482c685de636e
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114388256"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[预览版\]：配置 Azure 恢复服务保管库以使用专用 DNS 区域](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942bd215-1a66-44be-af65-6a1c0318dbe2) |使用专用 DNS 区域来替代专用终结点的 DNS 解析。 专用 DNS 区域会链接到虚拟网络，以解析到恢复服务保管库。 有关详细信息，请访问：[https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md)。 |DeployIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateDnsZones_DeployIfNotExist.json) |
|[\[预览版\]：在 Azure 恢复服务保管库上配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe95a8a5c-0987-421f-84ab-df4d88ebf7d1) |专用终结点可在源或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 将专用终结点映射到恢复服务保管库的站点恢复资源可以降低数据泄露风险。 若要使用专用链接，必须将托管服务标识分配给恢复服务保管库。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints](../../../../articles/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints.md)。 |DeployIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateEndpoints_DeployIfNotExist.json) |
|[\[预览版\]：恢复服务保管库应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11e3da8c-1d68-4392-badd-0ff3c43ab5b0) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 恢复服务保管库，可以减少数据泄漏风险。 在以下位置详细了解 Azure Site Recovery 的专用链接：[https://aka.ms/HybridScenarios-PrivateLink](../../../../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) 和 [https://aka.ms/AzureToAzure-PrivateLink](../../../../articles/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints.md)。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/RecoveryServices_SiteRecovery_PrivateEndpoint_Audit.json) |