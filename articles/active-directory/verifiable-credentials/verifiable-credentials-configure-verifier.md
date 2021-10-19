---
title: 教程 - 配置 Azure AD 可验证凭据验证程序（预览版）
description: 本教程将介绍如何配置租户以验证可验证凭据
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 10/08/2021
ms.openlocfilehash: 4faed2beb4f31537453dc60805467c9128d5cc0a
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730885"
---
# <a name="configure-azure-ad-verifiable-credentials-verifier-preview"></a>配置 Azure AD 可验证凭据验证程序（预览版）

在[上一教程](verifiable-credentials-configure-issuer.md)中介绍了如何使用相同的 Azure Active Directory (Azure AD) 租户颁发和验证凭据。 本教程将详细介绍所需的步骤，以演示和验证你的首个可验证凭据：经过验证的凭据专家卡。

作为验证方，你可以解锁拥有经过验证的凭据专家卡的主体的特权。 在本教程中，你将从本地计算机运行一个示例应用程序，它会要求你提供经过验证的凭据专家卡并进行验证。

在本文中，学习如何：

> [!div class="checklist"]
>
> - 将示例应用程序代码下载到本地计算机
> - 在 Azure AD 租户上设置 Azure AD 可验证凭据
> - 收集凭据和环境详细信息以设置示例应用程序，然后使用经过验证的凭据专家卡详细信息更新示例应用程序
> - 运行示例应用程序并启动可验证凭据颁发进程

## <a name="prerequisites"></a>先决条件

