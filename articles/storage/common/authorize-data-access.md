---
title: 授权数据操作
titleSuffix: Azure Storage
description: 了解授权访问 Azure 存储的各种方式，包括 Azure Active Directory、共享密钥授权或共享访问签名 (SAS)。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 490b0b109e4b89b01b748d2d6aa71b4477f8a8ef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560582"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>授权访问 Azure 存储中的数据

每次访问存储帐户中的数据时，客户端都会通过 HTTP/HTTPS 向 Azure 存储发出请求。 每个对安全资源的请求都必须经过授权，以便服务确保客户端具有访问数据所需的权限。

下表介绍了多个选项，Azure 存储提供这些选项，用于授权对资源的访问：

| Azure 项目 | 共享密钥（存储帐户密钥） | 共享访问签名 (SAS) | Azure Active Directory (Azure AD) | 本地 Active Directory 域服务 | 匿名公共读取访问权限 |
|--|--|--|--|--|--|
| Azure Blob | [支持](/rest/api/storageservices/authorize-with-shared-key/) | [支持](storage-sas-overview.md) | [支持](authorize-data-access.md) | 不支持 | [支持](../blobs/anonymous-read-access-configure.md) |
| Azure 文件存储 (SMB) | [支持](/rest/api/storageservices/authorize-with-shared-key/) | 不支持 | [仅支持 AAD 域服务](../files/storage-files-active-directory-overview.md) | [支持，凭据必须同步到 Azure AD](../files/storage-files-active-directory-overview.md) | 不支持 |
| Azure 文件存储 (REST) | [支持](/rest/api/storageservices/authorize-with-shared-key/) | [支持](storage-sas-overview.md) | 不支持 | 不支持 | 不支持 |
| Azure 队列 | [支持](/rest/api/storageservices/authorize-with-shared-key/) | [支持](storage-sas-overview.md) | [支持](authorize-data-access.md) | 不支持 | 不支持 |
| Azure 表 | [支持](/rest/api/storageservices/authorize-with-shared-key/) | [支持](storage-sas-overview.md) | [支持](../tables/authorize-access-azure-active-directory.md)（预览版） | 不支持 | 不支持 |

下面简要介绍每个授权选项：

- 用于 blob、队列和表的 Azure Active Directory (Azure AD) 集成。 Azure 提供 Azure 基于角色的访问控制 (Azure RBAC)，用于控制客户端对存储帐户中资源的访问权限。 Microsoft 建议尽可能使用 Azure AD 以获得最佳安全性和易用性。 有关 Azure AD 集成的详细信息，请参阅[授权访问 Azure 存储中的数据](authorize-data-access.md)。

- Azure 文件存储的 Azure Active Directory 域服务 (Azure AD DS) 身份验证。 Azure 文件存储支持通过 Azure AD DS 使用服务器消息块 (SMB) 进行基于标识的授权。 可以使用 Azure RBAC 对客户端对存储帐户中的 Azure 文件存储资源的访问进行细粒度控制。 有关使用域服务进行 Azure 文件存储身份验证的详细信息，请参阅[概述](../files/storage-files-active-directory-overview.md)。

- Azure 文件存储的本地 Active Directory 域服务（AD DS 或本地 AD DS）身份验证。 Azure 文件支持通过 AD DS 使用 SMB 进行基于标识的授权。 AD DS 环境可以托管在本地计算机中，也可以托管在 Azure VM 中。 支持使用 AD DS 凭据从加入域的计算机在本地或 Azure 中对文件存储的 SMB 访问。 可以将 Azure RBAC 用于共享级访问控制，并将 NTFS DACL 用于目录/文件级权限执行。 有关使用域服务进行 Azure 文件存储身份验证的详细信息，请参阅[概述](../files/storage-files-active-directory-overview.md)。

- 用于 blob、文件、队列和表的 **共享密钥授权**。 使用共享密钥的客户端会随使用存储帐户访问密钥签名的每个请求传递一个标头。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key/)。

- 用于 blob、文件、队列和表的 **共享访问签名**。 共享访问签名 (SAS) 针对存储帐户中的资源提供有限的委托访问权限。 通过对签名的有效时间间隔或对它授予的权限添加约束，可灵活地管理访问权限。 有关详细信息，请参阅[使用共享访问签名 (SAS)](storage-sas-overview.md)。
- 用于容器和 blob 的 **匿名公共读取访问**。 无需授权。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/anonymous-read-access-configure.md)。

## <a name="next-steps"></a>后续步骤

- [授权访问 Azure 存储中的数据](authorize-data-access.md)
- [通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key/)
- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)
