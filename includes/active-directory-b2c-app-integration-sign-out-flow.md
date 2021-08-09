---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f4677d9800f0c94d9071838b9addbeb3cd1231c9
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071314"
---
注销流程涉及以下步骤：

1. 在应用中，用户选择“注销”。
1. 应用会清除其会话 Cookie，并且身份验证库会清除其令牌缓存。
1. 应用将用户重定向到 Azure AD B2C 注销终结点，以终止 Azure AD B2C 会话。
1. 用户被重定向回该应用。