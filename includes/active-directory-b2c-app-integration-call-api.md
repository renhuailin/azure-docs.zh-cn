---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 3d4bb5ff840a2f4ee5c4c33e2cc51dfe440866a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777829"
---
身份验证完成后，用户将与应用交互，应用会调用受保护的 Web API。 Web API 使用[持有者令牌](https://datatracker.ietf.org/doc/html/rfc6750)身份验证。 持有者令牌是应用从 Azure AD B2C 获取的访问令牌。 应用在 HTTPS 请求的授权标头中传递令牌。 
    
```http
Authorization: Bearer <token>
```

如果访问令牌范围与 Web API 范围不匹配，则身份验证库将获取具有正确范围的新访问令牌。
