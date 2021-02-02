---
title: 媒体服务的受信任存储
description: 托管标识身份验证允许媒体服务通过受信任的存储访问来访问已配置防火墙或 VNet 限制的存储帐户。
keywords: 受信任的存储，托管标识
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: e8d21e57f9a844b3cc0538f4805780829a1350f4
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428582"
---
# <a name="trusted-storage-for-media-services"></a>媒体服务的受信任存储

创建媒体服务帐户时，必须将其与存储帐户相关联。 媒体服务可以使用系统身份验证或托管标识身份验证访问该存储帐户。 媒体服务验证媒体服务帐户和存储帐户是否位于同一订阅中，并验证添加关联的用户是否具有使用 Azure 资源管理器 RBAC 访问存储帐户的权限。

## <a name="trusted-storage-with-a-firewall"></a>使用防火墙的受信任存储

但是，如果你想要使用防火墙来保护存储帐户并启用受信任的存储，则 [管理标识](concept-managed-identities.md) 身份验证是首选选项。 这使媒体服务可通过受信任的存储访问权限访问配置有防火墙或 VNet 限制的存储帐户。 这使媒体服务可通过受信任的存储访问权限访问配置有防火墙或 VNet 限制的存储帐户。

> [!NOTE]
> 需要授予 AMS 托管标识存储 Blob 数据参与者访问权限，媒体服务才能读取和写入存储帐户。  授予通用参与者角色将不起作用，因为它不会对数据平面启用正确的权限。

## <a name="further-reading"></a>其他阅读材料

若要了解用托管标识创建受信任存储的方法，请阅读 [托管标识和媒体服务](concept-managed-identities.md)。

若要详细了解受信任的 Microsoft 服务，请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

## <a name="next-steps"></a>后续步骤

若要详细了解托管标识可以为你和你的 Azure 应用程序执行哪些操作，请参阅 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
