---
title: 教程：使用 Azure 租户颁发和识别可验证凭据（预览）
description: 更改可验证凭据代码示例，以使用你的 Azure 租户
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: barclayn
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 34904f0183e96f8b0f59be42ea605d86ff34fed7
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729551"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>教程：使用租户颁发和识别可验证凭据（预览）

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

现在，你已使用“可验证凭据”服务设置 Azure 租户，接下来，我们使用示例应用逐步介绍使 Azure Active Directory (Azure AD) 能够颁发可验证凭据所需的步骤。

本文内容：

> [!div class="checklist"]
> * 在 Azure AD 中注册该示例应用
> * 创建规则和显示文件
> * 上传规则和显示文件
> * 设置“可验证凭据颁发者”服务以使用 Azure Key Vault
> * 使用你的租户信息更新示例代码。

我们的示例代码要求用户先向标识提供者（特别是 Azure AD B2C）进行身份验证，然后才能颁发已验证的凭据专家 VC。 并非所有可验证凭据颁发者都需要进行身份验证才能颁发凭据。

身份验证 ID 令牌使用户能够先证明他们的身份，然后再接收凭据。 当用户成功登录时，标识提供者会返回一个安全令牌，其中包含有关该用户的声明。 然后，颁发者服务会将这些安全令牌及其声明转换为可验证凭据。 该可验证凭据以颁发者的 DID 进行签名。

任何支持 OpenID Connect 协议的标识提供者都受支持。 支持的标识提供者示例包括 [Azure Active Directory](../fundamentals/active-directory-whatis.md) 和 [Azure AD B2C](../../active-directory-b2c/overview.md)。 在本教程中，我们使用 AAD。

## <a name="prerequisites"></a>先决条件

本教程假设你已完成[前面教程](enable-your-tenant-verifiable-credentials.md)中的步骤，并有权访问你所使用的环境。

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>注册应用以允许 DID 电子钱包登录用户

若要颁发可验证凭据，你需要注册一个应用，以便允许 Authenticator 或任何其他可验证的凭据电子钱包登录用户。  

在 Azure AD 中注册名为“VC Wallet App”的应用程序，并获取客户端 ID。

