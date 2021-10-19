---
title: 使用 Active Directory 授权对表的访问（预览）
titleSuffix: Azure Storage
description: 使用 Azure Active Directory (Azure AD) 授权对 Azure 表的访问（预览）。 分配 Azure 角色以授予访问权限。 使用 Azure AD 帐户访问数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e0bf6ebac7826346089b82962963d3596d1d4a8f
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857904"
---
# <a name="authorize-access-to-tables-using-azure-active-directory-preview"></a>使用 Azure Active Directory 授权对表的访问（预览）

Azure 存储支持使用 Azure Active Directory (Azure AD) 授权对表数据的请求（预览）。 可以通过 Azure AD 使用 Azure 基于角色的访问控制 (Azure RBAC) 授予对安全主体的访问权限，该安全主体可能是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 然后，可使用令牌来授权针对表服务的请求。

与共享密钥授权相比，使用 Azure AD 对针对 Azure 存储的请求进行授权提供了更高的安全性和易用性。 Microsoft 建议在可能的情况下对表应用程序使用 Azure AD 授权，以确保使用所需的最低权限进行访问。

可以针对所有公共区域和国家/地区云的所有常规用途使用 Azure AD 进行授权。 仅通过 Azure 资源管理器部署模型创建的存储帐户支持 Azure AD 授权。

> [!IMPORTANT]
> 使用 Azure AD 授权访问表的功能目前以预览版提供。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview-of-azure-ad-for-tables"></a>Azure AD 表概述

当安全主体（用户、组或应用程序）尝试访问表资源时，须对请求进行授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 接下来，将该令牌作为请求的一部分传递给表服务，服务将使用它来授予对指定资源的访问权限。

身份验证步骤要求应用程序在运行时请求 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure Functions 应用）中运行，则可以使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)访问表。 若要了解如何授权由托管标识发出的请求，请参阅[使用 Azure 资源的托管标识授权访问表数据](authorize-managed-identity.md)。

授权步骤要求将一个或多个 Azure 角色分配给安全主体。 Azure 存储提供了 Azure 角色，这些角色涵盖了针对表数据的通用权限集。 分配给安全主体的角色决定该主体拥有的权限。 若要详细了解如何分配 Azure 角色用于访问表，请参阅[分配用于访问表数据的 Azure 角色](assign-azure-role-data-access.md)。

向 Azure 表服务发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行访问授权。 若要了解如何请求访问令牌并使用它来授权请求，请参阅[从 Azure 存储应用程序使用 Azure AD 授权对 Azure 存储的访问](../common/storage-auth-aad-app.md)。

## <a name="assign-azure-roles-for-access-rights"></a>分配 Azure 角色以授予访问权限

Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 存储定义了一组 Azure 内置角色，它们包含用于访问表数据的通用权限集。 还可以定义自定义角色来访问表数据。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="resource-scope"></a>资源范围

向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

可在以下级别限定对 Azure 表资源的访问范围，从最小的范围开始：

- 单个表。 在此范围内，角色分配应用于指定的表。
- **存储帐户。** 在此范围内，角色分配应用于帐户内的所有表。
- **资源组。** 在此范围内，角色分配应用于资源组中所有存储帐户内的所有表。
- **订阅。** 在此范围内，角色分配应用于订阅中所有资源组内的所有存储帐户中的所有表。
- **管理组。** 在此范围内，角色分配将应用于管理组中所有订阅的所有资源组中的所有存储帐户中的所有表。

有关 Azure RBAC 角色分配范围的详细信息，请参阅 [了解 Azure RBAC 的范围](../../role-based-access-control/scope-overview.md)。

### <a name="azure-built-in-roles-for-tables"></a>适用于表的 Azure 内置角色

Azure RBAC 提供内置角色，用于授权使用 Azure AD 和 OAuth 访问表数据。 提供针对 Azure 存储中表的权限的内置角色包括：

- [存储表数据参与者](../../role-based-access-control/built-in-roles.md#storage-table-data-contributor)：用于授予对表存储资源的读取/写入/删除权限。
- [存储表数据读取器](../../role-based-access-control/built-in-roles.md#storage-table-data-reader)：用于授予对表存储资源的只读权限。

若要了解如何将 Azure 内置角色分配给安全主体，请参见[分配用于访问表数据的 Azure 角色](assign-azure-role-data-access.md)。 若要了解如何列出 Azure RBAC 角色及其权限，请参阅[列出 Azure 角色定义](../../role-based-access-control/role-definitions-list.md)。

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](../../role-based-access-control/role-definitions.md#control-and-data-actions)。 若要了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

只有为数据访问显式定义的角色才允许安全主体访问表数据。 内置角色（例如“所有者”、“参与者”和“存储帐户参与者”）允许安全主体管理存储帐户，但不通过 Azure AD 提供对该帐户内的表数据的访问权限  。 但是，如果角色包括 Microsoft.Storage/storageAccounts/listKeys/action，则分配了该角色的用户可以使用帐户访问密钥通过共享密钥授权来访问存储帐户中的数据。

要详细了解数据服务和管理服务的 Azure 存储的 Azure 内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../../role-based-access-control/built-in-roles.md#storage)的“存储”部分。 此外，若要了解 Azure 中提供权限的不同类型的角色，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> Azure 角色分配最多需要 30 分钟时间来进行传播。

### <a name="access-permissions-for-data-operations"></a>数据操作访问权限

若要详细了解有关调用特定表服务操作所需的权限，请参见[用于调用数据操作所需的权限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)。

## <a name="next-steps"></a>后续步骤

- [授权访问 Azure 存储中的数据](../common/authorize-data-access.md)
- [分配用于访问表数据的 Azure 角色](assign-azure-role-data-access.md)