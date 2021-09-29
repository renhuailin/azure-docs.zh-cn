---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fbed6ca4208e7a4967684b5218feefd11561da60
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669185"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 管理服务应使用支持虚拟网络的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |有了 API 管理支持的 SKU，将服务部署到虚拟网络中就可以解锁高级 API 管理网络和安全功能，从而更全面地控制网络安全配置。 有关详细信息，请访问：[https://aka.ms/apimvnet](../../../../articles/api-management/api-management-using-with-vnet.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API 管理服务应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf73bd95-24da-4a4f-96b9-4e8b94b402bd) |若要提高 API 管理服务的安全性，请确保终结点不会公开到公共 Internet。 一些公共终结点由 API 管理服务公开，以支持用户方案，例如对管理 API 的直接访问、使用 Git 管理配置、自承载网关配置。 如果不使用这些功能中的任何一个，则应禁用相应的终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_PublicEndpoint_AuditIfNotExist.json) |
|[API 管理服务应使用虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure 虚拟网络部署提供了增强的安全性和隔离，并允许你将 API 管理服务放置在不可经 Internet 路由的网络（你控制对其的访问权限）中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络，这样就能够访问网络中的和/或本地的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[配置 API 管理服务以禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ca8c8ac-3a6e-493d-99ba-c5fa35347ff2) |若要提高 API 管理服务的安全性，请禁用公共终结点。 一些公共终结点由 API 管理服务公开，以支持用户方案，例如对管理 API 的直接访问、使用 Git 管理配置、自承载网关配置。 如果不使用这些功能中的任何一个，则应禁用相应的终结点。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_PublicEndpoint_DeployIfNotExist.json) |