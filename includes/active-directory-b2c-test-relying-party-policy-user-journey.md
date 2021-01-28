---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 4ca47bf5b99588fbe4efd91a9211ee0309c1892c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951510"
---
## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin` 。
1. 对于 " **应用程序**"，请选择 [之前注册](../articles/active-directory-b2c/troubleshoot-custom-policies.md#troubleshoot-the-runtime)的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择 " **立即运行** " 按钮。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms` ，后者显示 Azure AD B2C 返回的令牌的内容。

