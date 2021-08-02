---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 54f88c362f0870776972693d35c7a34d761c9f5b
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904364"
---
Azure RBAC 提供了许多内置角色，用于使用 Azure AD 和 OAuth 授予对 blob 数据的访问权限。 某些角色可在 Azure 存储中提供数据资源权限，例如：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于为 Azure Data Lake Storage Gen2 设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用来授予对 Blob 存储资源的只读权限。
- [存储 Blob 代理](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator)：获取用户委派密钥，用于创建使用 Azure AD 凭据为容器或 Blob 签名的共享访问签名。

若要了解如何列出 Azure RBAC 角色及其权限，请参阅[列出 Azure 角色定义](../articles/role-based-access-control/role-definitions-list.md)。

只有为数据访问明确定义的角色才允许安全主体访问 blob 数据。 内置角色（例如“所有者”、“参与者”和“存储帐户参与者”）允许安全主体管理存储帐户，但不通过 Azure AD 提供对该帐户内的 blob 数据的访问权限。 但是，如果角色包括 Microsoft.Storage/storageAccounts/listKeys/action，则分配了该角色的用户可以使用帐户访问密钥通过共享密钥授权来访问存储帐户中的数据。 有关详细信息，请参阅[选择如何在 Azure 门户中授予对 blob 数据的访问权限](../articles/storage/blobs/authorize-data-operations-portal.md)。

要详细了解数据服务和管理服务的 Azure 存储的 Azure 内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../articles/role-based-access-control/built-in-roles.md#storage)的“存储”部分。 此外，若要了解 Azure 中提供权限的不同类型的角色，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> Azure 角色分配最多需要 30 分钟时间来进行传播。
