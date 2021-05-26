---
title: 教程 - 配置 Azure Active Directory，颁发可验证凭据（预览）
description: 在本教程中，你将构建在你的租户中部署可验证凭据所需的环境
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 05/18/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 3f5808f3074fa2e45bc638e3e3d13cad431997ac
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110092776"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>教程 - 配置 Azure Active Directory，颁发可验证凭据（预览）

在本教程中，我们以[入门](get-started-verifiable-credentials.md)文章中完成的工作为基础，并使用自己的[分散标识符](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) 设置 Azure Active Directory (Azure AD)。 我们使用分散式标识符，通过示例应用和颁发者颁发可验证凭据;但是，在本教程中，我们仍使用示例 Azure B2C 租户进行身份验证。  在下一教程中，我们将执行另外的步骤，将应用配置可与你的 Azure AD 一起使用。

> [!IMPORTANT]
> 当前 Azure Active Directory 可验证凭据为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文内容：

> [!div class="checklist"]
> * 创建必需的服务以使你的 Azure AD 用于可验证凭据 
> * 我们正在创建你的 DID
> * 我们正在自定义规则和显示文件
> * 在 Azure AD 中配置可验证凭据。


## <a name="prerequisites"></a>必备条件

在开始本教程之前，必须先首先：

- 完成[入门](get-started-verifiable-credentials.md)。
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 带 P2 [许可证的 Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)Azure AD。 如果没有帐户，请参照[如何创建免费的开发人员帐户](how-to-create-a-free-developer-account.md)。
- [Azure Key Vault](../../key-vault/general/overview.md) 的实例，据此你有权创建密钥和机密。

## <a name="azure-active-directory"></a>Azure Active Directory

在开始之前，我们需要 Azure AD 租户。 如果你的租户启用了可验证凭据，则会为其分配分散标识符 (DID)，颁发可验证凭据的颁发者服务可以启用租户。 你颁发的任何可验证凭据都是由你的租户和其 DID 进行颁发。 验证可验证凭据时，也会用到 DID。
如果你刚刚才创建测试 Azure 订阅，你的租户不需要充满用户帐户，但你至少需要一个用户测试帐户才能完成后面的教程。

## <a name="create-a-key-vault"></a>创建密钥保管库

使用可验证凭据时，你可以完全控制和管理租户用于对可验证凭据进行数字签名的加密密钥。 若要颁发和验证凭据，你必须提供 Azure AD 访问你自己的 Azure Key Vault 实例。

1. 在 Azure 门户菜单或“主页”中，选择“创建资源” 。
2. 在搜索框中输入“Key Vault”。
3. 从结果列表中选择“Key Vault”。
4. 在“Key Vault”部分，选择“创建”。
5. 在“创建密钥保管库”部分，提供以下信息：
    - 订阅：选择订阅。
    - 在“资源组”下选择“新建”，然后输入资源组名称，比如“VC 资源组”。  。 我们在多个项目中使用相同的资源组名称。
    - 名称：必须提供唯一的名称。 我们使用“woodgroup-kv”作为名称，可以将此值替换为独属于你自己的名称。
    - 在“位置”下拉菜单中选择一个位置。
    - 让其他选项保留默认值。
