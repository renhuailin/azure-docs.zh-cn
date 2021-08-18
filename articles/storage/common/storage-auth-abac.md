---
title: 使用 Azure 角色分配条件授予对 Blob 的访问权限（预览版）
titleSuffix: Azure Storage
description: 使用 Azure 角色分配条件和 Azure 基于属性的访问控制 (Azure ABAC) 授权访问 Azure Blob。 使用存储属性定义角色分配条件。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 355eaa0c7e907dea1e1fe88e0640d3b09463fa09
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729013"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>使用 Azure 角色分配条件授予对 Blob 的访问权限（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

基于属性的访问控制 (ABAC) 是一个授权策略，根据与安全主体、资源、请求和环境关联的属性定义访问级别。 Azure ABAC 建立在 Azure 基于角色的访问控制 (Azure RBAC) 基础之上，它在现有的标识和访问管理 (IAM) 系统中[为 Azure 角色分配添加条件](../../role-based-access-control/conditions-overview.md)。 此预览版包括 Blob 和 Data Lake Storage Gen2 上的角色分配条件的支持。 它使你能够基于资源和请求属性创建角色分配条件。

## <a name="overview-of-conditions-in-azure-storage"></a>Azure 存储中的条件概述

Azure 存储允许[使用 Azure Active Directory](authorize-data-access.md) (Azure AD) 来授权对 Blob 和队列存储的请求。 Azure AD 使用 Azure RBAC 授予对受保护资源的访问权限。 Azure 存储定义了一组 Azure [内置角色](../../role-based-access-control/built-in-roles.md#storage)，它们包含用于访问 Blob 和队列数据的通用权限集。 还可以定义具有选定权限集的自定义角色。 Azure 存储支持存储帐户或 Blob 容器的角色分配。

但是，在某些情况下，可能需要启用对存储资源的更精细访问，或简化存储资源的数百个角色分配。 可以针对 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 配置[角色分配条件](../../role-based-access-control/conditions-overview.md)以实现这些目标。 可以将条件用于[自定义角色](../../role-based-access-control/custom-roles.md)，或选择内置角色。 请注意，不支持通过[存储资源提供程序](/rest/api/storagerp)为管理[操作](../../role-based-access-control/role-definitions.md#actions)使用条件。

Blob 支持 Azure 存储中的条件。 可以将条件用于启用了[分层命名空间](../blobs/data-lake-storage-namespace.md) (HNS) 功能的帐户。 文件、队列和表目前不支持条件。

## <a name="supported-attributes-and-operations"></a>支持的属性和操作

在此预览版中，可以向内置角色或自定义角色添加条件。 使用自定义角色只能为用户授予必不可少的权限或数据操作。 此预览版中支持的内置角色包括[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)、[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)和[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。

如果使用基于 [Blob 索引标记](../blobs/storage-manage-find-blobs.md)的条件，则应使用“存储 Blob 数据所有者”，因为此角色包括标记操作的权限。

> [!NOTE]
> 使用分层命名空间的 Data Lake Storage Gen2 存储帐户不支持 Blob 索引标记。 不应在启用了 HNS 的存储帐户上使用索引标记创建角色分配条件。

[Azure 角色分配条件格式](../../role-based-access-control/conditions-format.md)允许在条件中使用 `@Resource` 或 `@Request` 属性。 `@Resource` 属性是指正在访问的存储资源（例如存储帐户、容器或 Blob）的现有属性。 `@Request` 属性是指存储操作请求中包含的属性。

有关每个 DataAction 支持的属性的完整列表，请参阅 [Azure 存储中 Azure 角色分配条件的操作和属性（预览版）](storage-auth-abac-attributes.md)。

## <a name="see-also"></a>另请参阅

- [Azure 存储中 Azure 角色分配条件的安全注意事项（预览版）](storage-auth-abac-security.md)
- [Azure 存储中 Azure 角色分配条件的操作和属性（预览版）](storage-auth-abac-attributes.md)
- [什么是 Azure 基于特性的访问控制 (Azure ABAC)（预览版）？](../../role-based-access-control/conditions-overview.md)