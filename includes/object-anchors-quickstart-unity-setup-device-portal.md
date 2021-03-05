---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044542"
---
### <a name="set-up-the-windows-device-portal"></a>设置 Windows 设备门户

若要通过 WiFi 连接到 HoloLens，请执行以下步骤：

1. 首先，[将 HoloLens 连接到 WiFi](https://docs.microsoft.com/hololens/hololens-network)。

2. 然后，在设备上的“设置”>“网络和 Internet”>“Wi-Fi”>“高级选项”下获取 IP 地址。

3. 通过电脑上的 Web 浏览器，转到 `https://<YOUR_HOLOLENS_IP_ADDRESS>`。 浏览器中将显示以下消息：“此网站的安全证书有问题。” 由于颁发给设备门户的证书是自签名证书，因此会出现上述消息。 你可以忽略此证书错误并继续。

有关设置 Windows 设备门户的详细信息，请参阅[此处](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)。
