---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 8a1253e7ac88d2fe8b557c48dd846b48de59fba3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059126"
---
## <a name="simultaneous-use-of-private-endpoints-and-vnet-service-endpoints"></a>同时使用专用终结点和 VNet 服务终结点

可使用[专用终结点](../speech-services-private-link.md)和 [VNet 服务终结点](../speech-service-vnet-service-endpoint.md)同时访问同一语音资源。 但是，要同时启用专用终结点和 VNet 服务终结点，需要使用 Azure 门户“语音资源”的网络设置中的“选定网络和专用终结点”选项。 此方案不支持任何其他选项。
