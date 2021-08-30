---
title: 使用 Active Directory 授权访问 Blob
titleSuffix: Azure Storage
description: 使用 Azure Active Directory (Azure AD) 授权访问 Azure Blob。 分配 Azure 角色以授予访问权限。 使用 Azure AD 帐户访问数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f70b876bd76eaf2fb7f8ca18cf3dabb7e66bf9b5
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733794"
---
# <a name="authorize-access-to-blobs-using-azure-active-directory"></a>使用 Azure Active Directory 授权访问 Blob

Azure 存储支持使用 Azure Active Directory (Azure AD) 来授权请求 Blob 数据。 可以通过 Azure AD 使用 Azure 基于角色的访问控制 (Azure RBAC) 授予对安全主体的访问权限，该安全主体可能是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 然后可以使用令牌来授权对 Blob 服务发出请求。

与共享密钥授权相比，使用 Azure AD 对针对 Azure 存储的请求进行授权提供了更高的安全性和易用性。 Microsoft 建议在可能的情况下对 Blob 应用程序使用 Azure AD 授权，以确保使用所需的最低权限进行访问。

可以针对所有公共区域和国家/地区云的所有常规用途帐户和 Blob 存储帐户使用 Azure AD 进行授权。 仅通过 Azure 资源管理器部署模型创建的存储帐户支持 Azure AD 授权。

Blob 存储还支持创建通过 Azure AD 凭据签名的共享访问签名 (SAS)。 有关详细信息，请参阅[向具有共享访问签名的数据授予有限的访问权限](../common/storage-sas-overview.md)。

## <a name="overview-of-azure-ad-for-blobs"></a>Azure AD Blob 概述

当某个安全主体（用户、组或应用程序）尝试访问 Blob 资源时，除非该 Blob 可供匿名访问，否则必须为请求授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 接下来，将该令牌作为请求的一部分传递给 Blob 服务，服务将使用它来授权访问指定的资源。

身份验证步骤要求应用程序在运行时请求 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure Functions 应用）中运行，则可以使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)访问 Blob 数据。 若要了解如何授权托管标识向 Azure Blob 服务发出的请求，请参阅[使用 Azure Active Directory 和 Azure 资源的托管标识授权访问 Blob](../common/storage-auth-aad-msi.md)。

授权步骤要求将一个或多个 Azure 角色分配给安全主体。 Azure 存储提供包含用于 Blob 数据的通用权限集的 Azure 角色。 分配给安全主体的角色确定了该主体拥有的权限。 若要详细了解如何分配 Azure 角色用于访问 Blob，请参阅[分配用于访问 Blob 数据的 Azure 角色](../blobs/assign-azure-role-data-access.md)。

向 Azure Blob 服务发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行访问授权。 若要了解如何请求访问令牌并使用它来授权请求 Blob 数据，请参阅[从 Azure 存储应用程序使用 Azure AD 授权访问 Azure 存储](../common/storage-auth-aad-app.md)。

## <a name="assign-azure-roles-for-access-rights"></a>分配 Azure 角色以授予访问权限

Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 存储定义了一组 Azure 内置角色，其中包含用于访问 Blob 数据的通用权限集。 还可以定义自定义角色来访问 Blob 数据。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="resource-scope"></a>资源范围

向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

可在以下级别范围内限定对 Azure Blob 资源的访问权限，从最小范围开始：

- **单个容器。** 在此范围内，角色分配将应用于容器中的所有 blob，以及容器属性和元数据。
- **存储帐户。** 在此范围内，角色分配将应用于所有容器及其 blob。
- **资源组。** 在此范围内，角色分配适用于资源组内的所有存储帐户中的所有容器。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有容器。
- **管理组。** 在此范围内，角色分配将应用于管理组中所有订阅的所有资源组中的所有存储帐户中的所有容器。

有关 Azure RBAC 角色分配范围的详细信息，请参阅[了解 Azure RBAC 的范围](../../role-based-access-control/scope-overview.md)。

### <a name="azure-built-in-roles-for-blobs"></a>用于 Blob 的 Azure 内置角色

Azure RBAC 提供多个内置角色，用于通过 Azure AD 和 OAuth 授权访问 Blob 数据。 某些角色可在 Azure 存储中提供数据资源权限，例如：

