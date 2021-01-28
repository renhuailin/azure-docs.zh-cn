---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951508"
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

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择 " **上传自定义策略**"，然后上传已更改的两个策略文件，顺序如下：扩展策略，例如 `TrustFrameworkExtensions.xml` `SignUpSignIn.xml` 。



