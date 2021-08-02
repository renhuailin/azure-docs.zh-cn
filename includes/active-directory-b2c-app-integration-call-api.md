---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: fe128e7eee99d2b396c758979f0efff936ec6785
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072958"
---
进行身份验证后，用户导航到一个页面，该页面通过持有者令牌身份验证调用受保护的 Web API。 持有者令牌是从 Azure AD B2C 获取的访问令牌。 应用在 HTTPS 请求的授权标头中传递令牌。 
    
```http
Authorization: Bearer <token>
```

如果访问令牌范围与 Web API 范围不一致，身份验证库将获取具有正确范围的新访问令牌。
s