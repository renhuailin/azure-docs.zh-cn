---
title: 保护在 Azure AD 自助注册用户流中用作 API 连接器的 API
description: 保护在自助注册用户流中用作 API 连接器的自定义 RESTful API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/16/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9f580c903ccc806ab4cfe5229c4931a4810bf8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481269"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-external-identities-self-service-sign-up-user-flows"></a>保护在 Azure AD 外部标识自助注册用户流中用作 API 连接器的 API

在 Azure AD 外部标识自助注册用户流中集成 REST API 时，必须使用身份验证来保护 REST API 终结点。 REST API 身份验证可确保只有具备适当凭据的服务（例如 Azure AD）可以调用该终结点。 本文探讨如何保护 REST API。 

## <a name="prerequisites"></a>先决条件
完成[演练：向注册用户流添加 API 连接器](self-service-sign-up-add-api-connector.md)指南中的步骤。

可以使用 HTTP 基本身份验证或 HTTPS 客户端证书身份验证来保护 API 终结点。 对于这两种情况，都要提供凭据，供 Azure AD 在调用 API 终结点时使用。 然后，API 终结点将检查凭据并执行授权决策。


## <a name="http-basic-authentication"></a>HTTP 基本身份验证

HTTP 基本身份验证在 [RFC 2617](https://tools.ietf.org/html/rfc2617) 中进行定义。 基本身份验证的工作方式如下：Azure AD 使用 `Authorization` 头中的客户端凭据（`username` 和 `password`）发送 HTTP 请求。 凭据的格式是 base64 编码的字符串 `username:password`。 然后，API 负责检查这些值以执行其他授权决策。

若要为 API 连接器配置 HTTP 基本身份验证，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD” 。
3. 选择“API 连接器”，然后选择要配置的“API 连接器” 。
4. 对于“身份验证类型”，请选择“基本”。 
5. 提供 REST API 终结点的“用户名”和“密码”。 
    :::image type="content" source="media/secure-api-connector/api-connector-config.png" alt-text="为 API 连接器提供基本身份验证配置。":::
6. 选择“保存”。

## <a name="https-client-certificate-authentication"></a>HTTPS 客户端证书身份验证

客户端证书身份验证是一种基于证书的相互身份验证，其中客户端 Azure AD 向服务器提供客户端证书来证明自己的身份。 此操作在 SSL 握手期间发生。 API 负责验证属于有效客户端（例如 Azure AD）的证书，并执行授权决策。 客户端证书是一种 X.509 数字证书。 

> [!IMPORTANT]
> 在生产环境中，证书必须由证书颁发机构进行签名。

### <a name="create-a-certificate"></a>创建证书

#### <a name="option-1-use-azure-key-vault-recommended"></a>选项 1：使用 Azure 密钥保管库（建议）

若要创建证书，可以使用 [Azure 密钥保管库](../../key-vault/certificates/create-certificate.md)，其中提供了适用于自签名证书的选项，以及与证书颁发者提供程序相集成以便为证书签名的选项。 建议的设置包括：
- **使用者**：`CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **内容类型**：`PKCS #12`
- **生存期操作类型**：`Email all contacts at a given percentage lifetime` 或 `Email all contacts a given number of days before expiry`
- **密钥类型**：`RSA`
- **密钥大小**：`2048`
- 可导出的私钥：`Yes`（以便能够导出 `.pfx` 文件）

然后，你可以[导出该证书](../../key-vault/certificates/how-to-export-certificate.md)。

#### <a name="option-2-prepare-a-self-sized-certificate-using-powershell-module"></a>选项 2：使用 PowerShell 模块准备自签名证书

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="configure-your-api-connector"></a>配置 API 连接器

若要为 API 连接器配置客户端证书身份验证，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure AD” 。
3. 选择“API 连接器”，然后选择要配置的“API 连接器” 。
4. 对于“身份验证类型”，请选择“证书”。 
5. 在“上传证书”框中，选择包含私钥的证书 .pfx 文件。
6. 在“输入密码”框中，键入证书的密码。
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="为 API 连接器提供证书身份验证配置。":::
7. 选择“保存”。

### <a name="perform-authorization-decisions"></a>执行授权决策 
API 必须根据发送的客户端证书实现授权，以便保护 API 终结点。 对于 Azure 应用服务和 Azure Functions，请参阅[配置 TLS 相互身份验证](../../app-service/app-service-web-configure-tls-mutual-auth.md)来了解如何通过 API 代码启用和验证证书。  也可以将 Azure API 管理用作任何 API 服务前面的层，以根据所需值[检查客户端证书属性](
../../api-management/api-management-howto-mutual-certificates-for-clients.md)。

### <a name="renewing-certificates"></a>续订证书
建议针对证书过期时间设置提醒警报。 当所使用的证书即将到期时，需要生成新的证书并重复上述步骤。 为了“滚动”使用新证书，API 服务可以在部署新证书期间继续暂时接受新旧证书。 

若要将新证书上传到现有 API 连接器，请在“API 连接器”下选择该 API 连接器，然后单击“上传新证书”。  Azure AD 将自动使用最近上传的未过期且已超过开始日期的证书。

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text=" API 连接器已存在，请向其提供新证书。":::

## <a name="api-key-authentication"></a>API 密钥身份验证

某些服务使用“API 密钥”机制，要求调用方包含一个唯一密钥作为 HTTP 头或 HTTP 查询参数，以模糊处理在开发过程中对 HTTP 端点的访问。 对于 [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)，可以通过在 API 连接器的“终结点 URL”中将 `code` 添加为查询参数来实现此目的。 例如 `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>。 

在生产环境中不应单独使用此机制。 因此，始终需要基本身份验证或证书身份验证的配置。 如果你不希望出于开发目的实现任何身份验证方法（不建议），可以在 API 连接器配置中选择“基本”身份验证，并对 `username` 和 `password` 使用临时值，API 在实现适当的授权时可忽略这些值。

## <a name="next-steps"></a>后续步骤
- 参考[快速入门示例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)帮助自己入门。