---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674215"
---
## <a name="configure-the-relying-party-policy"></a>配置信赖方策略

依赖方策略（例如 [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)）指定将执行 Azure AD B2C 的用户旅程。 在 [信赖方](../articles/active-directory-b2c/relyingparty.md)内查找 **DefaultUserJourney** 元素。 更新  **ReferenceId** ，使其与已添加标识提供者的用户旅程 ID 匹配。 

在以下示例中，对于 `CustomSignUpOrSignIn` 用户旅程， **ReferenceId** 设置为 `CustomSignUpOrSignIn` ：

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>上传自定义策略

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 " **上传自定义策略**"，然后上传已更改的两个策略文件，顺序如下：扩展策略，例如 `TrustFrameworkExtensions.xml` `SignUpSignIn.xml` 。

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`
1. 对于 " **应用程序**"，请选择之前注册的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择 " **立即运行** " 按钮。

如果登录过程成功，浏览器将重定向到 `https://jwt.ms` ，后者显示 Azure AD B2C 返回的令牌的内容。

