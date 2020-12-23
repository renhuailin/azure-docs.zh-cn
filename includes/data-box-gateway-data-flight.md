---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581658"
---
对于航班中的数据：

- 标准 TLS 1.2 用于在设备和 Azure 之间传输的数据。 不会回退到 TLS 1.1 和更早版本。 如果不支持 TLS 1.2，则将阻止代理通信。 门户和 SDK 管理还需要 TLS 1.2。
- 当客户端通过浏览器的本地 web UI 访问设备时，将使用标准 TLS 1.2 作为默认安全协议。

    - 最佳做法是将浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，你可以使用 TLS 1.1 或 TLS 1.0。
- 建议你在将数据从数据服务器复制时，将 SMB 3.0 与加密配合使用来保护数据。
