---
title: 收集必需的参数
services: cognitive-services
author: aahill
manager: nitinme
description: 适用于所有认知服务容器的参数
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996381"
---
## <a name="gathering-required-parameters"></a>收集必需的参数

所有认知服务容器都需要三个主要参数。 最终用户许可协议 (EULA) 的值必须为 `accept`。 此外，终结点 URL 和 API 密钥都是必需的。

### <a name="endpoint-uri-endpoint_uri"></a>终结点 URI `{ENDPOINT_URI}`

终结点 URI 值位于相应认知服务资源的 Azure 门户“概述”页上。 导航到“概述”页，将鼠标悬停在终结点上，将显示一个 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 图标。 复制并在需要时使用。

![收集终结点 URL 供以后使用](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>密钥 `{API_KEY}`

此密钥用于启动容器，在相应认知服务资源的 Azure 门户“密钥”页上提供。 导航到“密钥”页，然后单击 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 图标。

![获取两个密钥中的一个供以后使用](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 以安全方式存储密钥（例如，使用 Azure Key Vault 来存储）。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。