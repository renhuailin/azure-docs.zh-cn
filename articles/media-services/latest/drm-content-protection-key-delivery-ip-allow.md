---
title: 使用 IP 允许列表限制对 DRM 许可证和 AES 密钥传递的访问
description: 了解如何使用 IP 允许列表限制对 DRM 和 AES 密钥的访问。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cc0a35c196956d1f6afaf3d3fb27b9f428bb9f6d
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204737"
---
# <a name="restrict-access-to-drm-license-and-aes-key-delivery-using-ip-allowlists"></a>使用 IP 允许列表限制对 DRM 许可证和 AES 密钥传递的访问

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

当使用媒体服务的[内容保护](./drm-content-protection-concept.md)和 DRM 功能保护媒体时，你可能会遇到这样的情形：需要将许可证或密钥请求的交付限制为网络上客户端设备的特定 IP 范围。 若要限制内容播放和密钥传递，可以使用 IP 允许列表进行密钥传递。

此外，还可以使用允许列表完全阻止对密钥传递流量的所有公共 Internet 访问，而只允许来自专用网络终结点的流量。

密钥传递的 IP 允许列表限制在提供的 IP 允许列表范围内将 DRM 许可证和 AES-128 密钥传递到客户端。

## <a name="setting-the-allowlist-for-key-delivery"></a>为密钥传递设置允许列表

密钥传递 IP 允许列表的设置位于媒体服务帐户资源上。 创建新的媒体服务帐户时，可以通过[媒体服务帐户资源](/rest/api/media/mediaservices/create-or-update)的 KeyDelivery 属性限制允许的 IP 范围。

可以将 defaultAction 属性设置为“允许”或“拒绝”，以控制将许可证和密钥传递到允许列表范围内的客户端。

ipAllowList 属性是使用 [CIDR 表示法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)的单个 IPv4 地址和/或 IPv4 范围的数组。

## <a name="setting-the-allowlist-in-the-portal"></a>在门户中设置允许列表

Azure 门户提供了一种方法，用于配置和更新用于密钥传递的 IP 允许列表。  导航到媒体服务帐户，并访问“设置”下的“密钥传递”菜单。

## <a name="next-steps"></a>后续步骤

- [创建帐户](./account-create-how-to.md)
- [DRM 和内容保护概述](./drm-content-protection-concept.md)