- 在开始之前，请务必[为 Azure AD 可验证凭据设置租户](verifiable-credentials-configure-tenant.md)
- 如果要克隆托管示例应用程序的存储库，则需要安装 [GIT](https://git-scm.com/downloads)。
- [Visual Studio Code](https://code.visualstudio.com/Download) 或类似代码编辑器
- [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
- [NGROK](https://ngrok.com/) 免费版。
- 安装了 Microsoft Authenticator 的移动设备
  - 已安装 Android 版本 6.2108.5654 或更高版本。
  - 已安装 iOS 版本 6.5.82 或更高版本。

## <a name="gather-tenant-details-to-set-up-your-sample-application"></a>收集租户详细信息以设置示例应用程序

设置 Azure AD 可验证凭据服务后，收集有关环境和设置的可验证凭据的一些信息。 设置示例应用程序时，会用到这些信息。

1. 从可验证凭据中，选择“组织设置”。
1. 复制“租户标识符”并记下它供将来使用。
1. 复制“分散式标识符”并记下它供将来使用。

以下屏幕截图演示了复制所需值的方法：

![屏幕截图演示了如何从 Azure AD 可验证凭据复制所需值。](media/verifiable-credentials-configure-verifier/tenant-settings.png)

## <a name="download-the-sample-code"></a>下载示例代码

示例应用程序在 .NET 中提供，代码保留在 GitHub 存储库中。 从 [GitHub 存储库](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet)下载示例代码，或将存储库克隆到本地计算机：

```bash
git clone git@github.com:Azure-Samples/active-directory-verifiable-credentials-dotnet.git 
```

## <a name="configure-the-verifiable-credentials-app"></a>配置可验证凭据应用

为你创建的已注册应用程序创建客户端密码。 该示例应用程序请求令牌时使用客户端密码来证明其身份。

1. 导航到位于“Azure Active Directory”内的“应用注册”页 

1. 选择此前创建的“verifiable-credentials-app”应用程序。

1. 选择名称以进入“应用注册详细信息”

1. 复制“应用程序(客户端) ID”并保存它供以后使用。 

    ![显示如何获取应用 ID 的屏幕截图。](media/verifiable-credentials-configure-verifier/get-app-id.png)

1. 在“应用注册详细信息”中，从主菜单的“管理”下选择“证书和密码” 。

1. 选择“新建客户端密码”

    1. 在“说明”框中输入客户端密码的说明（例如 vc-sample-secret）。

    1. 在“过期时间”下，选择密码的有效期（例如 6 个月），然后选择“添加” 。

    1. 记下机密的“值”。 在稍后的步骤中将使用此值进行配置。 该密码值将不会再次显示，也无法通过其他任何方式检索，因此，请在该值显示时立即将其记下来。

此时，你应具有设置示例应用程序所需的所有必需信息。

## <a name="update-the-sample-application"></a>更新示例应用程序

现在，对示例应用的颁发者代码进行修改，以便用可验证凭据 URL 更新它。 此步骤使你可以使用自己的租户颁发可验证凭据。

1. 在“active-directory-verifiable-credentials-dotnet-main”目录中，打开 Visual Studio Code，然后选择“1. asp-net-core-api-idtokenhint”目录中的项目 。

1. 在项目根文件夹下，打开 appsettings.json 文件。 此文件包含了有关 Azure AD 可验证凭据的信息。 使用之前在上述步骤中记录的信息更新以下属性

    1. **租户 ID**：你的租户 ID
    1. **客户端 ID**：你的客户端 ID
    1. **客户端密码**：你的客户端密码
    1. **VerifierAuthority**：你的去中心化标识符
    1. **CredentialManifest**：你的颁发凭据 URL

1. 保存 appsettings.json 文件。

下面的 JSON 演示了一个完整的 appsettings.json 文件：

```json
{

 "AppSettings": {
   "Endpoint": "https://beta.did.msidentity.com/v1.0/{0}/verifiablecredentials/request",
   "VCServiceScope": "bbb94529-53a3-4be5-a069-7eaf2712b826/.default",
   "Instance": "https://login.microsoftonline.com/{0}",
   "TenantId": "987654321-0000-0000-0000-000000000000",
   "ClientId": "555555555-0000-0000-0000-000000000000",
   "ClientSecret": "123456789012345678901234567890",
   "VerifierAuthority": "did:ion:EiDJzvzaBMb_EWTWUFEasKzL2nL-BJPhQTzYWjA_rRz3hQ:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfMmNhMzY2YmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiZDhqYmduRkRGRElzR1ZBTWx5aDR1b2RwOGV4Q2dpV3dWUGhqM0N...",
   "CredentialManifest": " https://beta.did.msidentity.com/v1.0/987654321-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert"
 }
}
```

## <a name="run-and-test-the-sample-app"></a>运行并测试示例应用

现在你可以通过运行示例应用程序来演示和验证你的首个经过验证的专家卡。

1. 在 Visual Studio Code 中，运行 Verifiable_credentials_DotNet 项目。 或在命令 shell 中运行以下命令：

    ```bash
    cd active-directory-verifiable-credentials-dotnet/1. asp-net-core-api-idtokenhint  dotnet build "asp-net-core-api-idtokenhint.csproj" -c Debug -o .\bin\Debug\netcoreapp3.1  
    dotnet run
    ```

1. 在另一终端中，运行以下命令。 此命令运行 [ngrok](https://ngrok.com/) 以在 3000 上设置 URL，并使其在 Internet 上可公开使用。

    ```bash
    ngrok http 3000 
    ```
    
    >[!NOTE]
    > 在某些计算机上可能需要以此格式 `./ngrok http 3000` 运行命令

1. 打开 ngrok 生成的 HTTPS URL。

    ![显示如何获取 ngrok 公共 URL 的屏幕截图。](media/verifiable-credentials-configure-verifier/run-ngrok.png)

1. 在 Web 浏览器中，选择“验证凭据”。

    ![显示如何从示例应用中验证凭据的屏幕截图。](media/verifiable-credentials-configure-verifier/verify-credential.png)

1. 使用 Authenticator 应用扫描 QR 码，或者直接通过移动相机进行扫描。

1. 在“此应用或网站可能存在风险”警告消息中选择“高级” 。 显示此警告是因为你的域未经验证。 若要验证域，请按照“将域关联到去中心化标识符 (DID)”一文中的指南操作。 对于本教程，可跳过域注册。  

    ![显示如何在“Authenticator 应用有风险”警告上选择“高级”的屏幕截图。](media/verifiable-credentials-configure-verifier/at-risk.png)
    

1. 在出现“网站有风险”的警告时，选择“仍然继续(不安全)”。  
 
    ![显示如何在显示“有风险”警告的情况下继续操作的屏幕截图。](media/verifiable-credentials-configure-verifier/proceed-anyway.png)

1. 选择“允许”以批准请求。

    ![显示如何批准出示请求的屏幕截图。](media/verifiable-credentials-configure-verifier/approve-presentation-request.jpg)

1. 批准请求后，可以看到请求已获得批准。 还可查看日志。 若要查看日志，请选择可验证凭据：

    ![显示可验证凭据专家卡的屏幕截图。](media/verifiable-credentials-configure-verifier/verifable-credential-info.png)

1. 然后选择“最近活动”。  

    ![显示“最近活动”按钮的屏幕截图，该按钮将转到凭据历史记录。](media/verifiable-credentials-configure-verifier/verifable-credential-history.jpg)

1. “最近活动”可显示可验证凭据的近期活动。

    ![显示可验证凭据历史记录的屏幕截图。](media/verifiable-credentials-configure-issuer/verify-credential-history.jpg)

1. 返回到示例应用。 它显示了收到的可验证凭据的表示形式。

    ![显示收到的可验证凭据的表示形式的屏幕截图](media/verifiable-credentials-configure-verifier/presentation-received.png)

## <a name="next-steps"></a>后续步骤

了解[如何自定义可验证凭据](credential-design.md)
