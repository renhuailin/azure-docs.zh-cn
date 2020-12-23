---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369065"
---
>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../../cognitive-services-custom-subdomains.md)。 

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](../../cognitive-services-apis-create-account.md)为墨迹识别器创建资源。

创建资源后，通过打开 [Azure 门户](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)上的资源并单击“快速入门”来获取终结点和密钥。

创建两个[环境变量](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)：

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 用于对请求进行身份验证的订阅密钥。 

* `INK_RECOGNITION_ENDPOINT` - 资源的终结点。 它将如下所示： <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`