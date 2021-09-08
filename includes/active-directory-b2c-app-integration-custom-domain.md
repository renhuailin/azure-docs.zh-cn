---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 8547dac26c8d3a6a286a49a6b6e12919199a994b
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220333"
---
## <a name="use-a-custom-domain"></a>使用自定义域

通过使用[自定义域](../articles/active-directory-b2c/custom-domain.md)，可以完全标记身份验证 URL。 从用户的角度来看，用户在身份验证过程中仍留在你的域中，而不是重定向到 Azure AD B2C b2clogin.com 域名。

若要删除对 URL 中“b2c”的所有引用，还可以将身份验证请求 URL 中的 B2C 租户名称 (contoso.onmicrosoft.com) 替换为你的租户 ID GUID。 例如，可以将 `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` 更改为 `https://account.contosobank.co.uk/<tenant ID GUID>/`。