- [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于为 Azure Data Lake Storage Gen2 设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../../storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用来授予对 Blob 存储资源的只读权限。
- [存储 Blob 代理](../../role-based-access-control/built-in-roles.md#storage-blob-delegator)：获取用户委派密钥，用于创建使用 Azure AD 凭据为容器或 Blob 签名的共享访问签名。

若要了解如何将 Azure 内置角色分配给安全主体，请参阅[分配用于访问 Blob 数据的 Azure 角色](../blobs/assign-azure-role-data-access.md)。 若要了解如何列出 Azure RBAC 角色及其权限，请参阅[列出 Azure 角色定义](../../role-based-access-control/role-definitions-list.md)。

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](../../role-based-access-control/role-definitions.md#management-and-data-operations)。 若要了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

只有为数据访问显式定义的角色才允许安全主体访问 Blob 数据。 内置角色（例如“所有者”、“参与者”和“存储帐户参与者”）允许安全主体管理存储帐户，但不通过 Azure AD 提供对该帐户内的 blob 数据的访问权限。 但是，如果角色包括 Microsoft.Storage/storageAccounts/listKeys/action，则分配了该角色的用户可以使用帐户访问密钥通过共享密钥授权来访问存储帐户中的数据。 有关详细信息，请参阅[选择如何在 Azure 门户中授予对 blob 数据的访问权限](../../storage/blobs/authorize-data-operations-portal.md)。

要详细了解数据服务和管理服务的 Azure 存储的 Azure 内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../../role-based-access-control/built-in-roles.md#storage)的“存储”部分。 此外，若要了解 Azure 中提供权限的不同类型的角色，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> Azure 角色分配最多需要 30 分钟时间来进行传播。

### <a name="access-permissions-for-data-operations"></a>数据操作访问权限

若要详细了解有关调用特定 Blob 服务操作所需的权限，请参阅[用于调用数据操作的权限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)。

## <a name="access-data-with-an-azure-ad-account"></a>使用 Azure AD 帐户访问数据

可以使用用户的 Azure AD 帐户或使用帐户访问密钥（共享密钥授权）来授权通过 Azure 门户、PowerShell 或 Azure CLI 访问 Blob 数据。

### <a name="data-access-from-the-azure-portal"></a>通过 Azure 门户访问数据

Azure 门户可以使用 Azure AD 帐户或帐户访问密钥来访问 Azure 存储帐户中的 Blob 数据。 Azure 门户使用哪种授权方案取决于分配给你的 Azure 角色。

当你尝试访问 Blob 数据时，Azure 门户首先会检查你是否分配有一个包含“Microsoft.Storage/storageAccounts/listkeys/action”的 Azure 角色。 如果你拥有包含此操作的角色，则 Azure 门户将使用帐户密钥通过共享密钥授权来访问 Blob 数据。 如果你不拥有包含此操作的角色，则 Azure 门户会尝试使用你的 Azure AD 帐户访问数据。

若要使用 Azure AD 帐户通过 Azure 门户访问 Blob 数据，需要拥有访问 Blob 数据的权限，另外还需要拥有在 Azure 门户中浏览存储帐户资源的权限。 Azure 存储提供的内置角色授予对 Blob 资源的访问权限，但不授予对存储帐户资源的权限。 出于此原因，访问门户还需要分配范围为存储帐户或更高级别的 Azure 资源管理器角色，例如[读取者](../../role-based-access-control/built-in-roles.md#reader)角色。 “读取者”角色授予限制性最高的权限，但也接受可授予存储帐户管理资源访问权限的其他 Azure 资源管理器角色。 若要详细了解如何分配权限，使用户能够使用 Azure AD 帐户在 Azure 门户中访问数据，请参阅[分配用于访问 Blob 数据的 Azure 角色](../blobs/assign-azure-role-data-access.md)。

当你导航到容器时，Azure 门户会指示当前正在使用哪种授权方案。 有关在门户中访问数据的详细信息，请参阅[选择如何在 Azure 门户中授权访问 Blob 数据](../blobs/authorize-data-operations-portal.md)。

### <a name="data-access-from-powershell-or-azure-cli"></a>通过 PowerShell 或 Azure CLI 访问数据

Azure CLI 和 PowerShell 支持使用 Azure AD 凭据登录。 登录后，会话将在这些凭据下运行。 有关详细信息，请参阅以下文章之一：

- [选择如何使用 Azure CLI 授权 Blob 数据访问](authorize-data-operations-cli.md)
- [使用 Azure AD 凭据运行 PowerShell 命令以访问 blob 数据](authorize-data-operations-powershell.md)

## <a name="next-steps"></a>后续步骤

- [授权访问 Azure 存储中的数据](../common/authorize-data-access.md)
- [分配 Azure 角色以访问 blob 数据](assign-azure-role-data-access.md)
