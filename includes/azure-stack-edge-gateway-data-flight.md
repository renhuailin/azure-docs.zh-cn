---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466376"
---
对于航班中的数据：

- 标准传输层安全性 (TLS) 1.2 用于在设备和 Azure 之间传输的数据。 不会回退到 TLS 1.1 和更早版本。 如果不支持 TLS 1.2，则将阻止代理通信。 门户和 SDK 管理还需要 TLS 1.2。
- 当客户端通过浏览器的本地 web UI 访问设备时，将使用标准 TLS 1.2 作为默认安全协议。

  - 最佳做法是将浏览器配置为使用 TLS 1.2。
  - 你的设备仅支持 TLS 1.2，并且不支持较旧版本的 TLS 1.1 或 TLS 1.0。
- 建议你在将数据从数据服务器复制时，将 SMB 3.0 与加密配合使用来保护数据。
