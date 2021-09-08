---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: ebc113e993eadc41c2b1c58c9130908727101a6f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227868"
---
身份验证完成后，用户将与应用交互，应用会调用受保护的 Web API。 Web API 使用[持有者令牌](https://datatracker.ietf.org/doc/html/rfc6750)身份验证。 持有者令牌是应用从 Azure AD B2C 获取的访问令牌。 应用在 HTTPS 请求的授权标头中传递令牌。 
    
```http
Authorization: Bearer <token>
```

如果访问令牌范围与 Web API 范围不一致，身份验证库将获取具有正确范围的新访问令牌。
