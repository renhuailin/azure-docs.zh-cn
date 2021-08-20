---
title: 教程：配置 Azure Acitive Directory 以颁发可验证凭据（预览版）
description: 在本教程中，将构建在租户中部署可验证凭据所需环境。
documentationCenter: ''
author: barclayn
manager: daveba
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 06/24/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 7b50e17a8c6730aedbc8fea68a2ab4d8685b2fa5
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228971"
---
# <a name="tutorial---configure-azure-active-directory-to-issue-verifiable-credentials-preview"></a>教程：配置 Azure Acitive Directory 以颁发可验证凭据（预览版）

在本教程中，将以[入门](get-started-verifiable-credentials.md)文章中完成的工作为基础，并使用自己的[分散标识符](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) 设置 Azure Active Directory (Azure AD)。 将通过示例应用和证书颁发者，使用 DID 颁发可验证凭据。 在本教程中，仍将使用示例 Azure B2C 租户进行身份验证。 在下一教程中，将执行几个步骤，将应用配置为与 Azure AD 一起使用。

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文内容：

> [!div class="checklist"]
> * 创建必需的服务以使 Azure AD 用于可验证凭据。
> * 创建 DID。
> * 自定义规则和显示文件。
> * 在 Azure AD 中配置可验证凭据。

## <a name="prerequisites"></a>必备条件

在开始本教程之前，必须先首先：

- 完成[入门](get-started-verifiable-credentials.md)教程中的步骤。
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 具有带 P2 [许可证](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)的 Azure AD。 如果没有，请遵循[创建免费开发者帐户](how-to-create-a-free-developer-account.md)中的步骤。
- 具有 [Azure Key Vault](../../key-vault/general/overview.md) 的实例，用户有权在其中创建密钥和机密。

## <a name="azure-active-directory"></a>Azure Active Directory

在开始之前，用户需要 Azure AD 租户。 为租户启用可验证凭据后，为其分配 DID。 还可以为其启用颁发者服务，以签发可验证凭据。 你颁发的任何可验证凭据都是由你的租户和其 DID 进行颁发。 验证可验证凭据时，也会用到 DID。

如果刚刚创建了一个测试 Azure 订阅，则租户无需填充用户帐户。 完成后续教程需要至少一个用户测试帐户。

## <a name="create-a-key-vault"></a>创建 key vault

使用可验证凭据时，可以完全控制和管理租户用于对可验证凭据进行数字签名的加密密钥。 要颁发和验证凭据，必须向 Azure AD 提供自己的 Azure Key Vault 实例的访问权限。

1. 在 Azure 门户或主页中，选择“创建资源”。
1. 在搜索框中输入“Key Vault”。
1. 从结果列表中选择“Key Vault”。
1. 在“Key Vault”部分，选择“创建”。
1. 在“创建密钥保管库”部分，提供以下信息：
    - “订阅”：选择订阅。
    - “资源组”：选择“新建”，输入资源组名称，如“VC-资源组”。 多个项目中使用相同的资源组名称。
    - 名称：必须提供唯一的名称。 使用“woodgroup-vc-kv”，也可以将此值替换为自己独有的名称。
    - 位置：选择一个位置。
    - 使其他选项设置为其默认值。
