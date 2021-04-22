---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 648c2e4ffc63dad42a87888e7cb5629fb7301eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900778"
---
牢记以下最佳做法：

- 我们建议将所有密码都存储在安全位置，以便在忘记密码时不必重置密码。 管理服务无法检索现有密码。 只能通过 Azure 门户对其进行重置。 如果要重置密码，请务必在重置密码前通知所有用户。
- 可以通过 HTTP 远程访问设备的 Windows PowerShell 接口。 安全的最佳做法是仅在受信任的网络上使用 HTTP。
- 确保设备密码是强密码并且受到严密保护。 遵循[密码最佳做法](../articles/security/fundamentals/identity-management-best-practices.md#enable-password-management)。