1. 请按照有关向 [Azure AD](../develop/quickstart-register-app.md) 注册应用程序的说明进行操作。注册时，请使用下列值。

   - 名称：“VC Wallet App”
   - 支持的帐户类型：仅限此组织目录中的帐户
   - 重定向 URI：vcclient://openid/

   ![注册应用程序](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. 注册该应用程序后，请记下应用程序（客户端）ID。 稍后需要用到此值。

   ![应用程序客户端 ID](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. 选择“终结点”按钮，然后复制 OpenID Connect 元数据文档 URI。 下一节需要用到此信息。 

   ![颁发者终结点](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)


到目前为止，我们一直在使用示例应用。 该应用使用 [Azure Active Directory B2C](../../active-directory-b2c/overview.md)，现在我们要切换为使用 Azure AD，因此需要进行一些更改，这不仅仅是为了符合你的环境，同时还是为了支持以前未曾使用过的其他声明。

1. 复制下面的规则文件，并将其保存到 modified-expertRules.json。 

    > [!NOTE]
    > 此规则文件中包含 "scope": "openid profile"，但示例应用的规则文件未包含该内容。 下一节将介绍如何在 Azure Active Directory 租户中启用可选的声明。 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. 打开该文件，并将 client_id 和 configuration 值替换为上一节复制的两个值。 

    ![突出显示需要在此步骤中修改的两个值](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  值 Configuration 是 OpenID Connect 元数据文档 URI。

  示例代码使用的是 Azure Active Directory B2C，但我们使用的是 Azure Active Directory，因此需要通过作用域添加可选的声明，以便将这些声明包括在要写入可验证凭据的 ID 令牌中。 

3. 回到 Azure 门户中，打开 Azure Active Directory。
4. 选择“应用注册”。
5. 打开我们早先创建的 VC Wallet App。
6. 选择“令牌配置”。
7. 选择“+添加可选声明”

     ![在令牌配置下添加可选的声明](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. 从“令牌类型”中选择 ID，然后从可用声明列表中选择 given_name 和 family_name   

     ![添加可选的声明](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. 按“添加”  。
10. 如果你收到如下所示的权限警告，请选中该复选框，然后选择“添加”。

     ![添加对应于可选声明的权限](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

现在，当系统显示“登录”给用户以颁发可验证凭据时，VC Wallet App 知道通过要写入可验证凭据的 scope 参数包括特定的声明。

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>使用此规则文件和旧显示文件创建新 VC

1. 将新规则文件上传至容器
1. 从可验证凭据页面中，使用旧显示文件和新规则文件 (modified-credentialExpert.json) 创建名为 modifiedCredentialExpert 的新凭据。 
1. 创建凭据的过程完成后，请从“概述”页面复制“颁发凭据 URL”并保存，下一节需要用到该信息。 

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>设置节点应用以访问 Azure Key Vault

为验证用户的凭据颁发请求，颁发者网站会使用 Azure Key Vault 中的加密密钥。 若要访问 Azure Key Vault，你的网站需要可用来向 Azure Key Vault 进行身份验证的客户端 ID 和客户端密码。

首先，我们需要注册另一个应用程序。 此注册适用于网站。 之前的钱包应用注册只是为了允许用户使用钱包应用登录到目录，在我们的例子中它恰好在同一目录中，但钱包应用注册也可以在不同的目录中完成。 如果应用程序的责任不同，则一种很好的做法是将应用注册分开。 在这种情况下，我们需要我们的网站可以访问密钥保管库。

1. 请按照有关向 [Azure AD](../develop/quickstart-register-app.md) 注册应用程序的说明进行操作。注册时，请使用下列值。

   - 名称：“VC 网站”
   - 支持的帐户类型：仅限此组织目录中的帐户

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-registration.png" alt-text="显示如何注册应用程序的屏幕截图。":::

1. 注册该应用程序后，请记下应用程序（客户端）ID。 稍后需要用到此值。

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-details.png" alt-text="显示应用程序客户端 ID 的屏幕截图。":::

1. 查看 VC 网站应用概述页面时，请选择“证书和密码”。

    :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-certificates-secrets.png" alt-text="显示“证书和密码”窗格的屏幕截图。":::

1. 在“客户端密码”部分，选择“新建客户端密码” 
    1. 添加说明，例如“节点 VC 客户端密码”
    1. 过期时间：一年。

    ![有效期为一年的应用程序密码](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)

1. 将该密码抄下来。 更新示例节点应用时需要用到此信息。

>[!WARNING]
> 你只有一次抄下该密码的机会， 在此之后，系统会对该密码进行单向哈希处理。 请勿抄下 ID。 

在 Azure AD 中创建应用程序和客户端密码后，需要将必要的权限授予该应用程序，以便其对你的 Key Vault 执行操作。 要让网站能够访问和使用存储在该处的私钥，必须进行这些权限更改。

1. 转到 Key Vault。
2. 选择我们在这些教程中使用的密钥保管库。
3. 在左侧导航栏上，选择“访问策略”
4. 选择“+添加访问策略”。
5. 在“密钥权限”部分，选择“获取”，然后选择“签名”。  
6. 选择“主体”，然后使用应用程序 ID 搜索我们早先注册的应用程序。 选择该文件夹。
7. 选择 **添加** 。
8. 选择“保存”。

:::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png" alt-text="显示添加访问策略的屏幕截图。":::

有关密钥保管库权限和访问控制的详细信息，请参阅[密钥保管库 RBAC 指南](../../key-vault/general/rbac-guide.md)。

## <a name="make-changes-to-the-sample-app"></a>对示例应用进行更改

我们必须先将几个值放在一起，然后才能进行必要的代码更改。 我们在下一节会用到这些值，让示例代码使用你自己的存储在保管库中的密钥。 到目前为止，我们应该已准备好下列值。

- 协定 URI：来自我们刚刚创建的凭据（即，颁发凭据 URL）
- 应用程序客户端 ID：我们在注册节点应用时得到此 ID。
- 客户端密码：我们已在早先授权应用访问密钥保管库时创建此密码。

在我们的示例应用中进行一次更改之前，我们还需要获取一些其他值。 即刻行动！

### <a name="verifiable-credentials-settings"></a>可验证凭据设置

1. 导航到“可验证凭据”页面，然后选择“设置”。  
1. 将下列值抄下来：

    - 租户标识符 
    - 颁发者标识符（你的 DID）
    - 密钥保管库 (URI)

1. 在签名密钥标识符下，有一个 URI，但我们只需要其中的部分内容。 请抄下从 issuerSigningKeyION 开始的部分，即下图中红色矩形所突出显示的部分。

   ![登录密钥标识符](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID 文档

1. 打开 [DIF ION Network Explorer](https://identity.foundation/ion/explorer/)

2. 将你的 DID 粘贴到搜索栏中。

4. 从格式化响应中，找到名为 verificationMethod 的部分
5. 在“verificationMethod”下复制 `id`，并将其标记为 kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

现在，我们已经有了在示例代码中进行更改所需的全部信息。

- 颁发者：app.js 会使用新的协定 URI 更新常量凭据
- 验证者：app.js 会使用你的颁发者标识符更新 issuerDid
- 颁发者和验证者：请使用下列值更新 didconfig.json：


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>这是演示应用程序，你通常不应将密码直接提供给应用程序。


现在，一切都已准备就绪，你可以使用自己的密钥，从 Azure Active Directory 租户颁发和验证你自己的可验证凭据。 

## <a name="issue-and-verify-the-vc"></a>颁发和验证 VC

按照前一教程所述的步骤颁发可验证凭据，并在你的应用中验证该凭据。 一旦成功地完成验证过程，你现在便可以继续了解可验证凭据。

1. 打开命令提示符并打开颁发者文件夹。
2. 运行更新后的节点应用。

    ```terminal
    node app.js
    ```

3. 使用其他命令提示符运行 ngrok，在8081上设置 URL

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > 你可能还会注意到此应用或网站可能有风险的警告。 此时应该会出现该消息，因为我们尚未将你的 DID 链接到你的域。 按照 [DNS 绑定](how-to-dnsbind.md)说明进行配置。

    
4. 打开 ngrok 生成的 HTTPS URL。

    ![NGROK 转发终结点](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. 选择“获取凭据”
6. 在 Authenticator 中扫描 QR 代码。
7. 在“此应用或网站可能会出现风险”警告消息中选择“高级”。 

  ![初始警告](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. 在“网站有风险”的警告中选择“继续（不安全）”

  ![有关颁发者的第二条警告](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. 在“添加凭据”屏幕上，请注意以下几点： 
    1. 在页面顶部，可以看到红色的“未验证”消息
    1. 凭据根据我们对显示文件所做的更改自定义的。
    1. “登录你的帐户”选项指向 Azure AD 登录页面。
    
   ![添加凭据页面并显示警告](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. 选择“登录你的帐户”，并使用 Azure AD 租户中的用户进行身份验证。
11. 身份验证成功后，“添加”按钮不再是灰色。选择“添加”。

  ![进行身份验证后添加凭据屏幕](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

现在，我们已使用你的租户颁发了可验证凭据来生成 VC，同时仍然使用原来的 B2C 租户进行身份验证。

  ![由 Azure AD 颁发并由 Azure B2C 实例进行身份验证的 vc](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>使用示例应用程序验证 VC

现在，我们已从你自己的租户颁发了可验证凭据，其中包含来自 Azure AD 的声明，接下来使用示例应用验证该凭据。

1. 停止运行颁发者 ngrok 服务。

    ```cmd
    control-c
    ```

2. 现在，请运行包含验证程序端口 8082 的 ngrok。

    ```cmd
    ngrok http 8082
    ```

3. 在另一个终端窗口中，导航到验证程序应用，并按照与运行颁发者应用相同的方式运行。

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. 在浏览器中打开 ngrok url，并使用移动设备中的 Authenticator 扫描 QR 代码。
5. 在移动设备上，在“新建权限请求”页面上选择“允许”。

   >[!IMPORTANT]
    > 由于示例应用还使用你的 DID 对演示请求进行签名，你会注意到此应用或网站可能会有风险的警告。 此时应该会出现该消息，因为我们尚未将你的 DID 链接到你的域。 按照 [DNS 绑定](how-to-dnsbind.md)说明进行配置。
    
   ![新权限请求](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. 你现在已成功验证凭据，网站应该会显示你的 Azure AD 用户帐户的名字和姓氏。 

你现已完成本教程，正式成为经验证的凭据专家！ 你的示例应用正在使用你的 DID 进行颁发和验证，同时将声明从 Azure AD 写入可验证凭据。 

## <a name="next-steps"></a>后续步骤

- 了解如何创建[自定义凭据](credential-design.md)
- 颁发者服务通信[示例](issuer-openid.md)
