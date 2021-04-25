---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581658"
---
对于使用中的数据：

- 标准 TLS 1.2，用于在设备与 Azure 之间传输的数据。 不能回退到 TLS 1.1 和更早版本。 如果不支持 TLS 1.2，则代理通信将被阻止。 门户和 SDK 管理也需要 TLS 1.2。
- 当客户端通过浏览器的本地 Web UI 访问设备时，将使用标准 TLS 1.2 作为默认安全协议。

    - 最佳做法是将浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，可以使用 TLS 1.1 或 TLS 1.0。
- 从数据服务器复制数据时，建议使用具有加密功能的 SMB 3.0 来保护数据。