1. 提供上述信息后，选择“访问策略”。

    ![显示创建秘钥保管库屏幕的屏幕截图。](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

1. 在“访问策略”屏幕，选择“添加访问政策”。 

    >[!NOTE]
    > 默认情况下，仅创建密钥保管库的帐户具有访问权限。 可验证凭据服务需要访问密钥保管库。 如果选择退出，密钥保管库必须规定访问政策，允许管理员创建、删除密钥，并具有登录创建可验证凭据的域绑定的权限。 如果在测试时使用的是同一帐户，请修改默认政策，除了授予保管库创建者默认访问权限以外，授予帐户 **登录** 权限。

1. 对于用户管理员，请确保密钥权限部分已经启用了“创建”、“删除”和“签名”权限。   默认情况下，“创建”和“删除”已启用。  “签名”应该是唯一需要更新的密钥权限。

    ![显示秘钥保管库权限的屏幕截图。](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

1. 选择“查看 + 创建”。
1. 选择“创建”。
1. 转到保管库，记下保管库名称和 URI。

记下以下两个属性：

- “保管库名”：在本示例中，密钥保管库名称为“woodgrove-vc-kv”。  将在其他步骤中使用该名称。
- 保管库 URI：在本示例中，此值为 https://woodgrove-vc-kv.vault.azure.net/ 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

>[!NOTE]
> 每个密钥保管库事务都会产生额外的 Azure 订阅费用。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

>[!IMPORTANT]
> 在 Azure Active Directory 可验证凭据预览期间，在保管库中创建了密钥和机密后请勿修改。 删除、禁用或更新密钥和机密会使任何已颁发的凭据失效。

## <a name="create-modified-rules-and-display-files"></a>创建修改后的规则和显示文件

在本部分中，将使用[示例颁发者应用](https://github.com/Azure-Samples/active-directory-verifiable-credentials/)当中的规则和显示文件，并稍作修改已创建租户的第一个可验证凭据。

1. 将规则和显示 JSON 文件复制到临时文件夹中。 分别将其重命名为“MyFirstVC-display.js”和“MyFirstVC-rules.js”。  可以在“issuer\issuer_config”下同时找到两个文件。

   ![显示示例应用程序目录中的规则和显示文件的屏幕截图。](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![显示临时文件夹中的规则和显示文件的屏幕截图。](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

1. 在代码编辑器里打开“MyFirstVC-rules.json”文件。

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

   现在，将“类型”字段更改为“MyFirstVC”。

   ```json
    "type": ["MyFirstVC"]
  
   ```

   保存此更改。

   >[!NOTE]
   > 在本教程中，暂时不更改“配置”或“client_id”值。 仍将使用[入门](get-started-verifiable-credentials.md)教程中使用的示例 Azure 租户。 在下一教程中，将使用 Azure AD。

1. 在代码编辑器里打开“MyFirstVC-display.json”文件。

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

   做一些修改，让该可验证凭据视觉上与示例代码的版本不同。

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > 为确保凭据的可读且可访问，选择[对比度](https://www.w3.org/WAI/WCAG21/Techniques/general/G18)至少为 4.5:1 的文本和背景色。

   保存次更改。

## <a name="create-a-storage-account"></a>创建存储帐户

在创建第一个可验证凭据之前，创建一个可保存配置和规则文件的 Azure Blob 存储容器。

1. 使用所示选项创建存储帐户。 有关详细步骤，请参阅[创建存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)。

   - “订阅”：在这些教程中使用的订阅
   - “资源组”：在之前的教程中使用过的相同资源组 (vc-resource-group)
   - “名称”：唯一名称
   - “位置”：（美国）美国东部
   - **性能**：标准
   - “帐户类型”：存储 V2
   - “复制”：本地冗余
 
   ![显示创建存储帐户屏幕的屏幕截图。](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

1. 在创建存储帐户后，创建一个容器。 在“Blob 存储”下，选择“容器”。  使用以下值创建容器：

    - “名称”：vc-容器
    - “公共访问级别”：私有（无匿名访问）
  
   选择“创建”。

   ![显示如何创建容器的屏幕截图。](media/enable-your-tenant-verifiable-credentials/new-container.png)

1. 现在，请选择新容器并上传新规则和显示文件，即之前创建的“MyFirstVC-display.json”和“MyFirstVC-rules.json”。 

   ![显示上传规则文件的屏幕截图。](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-a-blob-role"></a>分配 blob 角色

在创建凭据之前，首先需要为已登录的用户授予正确的角色分配，以便他们能够访问存储 Blob 中的文件。

1. 转到“存储” > “容器”。 
1. 选择“访问控制(IAM)”。
1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面  。
1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 存储 Blob 数据读取者 |
    | 将访问权限分配到 | 用户、组或服务主体 |
    | Select | &lt;用于执行这些步骤的帐户&gt; |

    ![显示 Azure 门户中添加角色分配页面的屏幕截图。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

  >[!IMPORTANT]
  >默认情况下，容器创建者会被分配到“所有者”角色。 只有“所有者”角色是不够的。 你的帐户需要“存储 Blob 数据读取器”角色。 有关详细信息，请参阅[使用 Azure 门户分配用于访问 Blob 和队列数据的 Azure 角色](../../storage/blobs/assign-azure-role-data-access.md)。

## <a name="set-up-verifiable-credentials-preview"></a>设置可验证凭据（预览版）

现在，执行最后一个步骤来设置获取可验证凭据的租户。

1. 在 Azure 门户，搜索“可验证凭据”。
1. 选择“可验证凭据（预览版）”。
1. 选择“开始”。 
1. 提供以下信息来设置组织：

      - “组织名称”：输入名称，以便在可验证凭据中引用业务。 此值不面向客户。
      - “域”：输入的域将添加至 DID 文档服务终结点。 [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) 和其他钱包使用该信息验证你的 DID 是否已[链接到你的域](how-to-dnsbind.md)。 如果钱包可以验证 DID，则会显示已验证的符号。 如果钱包无法验证 DID，则会通知用户凭据是由它无法验证的组织颁发的。 域将你的 DID 绑定到用户可能了解的你业务中的内容。
      - “密钥保管库”：输入之前创建的密钥保管库名称。

   >[!IMPORTANT]
   > 域不能重定向。 否则，“DID”和“域”将无法链接。 请确保使用 https://www.domain.com 格式。
  
1. 选择“保存并创建凭据”。

    ![显示设置组织身份的屏幕截图。](media/enable-your-tenant-verifiable-credentials/save-create.png)

恭喜，现已为你的租户启用可验证凭据预览！

## <a name="create-your-verifiable-credential-in-the-portal"></a>在门户中创建可验证凭据

上一步后，将显示“创建新凭据”屏幕。

   ![显示“入门”屏幕的屏幕截图。](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. 凭据名称请输入“MyFirstVC”。 该名称用于在门户中识别可验证凭据。 它作为可验证凭据协定的一部分。
1. 在“显示文件”部分，选择“配置显示文件” 
1. 在“存储帐户”部分，选择“woodgrovevcstorage”。 
1. 从可用容器列表中选择“vc 容器”。
1. 选择先前创建的“MyFirstVC-display.js”。
1. 在“创建新凭据”屏幕的“规则文件”部分，选择“配置规则文件”。
1. 在“存储帐户”部分，选择“woodgrovecstorage”。
1. 选择“vc 容器”。
1. 选择“MyFirstVC-rules.json”。
1. 在“创建新凭据”屏幕上，选择“创建”。

   ![显示如何创建新凭证的屏幕截图。](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>凭据 URL

现在你有了新凭据，请复制凭据 URL。

   ![显示凭据 URL 的屏幕截图。](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>凭据 URL 是规则和显示文件的组合。 它是 Authenticator 在显示为用户可验证凭据颁发要求之前评估的 URL。

## <a name="update-the-sample-app"></a>更新示例应用

现在，对示例应用的颁发者代码进行修改，以便用可验证凭据 URL 更新它。 此步骤使你可以使用自己的租户颁发可验证凭据。

1. 转到放置示例应用文件的文件夹。
1. 打开颁发者文件夹，在 Visual Studio Code 中打开 app.js。
1. 用新的凭据 URL 更新“凭据”常量。 将“credentialType”常量设置为“MyFirstVC”并保存文件。 

    ![显示相关区域高亮显示的 Visual Studio 代码的屏幕截图。](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

1. 打开命令提示符，打开颁发者文件夹。
1. 运行更新后的节点应用。

    ```terminal
    node app.js
    ```

1. 使用其他命令提示符运行 ngrok，在 8081 上设置 URL。 可以使用 [ngrok npm package](https://www.npmjs.com/package/ngrok/) 全局安装 ngrok。

    ```terminal
    ngrok http 8081
    ```

    >[!IMPORTANT]
    > 你可能会看到一条警告，内容为“此应用程序或网站可能有风险。” 此时应该会出现该消息，因为尚未将 DID 链接到域。 手动配置相关说明，请参阅 [DNS 绑定](how-to-dnsbind.md)。

1. 打开 ngrok 生成的 HTTPS URL。

    ![显示 NGROK 转接终结点的屏幕截图。](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

1. 选择“获取凭据”。
1. 在 Authenticator 中扫描 QR 码。
1. 在“此应用或网站可能存在危险”屏幕上，选择“高级”。 

   ![显示初始警告的屏幕截图。](media/enable-your-tenant-verifiable-credentials/site-warning.png)

1. 在下一个“此应用或网站可能存在危险”屏幕上，选择“仍继续（不安全）”。 

   ![显示第二个有关颁发者的警告的屏幕截图。](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)

1. 在“添加凭据”屏幕上，请注意以下几点：
    1. 在屏幕顶部，可以看到红色的“未验证”消息。
    1. 凭据根据对显示文件所做的更改进行自定义。
    1. “登录帐户”选项指向 didplayground.b2clogin.com。
    
      ![显示“添加凭据”屏幕并显示警告的屏幕截图。](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

1. 选择“登录帐户”，然后使用在[入门](get-started-verifiable-credentials.md)教程中提供的凭据信息进行身份验证。
1. 身份验证成功后，“添加”按钮激活。 选择 **添加** 。

    ![显示身份验证后“添加凭据”屏幕的屏幕截图。](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

   通过租户生成可验证凭据，同时仍使用示例 Azure B2C 租户进行身份验证，即可颁发可验证凭据。

     ![显示由 Azure AD 颁发且由示例 Azure B2C 租户身份验证的可验证凭据的屏幕截图。](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)

## <a name="verify-the-verifiable-credential-by-using-the-sample-app"></a>使用示例应用验证可验证凭据

现在，已从租户颁发了可验证凭据，接下来使用示例应用对其验证。

>[!IMPORTANT]
> 测试时，请使用在[入门](get-started-verifiable-credentials.md)教程中使用过的电子邮件和密码。 租户颁发 vc 时，输入来自示例 Azure B2C 租户颁发的 ID 令牌。 在第二个教程中，将令牌颁发切换到租户。

1. 在 Azure 门户的“可验证凭据”窗格中选择“设置”。  复制 DID

   ![显示复制 DID 的屏幕截图。](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

1. 打开示例应用文件中的验证程序文件夹。 需要更新验证程序示例代码中的 app.js 文件，然后进行以下更改：

    - “凭据”：切换至凭据 URL
    - “凭据类型”：输入“MyFirstVC”。
    - “颁发者 DID”：从 Azure 门户 >“可验证凭据（预览）” > “设置” > “分散识别符 (DID)”复制此值。  
    
   ![显示更新常量颁发者 DID 以匹配颁发者标识符的屏幕截图。](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

1. 停止运行颁发者 ngrok 服务。

    ```cmd
    control-c
    ```

1. 现在，请运行包含验证程序端口 8082 的 ngrok。

    ```cmd
    ngrok http 8082
    ```

1. 在另一个终端窗口中，转至验证程序应用，并按照与运行颁发者应用相似的方式运行。

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

1. 在浏览器中打开 ngrok URL，使用移动设备中的 Authenticator 扫描 QR 码。
1. 在移动设备的“新权限请求”屏幕上，选择“允许”。 

    >[!NOTE]
    > DID 签名此可验证凭据仍是 Azure B2C。 验证程序 DID 仍来自 Microsoft 示例应用租户。 由于 Microsoft DID 已链接至我们的域中，因此不会弹出之前在颁发流程中看到的警告。 这种情况在下一部分更新。
    
   ![显示“新建权限请求”屏幕的屏幕截图。](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

1. 现在我们已经成功验证了凭据。

## <a name="next-steps"></a>后续步骤

现在，你已拥有从颁发者颁发可验证凭据的示例代码，让我们继续下一部分。 接下来我们将学习如何使用自己的标识提供者，对尝试获取可验证凭据的用户进行身份验证。 以及如何使用 DID 对演示文稿请求进行签名。

> [!div class="nextstepaction"]
> [教程：使用租户颁发和识别可验证凭据](issue-verify-verifiable-credentials-your-tenant.md)