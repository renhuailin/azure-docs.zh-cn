---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760655"
---
## <a name="retrying-after-errors-and-exceptions"></a>出现错误和异常后重试

调用 MSAL 时，应实现自己的重试策略。 MSAL 对 Azure AD 服务进行 HTTP 调用，有时会出现失败。 例如网络崩溃或服务器重载。  

### <a name="http-429"></a>HTTP 429

如果服务令牌服务器 (STS) 因请求过多而重载，则将返回 HTTP 错误 429，并在 `Retry-After` 响应字段中提示还要多久才能重试。