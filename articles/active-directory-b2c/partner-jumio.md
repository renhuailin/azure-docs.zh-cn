---
title: 为 Azure Active Directory B2C 配置 Jumio 的教程
titleSuffix: Azure AD B2C
description: 在本教程中，你将为 Azure Active Directory B2C 配置 Jumio 以实现自动化 ID 验证，从而保护客户数据。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: e344c849a8e9021daea9caebacec3289b99d03e6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256661"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>为 Azure Active Directory B2C 配置 Jumio 的教程

在本示例教程中，我们提供了有关如何将 Azure Active Directory B2C (Azure AD B2C) 与 [Jumio](https://www.jumio.com/) 集成的指导。 Jumio 是一项 ID 验证服务，可以实时自动验证 ID，以帮助保护客户数据。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已关联到你的 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

## <a name="scenario-description"></a>方案描述

Jumio 集成包括以下组件：

- Azure AD B2C：负责验证用户凭据的授权服务器。 它也称为“标识提供者”。

- Jumio：此服务接受用户提供的 ID 详细信息并对其进行验证。

- 中间 Rest API：此 API 实现 Azure AD B2C 与 Jumio 服务之间的集成。

- Azure Blob 存储：此服务向 Azure AD B2C 策略提供自定义 UI 文件。

以下体系结构图体现了实施详情。

![Azure AD B2C 与 Jumio 的集成体系结构示意图。](./media/partner-jumio/jumio-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户访问某个页面进行登录，或者进行注册以创建帐户。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户属性。
| 3. | 中间层 API 收集用户属性，并将这些属性转换为 Jumio API 可以使用的格式。 然后，它将属性发送到 Jumio。
| 4. | Jumio 使用这些信息并对其进行处理，然后将结果返回到中间层 API。
| 5. | 中间层 API 处理信息并将相关信息发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收从中间层 API 返回的信息。 如果它显示“失败”响应，则会向用户显示错误消息。 如果它显示“成功”响应，则表明用户已通过身份验证并会被写入到目录中。

## <a name="sign-up-with-jumio"></a>向 Jumio 注册

若要创建 Jumio 帐户，请联系 [Jumio](https://www.jumio.com/contact/)。

## <a name="configure-azure-ad-b2c-with-jumio"></a>为 Azure AD B2C 配置 Jumio

创建 Jumio 帐户后，使用该帐户来配置 Azure AD B2C。 以下各部分按顺序介绍了这一过程。

### <a name="deploy-the-api"></a>部署 API

将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api)部署到 Azure 服务。 可以按照[这些说明](/visualstudio/deployment/quickstart-deploy-to-azure)从 Visual Studio 发布代码。

>[!NOTE]
>你需要已部署服务的 URL，以便为 Azure AD 配置所需设置。

### <a name="deploy-the-client-certificate"></a>部署客户端证书

1. 客户端证书帮助保护 Jumio API 调用。 使用以下 PowerShell 示例代码创建自签名证书：

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   然后，将证书导出到为 ``{your-local-path}`` 指定的位置。

3. 按照[此文](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)中的说明操作，将证书导入到 Azure 应用服务。

### <a name="create-a-signingencryption-key"></a>创建签名密钥/加密密钥

创建长度超过 64 个字符且仅包含字母和数字的随机字符串。

例如： ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

使用下面的 PowerShell 脚本创建字符串：

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>配置 API

你可以[在 Azure 应用服务中配置应用程序设置](../app-service/configure-common.md#configure-app-settings)。 使用此方法，你可以安全地配置设置，不需要将其签入到存储库中。 你需要向 Rest API 提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio 帐户配置 |     |
|JumioSettings:AuthPassword | Jumio 帐户配置 |     |
|AppSettings:SigningCertThumbprint|创建的自签名证书的指纹|  |
|AppSettings:IdTokenSigningKey| 使用 PowerShell 创建的签名密钥 | |
| AppSettings:IdTokenEncryptionKey |使用 PowerShell 创建的加密密钥
| AppSettings:IdTokenIssuer | 要用于 JWT 令牌的颁发者（最好使用 GUID 值） |
| AppSettings:IdTokenAudience  | 要用于 JWT 令牌的受众（最好使用 GUID 值） |
|AppSettings:BaseRedirectUrl | Azure AD B2C 策略的基 URL | https://{your-tenant-name}.b2clogin.com/{your-application-id}|
| WEBSITE_LOAD_CERTIFICATES| 创建的自签名证书的指纹 |

### <a name="deploy-the-ui"></a>部署 UI

1. 设置[存储帐户中的 Blob 存储容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。

2. 将 UI 文件从 [UI 文件夹](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI)存储到 Blob 容器中。

#### <a name="update-ui-files"></a>更新 UI 文件

1. 在 UI 文件中，转到文件夹 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)。

2. 打开每个 HTML 文件。

3. 找到 `{your-ui-blob-container-url}` 并将其替换为你的 Blob 容器的 URL。

4. 找到 `{your-intermediate-api-url}` 并将其替换为中间 API 应用服务的 URL。

>[!NOTE]
> 建议你在属性收集页中添加同意通知，这是最佳做法。 通知用户该信息将会发送到第三方服务，用于标识验证。

### <a name="configure-the-azure-ad-b2c-policy"></a>配置 Azure AD B2C 策略

1. 转到 Policies 文件夹中的 [Azure AD B2C 策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies)。

2. 按照[此文](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)中的步骤下载 [LocalAccounts 初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)。

3. 为 Azure AD B2C 租户配置策略。

>[!NOTE]
>更新提供的策略，使其与你的特定租户相关。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户。 在“策略”下，选择“Identity Experience Framework”。 

2. 选择前面创建的“SignUpSignIn”。

3. 选择“运行用户流”，然后：

   a. 对于“应用程序”，选择已注册的应用（示例为 JWT）。

   b. 对于“回复 URL”，选择“重定向 URL”。

   c. 选择“运行用户流”。

4. 完成注册流并创建帐户。

5. 创建用户属性后，将在流中调用 Jumio 服务。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
