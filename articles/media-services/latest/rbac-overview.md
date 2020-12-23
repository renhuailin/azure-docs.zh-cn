---
title: 针对媒体服务帐户的 azure 基于角色的访问控制-Azure |Microsoft Docs
description: 本文介绍 azure 媒体服务帐户 (azure RBAC) 的基于角色的访问控制。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 8fba3db14c2a950dd230a4721841b4baa9f64636
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426805"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>用于媒体服务帐户的 azure RBAC)  (azure 基于角色的访问控制

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

目前，Azure 媒体服务不定义任何特定于服务的自定义角色。 若要获得媒体服务帐户的完全访问权限，客户可以使用内置的“所有者”或“参与者”角色。   这两个角色的主要区别是：所有者可以控制谁有权访问某个资源，而参与者则不可以。   也可使用内置的“读者”角色，但用户或应用程序将只能对媒体服务 API 进行读取访问。  

## <a name="design-principles"></a>设计原理

V3 API 的主要设计原则之一是使 API 更安全。 v3 API 不会在 **Get** 或 **List** 操作中返回机密或凭据。 在响应中，密钥始终为 null、空值或进行了净化。 用户需要调用单独的操作方法来获取机密或凭据。 “读者”角色  不能调用多项操作，例如 Asset.ListContainerSas、StreamingLocator.ListContentKeys、ContentKeyPolicies.GetPolicyPropertiesWithSecrets。 如果需要，可以使用单独的操作，在自定义角色中设置更精细的 Azure RBAC 安全权限。

若要列出媒体服务支持的操作，请执行以下代码：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[内置角色定义](../../role-based-access-control/built-in-roles.md)一文会确切地告知你该角色授予的权限。 

有关详细信息，请参阅以下文章：

- [经典订阅管理员角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [使用 REST API 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-rest.md)
- [媒体服务资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>后续步骤

- [使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)
- [使用媒体服务 .NET 获取内容密钥策略](get-content-key-policy-dotnet-howto.md)
