---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 611c2792162a152292c19333000465b1ebe9fbbc
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099997"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 管理服务应使用虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure 虚拟网络部署提供了增强的安全性和隔离，并允许你将 API 管理服务放置在不可经 Internet 路由的网络（你控制对其的访问权限）中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络，这样就能够访问网络中的和/或本地的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
