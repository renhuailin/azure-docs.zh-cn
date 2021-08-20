---
author: wesmc7777
ms.service: iot-dps
ms.topic: include
ms.date: 06/15/2021
ms.author: wesmc
ms.openlocfilehash: b3c218314f56fb53d0690ac0659192bb955e4301
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112538992"
---
> [!NOTE]
> 此服务的限制在某些区域可调整。 下表的“可调整?”列中进行了相关描述。 当限制可调整时，“可调整?”的值为“是” 。
>
> 如果你的业务需要将可调整的限制或配额提高到默认限制之上，可以通过[创建支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来请求更多资源。

下表列出了适用于 Azure IoT 中心设备预配服务资源的限制。

| 资源 | 限制 | 可调？ |
| --- | --- | --- |
| 每个 Azure 订阅的最大设备预配服务数 | 10 | 是 |
| 最大注册数 | 1,000,000 | 是 |
| 单个注册的最大数目 | 1,000,000 | 是 |
| 注册组的最大数目（X.509 证书） | 100 | 是 |
| 注册组的最大数目（对称密钥） | 100 | 否 |
| 最大 CA 数 | 25 | 否 |
| 链接的 IoT 中心的最大数量 | 50 | 否 |
| 消息的最大大小 | 96 KB| 否 |

> [!TIP]
> 如果对称密钥注册组的硬性限制是一个阻塞性问题，建议使用单个注册作为解决方法。

设备预配服务具有以下速率限制。

| 费率 | 每单位值 | 可调？ |
| --- | --- | --- |
| Operations | 200/分钟/服务 | 是 |
| 设备注册数 | 200/分钟/服务 | 是 |
| 设备轮询操作 | 5/10 秒/设备 | 否 |

DPS 上的每个 API 调用都可以作为一个操作来计费。 这包括所有服务 API 和设备注册 API。 设备注册轮询操作不计费。