6. 提供上述信息后，选择“访问策略”

    ![创建密钥保管库页面](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. 在访问策略页面，选择“添加访问政策”

    >[!NOTE]
    > 默认情况下，仅创建密钥保管库的帐户具有访问权限。 可验证凭据服务需要访问密钥保管库。 如果选择退出并同意创建可验证凭据的域绑定，密钥保管库必须规定访问政策，允许管理员创建密钥并有删除密钥的能力。   如果你在测试时使用的是同一帐户，请确保修改默认政策，在授予保管库创建者默认访问权限以外，授予帐户登录权限。

8. 对于用户管理员，请确保密钥权限部分已经启用了创建、删除和签名权限。   默认情况下，创建和删除权限已启用，签名权限应该是唯一需要更新的密钥权限。 

    ![密钥保管库权限](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. 选择“查看 + 创建”。
10. 选择“创建”。
11. 转到保管库，记下保管库名称和 URI

请记下下面列出的两个属性：

- 保管库名称：在本示例中，值名称为“woodgrove-vc-kv”。  将在其他步骤中使用该名称。
- 保管库 URI：在本示例中，此值为 https://woodgrove-vc-kv.vault.azure.net/ 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

>[!NOTE]
> 每个密钥保管库事务都会产生额外的 Azure 订阅费用。 查看 [Key Vault 定价页面](https://azure.microsoft.com/pricing/details/key-vault/) 了解更多详细信息。

>[!IMPORTANT]
> 在 Azure Active Directory 可验证凭据预览期间，在保管库中创建的密钥和机密不应在创建后修改。 删除、禁用或更新密钥和机密会使任何已颁发的凭据失效。 请不要在预览期间修改密钥或机密。

## <a name="create-a-modified-rules-and-display-file"></a>创建修改后的规则和显示文件

在本部分中，我们将使用[示例颁发者](https://github.com/Azure-Samples/active-directory-verifiable-credentials/)应用当中的规则和显示文件，并稍作修改以创建租户的第一个可验证凭据。

1. 将规则和 json 显示文件都复制到临时文件夹，并将它们分别重命名为 MyFirstVC-display.json 和 MyFirstVC-rules.json。  你可以在 issuer\issuer_config 下同时找到两个文件

   ![示例应用目录中的显示和规则文件](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![临时文件夹中的显示和规则文件](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. 在代码编辑器里打开 MyFirstVC-rules.json 文件。 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

   现在，我们将类型字段更改为“MyFirstVC”。 

   ```json
    "type": ["MyFirstVC"]
  
   ```

   保存此更改。

   >[!NOTE]
   > 在本教程的这一点上，我们不会更改“配置”或“客户端_id”。  我们仍然使用在[入门](get-started-verifiable-credentials.md)中使用过的 Microsoft B2C 租户。 在下一教程中，我们将使用你的 Azure AD。

3. 在代码编辑器里打开 MyFirstVC-display.json 文件。

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

   我们来做一些修改，让该可验证凭据视觉上与示例代码的版本不同。 

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > 为了确保凭据可读且可访问，我们强烈建议你选择[对比度](https://www.w3.org/WAI/WCAG21/Techniques/general/G18)至少为 4.5:1 的文本和背景色。  

   保存次更改。

## <a name="create-a-storage-account"></a>创建存储帐户

在创建我们的第一个可验证凭据之前，需要创建一个可保存配置和规则文件的“Blob 存储容器”。

1. 使用如下所示的选项创建存储帐户。 有关详细步骤，请查看[创建存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)一文。

   - 订阅：选择我们要在这些教程中使用的订阅。
   - 资源组：选择我们在之前的教程中使用过的资源组 (vc-resource-group)。
   - 名称：唯一名称。
   - 位置：（美国）美国东部。
   - 性能：标准。
   - 帐户类型：存储 V2。
   - 复制：本地冗余。
 
   ![新建存储帐户](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. 创建存储帐户后，需要创建一个容器。 在“Blob 存储”下选择“容器”并使用下列提供的值创建一个容器： 

    - 名称：vc-容器
    - 公共访问级别：私有（无匿名访问）

   ![创建容器](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. 现在，请选择新容器并上传新规则和显示文件，也就是我们之前创建的 MyFirstVC-display.json 和 MyFirstVC-rules.json。 

   ![上传规则文件](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>分配 blob 角色

在创建凭据之前，首先需要为已登录的用户授予正确的角色分配，以便他们能够访问存储 Blob 中的文件。

1. 导航到“存储” > “容器”。
2. 从左侧菜单中选择“访问控制 (IAM)”。
3. 选择“角色分配”。
4. 选择 **添加** 。
5. 在“角色”部分，选择“存储 Blob 数据读取器”。
6. 在“分配访问权限”下，选择“用户、组或服务原则” 。
7. 在“选择”中：选择用于执行这些步骤的帐户。
8. 选择“保存”完成角色分配操作。


   ![添加角色分配](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >默认情况下，容器创建者会被分配到“所有者”角色。 只有“所有者”角色是不够的。 你的帐户需要“存储 Blob 数据读取器”角色。 有关详细信息，请参阅[使用 Azure 门户分配用于访问 Blob 和队列数据的 Azure 角色](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>设置可验证凭据（预览）

现在，我们需要执行最后一个步骤来设置获取可验证凭据的租户。

1. 在 Azure 门户中，搜索“可验证凭据”。
2. 选择“可验证凭据（预览）”。
3. 选择“开始”
4. 我们需要设置你的组织，并提供组织名称、域和密钥保管库。 让我们看看每个值。

      - 组织名称：此名称用作在可验证凭据服务内引用你的业务。 此值不面向客户。
      - 域：输入的域将添加到你的 DID 文档中的服务终结点。 [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) 和其他钱包使用该信息验证你的 DID 是否已[链接到你的域](how-to-dnsbind.md)。 如果钱包可以验证 DID，则会显示已验证的符号。 如果钱包无法验证 DID，则会通知用户凭据是由它无法验证的组织颁发的。 域将你的 DID 绑定到用户可能了解的你业务中的内容。
      - 密钥保管库：提供我们之前创建的密钥保管库的名称。
 
   >[!IMPORTANT]
   > 域不能是重定向，否则 DID 不能链接到域。 请确保使用 https://www.domain.com 格式。
    
5. 选择“保存”并创建凭据

    ![设置组织标识](media/enable-your-tenant-verifiable-credentials/save-create.png)

恭喜，现已为你的租户启用可验证凭据预览！

## <a name="create-your-vc-in-the-portal"></a>在门户中创建你的 VC

上一步你留在了“创建新凭据”页面。 

   ![可验证凭据入门](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. 在“凭据名称”下，添加名称 MyFirstVC。 此名称用于在门户中识别你的可验证凭据，并作为可验证凭据协定的一部分包含在内。
2. 在显示文件部分，选择“配置显示文件”
3. 在“存储帐户”部分，选择“woodgrovevcstorage”。 
4. 从可用容器列表中选择“vc 容器”。
5. 选择前面创建的文件“MyFirstVC-display.js”。
6. 在“创建新凭据”的“规则文件”部分，选择“配置规则文件”
7. 在“存储帐户”部分，选择“woodgrovecstorage”
8. 选择“vc 容器”。
9. 选择“MyFirstVC-rules.json”
10. 从“创建新凭据”页面选择“创建”。

   ![创建新凭据](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>凭据 URL

现在你有了新凭据，请复制凭据 URL。

   ![问题凭据 URL](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>凭据 URL 是规则和显示文件的组合。 它是 Authenticator 在显示为用户可验证凭据颁发要求之前评估的 URL。  

## <a name="update-the-sample-app"></a>更新示例应用

现在，我们对示例应用的颁发者代码进行修改，以便用可验证凭据 URL 更新它。 这使你可以使用自己的租户颁发可验证凭据。

1. 转到放置示例应用文件的文件夹。
2. 打开颁发者文件夹，然后在 Visual Studio Code 中打开 app.js。
3. 用你的新凭据 URL 更新常数“credential”，并将“credentialType”常数设置为“MyFirstVC”，然后保存该文件。

    ![visual studio code 图像显示，相关区域经过标注](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. 打开命令提示符并打开颁发者文件夹。
5. 运行更新后的节点应用。

    ```terminal
    node app.js
    ```

6. 使用其他命令提示符运行 ngrok，在 8081 上设置 URL。 你可以使用 [ngrok npm 包](https://www.npmjs.com/package/ngrok/)全局安装 ngrok。

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > 你可能还会注意到此应用或网站可能有风险的警告。 此时应该会出现该消息，因为我们尚未将你的 DID 链接到你的域。 按照 [DNS 绑定](how-to-dnsbind.md)说明进行配置。

    
7. 打开 ngrok 生成的 HTTPS URL。

    ![NGROK 转发终结点](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. 选择“获取凭据”
9. 在 Authenticator 中扫描 QR 代码。
10. 在“此应用或网站可能会出现风险”警告消息中选择“高级”。 

  ![初始警告](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. 在“网站有风险”的警告中选择“继续（不安全）”

  ![有关颁发者的第二条警告](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. 在“添加凭据”屏幕上，请注意以下几点： 
    1. 在页面顶部，可以看到红色的“未验证”消息
    1. 凭据根据我们对显示文件所做的更改自定义的。
    1. “登录帐户”选项指向 didplayground.b2clogin.com。 
    
   ![添加凭据页面并显示警告](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. 选择“登录到你的帐户”，然后使用在[入门教程](get-started-verifiable-credentials.md)中提供的凭据信息进行身份验证。
14. 身份验证成功后，“添加”按钮不再是灰色。选择“添加”。

  ![进行身份验证后的添加凭据屏幕](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

现在，我们已使用我们的租户颁发了可验证凭据来生成 VC，同时仍然使用我们的 B2C 租户进行身份验证。

  ![由 Azure AD 颁发并由 Azure B2C 实例进行身份验证的 VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>使用示例应用程序验证 VC

现在，我们已从自己的租户颁发了可验证凭据，接下来使用示例应用对其验证。

>[!IMPORTANT]
> 测试时，请使用在[入门](get-started-verifiable-credentials.md)文章中使用过的电子邮件和密码。 租户颁发 vc 时，输入来自 Microsoft B2C 租户颁发的 id 令牌。 在第二个教程中，我们会将令牌颁发交换到你的租户

1. 在 Azure 门户的“可验证凭据”边栏选项卡中打开“设置”。 复制分散标识符 (DID)。

   ![复制 DID](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. 现在，打开示例应用文件中的验证程序文件夹。 需要更新验证程序示例代码中的 app.js 文件，然后进行以下更改：

    - 凭据：切换到你的凭据 URL
    - 凭据类型：“MyFirstVC”
    - 颁发者 DID：从 Azure 门户 >“可验证凭据（预览）”>“设置”>“分散识别符 (DID)”复制该值
    
   ![更新颁发者 DID 常数以匹配颁发者标识符](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. 停止运行颁发者 ngrok 服务。

    ```cmd
    control-c
    ```

4. 现在，请运行包含验证程序端口 8082 的 ngrok。

    ```cmd
    ngrok http 8082
    ```

5. 在另一个终端窗口中，导航到验证程序应用，并按照与运行颁发者应用相同的方式运行。

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. 在浏览器中打开 ngrok url，并使用移动设备中的 Authenticator 扫描 QR 代码。
7. 在移动设备上，在“新建权限请求”页面上选择“允许”。

    >[!NOTE]
    > 签署此 VC 的 DID 仍是 Microsoft B2C。 验证程序 DID 仍来自 Microsoft 示例应用租户。 由于 Microsoft DID 已链接到我们所拥有的域，因此不会看到我们在颁发流程中遇到过的类似警告。 这将在下一部分更新。
    
   ![新权限请求](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. 你没有成功验证凭据。

## <a name="next-steps"></a>后续步骤

现在，你已经有了从颁发者颁发 VC 的示例代码，接下来，我们将继续学习下一节，其中你使用自己的标识提供者对尝试获取可验证凭据的用户进行身份验证，并使用你的 DID 对演示请求签名。

> [!div class="nextstepaction"]
> [教程：使用租户颁发和识别可验证凭据](issue-verify-verifiable-credentials-your-tenant.md)


