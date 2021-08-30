---
title: 在 Microsoft Azure Maps 中向用户授予基于角色的访问权限
titleSuffix: Azure Maps
description: 使用基于角色的访问控制，向用户授予对 Azure Maps 的授权
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: 22b3eaaff0989718a781b29549b4fab102673273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801516"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>向用户授予对 Azure Maps 基于角色的访问权限

通过将 Azure AD 组或安全主体分配到一个或多个 Azure Maps 角色定义，可授予 Azure 基于角色的访问控制 (Azure RBAC)。

若要查看 Azure Maps 的可用 Azure 角色定义，请参阅[查看内置的 Azure Maps 角色定义](../how-to-manage-authentication.md#view-built-in-azure-maps-role-definitions)。

若要详细了解如何将可用的 Azure Maps 角色分配给创建的托管标识或服务主体，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)

若要有效地管理大量用户对 Azure Maps 应用和资源的访问，请参阅 [Azure AD 组](../../active-directory/fundamentals/active-directory-manage-groups.md)。

>[!IMPORTANT]
>对于允许进行身份验证以访问应用程序的用户，必须先在 Azure AD 中创建用户。 有关详细信息，请参阅[使用 Azure AD 添加或删除用户](../../active-directory/fundamentals/add-users-azure-active-directory.md)。

若要了解如何有效地管理大型用户目录，请参阅 [Azure AD](../../active-directory/fundamentals/index.yml)。

> [!WARNING]
> Azure Maps 内置角色定义提供了一种范围很广的授权访问权限，可以访问许多 Azure Maps REST API。 若要将 API 访问权限限制为最小范围，请参阅[创建自定义角色定义并将系统分配的标识分配给自定义角色定义](../../role-based-access-control/custom-roles.md)。 这样即可将访问权限限制为应用程序访问 Azure Maps 所需的最少特权。
