---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: e6ab23e9d98d2bb1dfea21a7b4f681dbd5be6e14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802565"
---
## <a name="use-a-custom-domain"></a>使用自定义域

通过使用[自定义域](../articles/active-directory-b2c/custom-domain.md)，可以完全标记身份验证 URL。 从用户的角度来看，用户在身份验证过程中仍留在你的域中，而不是重定向到 Azure AD B2C b2clogin.com 域名。

你还可以将身份验证请求 URL 中的 B2C 租户名称 (contoso.onmicrosoft.com) 替换为你的租户 ID GUID，以便删除对 URL 中“b2c”的所有引用。 例如，可以将 `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` 更改为 `https://account.contosobank.co.uk/<tenant ID GUID>/`。
