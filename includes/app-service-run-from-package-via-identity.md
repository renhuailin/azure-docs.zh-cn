---
author: mattchenderson
ms.service: app-service-web
ms.topic: include
ms.date: 06/11/2021
ms.author: mahender
ms.openlocfilehash: 9b05a4d16cbf6d3b9677c894bffb6e47018b5cff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122262479"
---
可以将 Azure Blob 存储配置为使用 [Azure AD 授权请求](../articles/storage/blobs/authorize-access-azure-active-directory.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 这意味着，可以依赖应用程序的[托管标识](../articles/app-service/overview-managed-identity.md)，而不是生成会过期的 SAS 密钥。 默认情况下，将使用应用的系统分配标识。 如果要指定用户分配的标识，可以将 `WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID` 应用设置设为该标识的资源 ID。 该设置还可以接受“SystemAssigned”作为值，尽管这与完全省略该设置是一样的。

若要使用标识提取包，请运行以下操作：

1. 确保 Blob 已[配置为私有访问](../articles/storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)。

1. 向标识授予[存储 Blob 数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色，其范围为包 blob。 有关创建角色分配的详细信息，请参阅[分配 Azure 角色以访问 Blob 数据](../articles/storage/blobs/assign-azure-role-data-access.md)。

1. 将 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置设为包的 blob URL。 格式可能为“https://{storage-account-name}.blob.core.windows.net/{container-name}/{path-to-package}”或类似格式。