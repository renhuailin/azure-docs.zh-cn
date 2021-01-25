---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760655"
---
## <a name="retrying-after-errors-and-exceptions"></a>出现错误和异常后重试

在调用 MSAL 时，应实现自己的重试策略。 MSAL 对 Azure AD 服务进行 HTTP 调用，有时会发生失败。 例如，网络可能会关闭或服务器过载。  

### <a name="http-429"></a>HTTP 429

如果服务令牌服务器 (STS) 因请求过多而重载，则将返回 HTTP 错误 429，并在 `Retry-After` 响应字段中提示还要多久才能重试。