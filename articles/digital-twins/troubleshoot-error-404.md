---
title: 'Azure 数字孪生请求失败，状态为: 404 找不到子域'
description: 'Azure 数字孪生“服务请求失败。 状态: 404 找不到子域”的原因和解决方法。'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: 774642e15b7d7371872c1ec97455c8f56f8b8789
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456078"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>服务请求失败。 状态: 404 找不到子域

本文介绍从 Azure 数字孪生服务请求收到 404 错误的原因和解决步骤。 

## <a name="symptoms"></a>症状

使用属于实例中不同 [Azure Active Directory (Azure AD) 租户](../active-directory/develop/quickstart-create-new-tenant.md)的服务主体或用户帐户来访问 Azure 数字孪生实例时，可能会发生此错误。 似乎向该标识分配了正确的[角色](concepts-security.md)，但 API 请求失败并出现错误状态 `404 Sub-Domain not found`。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

Azure 数字孪生要求所有身份验证用户属于 Azure 数字孪生实例所属于的相同 Azure AD 租户。

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 #1

若要解决此问题，可以让其他租户中的每个联合标识从 Azure 数字孪生实例的“主”租户请求令牌。 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>解决方法 #2

如果在代码中使用 `DefaultAzureCredential` 类，并且在获取令牌后继续遇到此问题，则可以在 `DefaultAzureCredential` 选项中指定主租户以阐明租户，即便身份验证默认为其他类型。

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的安全性和权限的详细信息：
* [Azure 数字孪生解决方案的安全性](concepts-security.md)
