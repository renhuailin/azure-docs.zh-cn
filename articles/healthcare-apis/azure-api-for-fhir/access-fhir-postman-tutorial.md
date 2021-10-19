---
title: Azure 中的 Postman FHIR 服务器 - Azure API for FHIR
description: 在本教程中，我们将演练使用 Postman 访问 FHIR 服务器所需的步骤。 Postman 有助于调试用于访问 API 的应用程序。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 1080b8fb47ebcab07737d26464302a9e18465315
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783309"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>使用 Postman 访问 Azure API for FHIR

客户端应用程序可通过 [REST API](https://www.hl7.org/fhir/http.html) 访问 Azure API for FHIR。 若要在生成应用程序时发送请求、查看响应和调试应用程序，请使用所选的 API 测试工具。 在本教程中，我们将指导你完成使用 [Postman](https://www.getpostman.com/)访问 FHIR 服务器的步骤。 

## <a name="prerequisites"></a>先决条件

- Azure 中存在 FHIR 终结点。 

  若要部署 Azure API for FHIR (托管服务) ，可以使用 [Azure 门户](fhir-paas-portal-quickstart.md)、 [PowerShell](fhir-paas-powershell-quickstart.md)或 [Azure CLI](fhir-paas-cli-quickstart.md)。

- 用于访问 FHIR 服务的已注册 [机密客户端应用程序](register-confidential-azure-ad-client-app.md) 。
- 你已向机密客户端应用程序和你的用户帐户（例如 "FHIR 数据参与者"）授予访问 FHIR 服务的权限。 有关详细信息，请参阅 [配置 FHIR 的 AZURE RBAC](./configure-azure-rbac.md)。
- 已安装 Postman。 
    
  有关 Postman 的详细信息，请参阅[与 Postman 入门](https://www.getpostman.com)。

## <a name="fhir-server-and-authentication-details"></a>FHIR 服务器和身份验证详细信息

若要使用 Postman，需要以下身份验证参数：

- FHIR 服务器 URL，例如 `https://MYACCOUNT.azurehealthcareapis.com`

- FHIR 服务器的标识提供者 `Authority`，例如 `https://login.microsoftonline.com/{TENANT-ID}`

- 配置 `audience` 的，它通常是 FHIR 服务器的 URL，例如 `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` 或 `https://azurehealthcareapis.com` 。

- `client_id`用于访问 FHIR 服务的[机密客户端应用程序](register-confidential-azure-ad-client-app.md)的或应用程序 ID。

- `client_secret`机密客户端应用程序的或应用程序机密。

最后，应检查 `https://www.getpostman.com/oauth2/callback` 是否是客户端应用程序的已注册回复 URL。

## <a name="connect-to-fhir-server"></a>连接到 FHIR 服务器

打开 "Postman"，然后选择 " **获取** " 向发出请求 `https://fhir-server-url/metadata` 。

![Postman 元数据功能语句](media/tutorial-postman/postman-metadata.png)

用于 Azure API for FHIR 的元数据 URL 是 `https://MYACCOUNT.azurehealthcareapis.com/metadata`。 

在此示例中，FHIR 服务器 URL 为 `https://fhirdocsmsft.azurewebsites.net` ，而服务器的功能声明在上可用 `https://fhirdocsmsft.azurewebsites.net/metadata` 。 无需身份验证即可访问此终结点。

如果尝试访问受限资源，则会发生 "身份验证失败" 响应。

![身份验证失败](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>获取访问令牌
选择“获取新的访问令牌”。

若要获取有效的访问令牌，请选择 "**授权**"，然后从 "**类型**" 下拉菜单中选择 " **OAuth 2.0** "。

![设置 OAuth 2.0](media/tutorial-postman/postman-select-oauth-two.png)

选择“获取新的访问令牌”。

![请求新的访问令牌](media/tutorial-postman/postman-request-token.png)

在 " **获取新访问令牌** " 对话框中，输入以下详细信息：

| 字段                 | 示例值                                                                                                   | 注释                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| 令牌名称            | MYTOKEN                                                                                                         | 选择的名称          |
| 授权类型            | 授权代码                                                                                              |                            |
| 回调 URL          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| 身份验证 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` 是 Azure API for FHIR 的 `https://MYACCOUNT.azurehealthcareapis.com` |
| 访问令牌 URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| 客户端 ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | 应用程序 ID             |
| 客户端机密         | `XXXXXXXX`                                                                                                      | 机密客户端密钥          |
| 范围 | `<Leave Blank>` | 不使用范围;因此，它可以保留为空。  
| 状态                 | `1234`     | [State](https://learning.postman.com/docs/sending-requests/authorization/) 是防止跨站点请求伪造的不透明值。 它是可选的，可以采用任意值，如 "1234"。                           |
| 客户端身份验证 | 在正文中发送客户端凭据                                                                                 |                 

选择要通过 Azure Active Directory Authentication flow 指导的 **请求令牌**，并将令牌返回到 Postman。 如果身份验证失败，请参阅 Postman 控制台了解更多详细信息。 **注意**：在功能区上，选择 " **视图**"，然后选择 " **显示 Postman 控制台**"。 Postman 控制台的键盘快捷方式是 **Alt-Ctrl + C**。

向下滚动以查看返回的令牌屏幕，然后选择 " **使用令牌**"。

![使用令牌](media/tutorial-postman/postman-use-token.png)

请参阅 **访问令牌** 字段，查看新填充的令牌。 如果选择 " **发送** " 以重复 `Patient` 资源搜索，则将返回 **状态** `200 OK` 。 返回的状态 `200 OK` 表明 HTTP 请求成功。

![200 正常](media/tutorial-postman/postman-two-hundred-ok.png)

在 *患者搜索* 示例中，数据库中没有患者，因此搜索结果为空。

可以使用 [jwt.ms](https://jwt.ms)之类的工具来检查访问令牌。 下面显示了内容的一个示例。

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

在进行故障排除时，最好首先验证你是否拥有正确的受众（`aud` 声明）。 如果令牌来自正确的颁发者 (`iss` 声明) 并且具有正确的受众 (`aud` 声明) ，但仍无法访问 FHIR API，则可能是用户或服务主体 (`oid` 声明) 无权访问 FHIR 数据平面。 建议使用 azure [RBAC)  (azure 基于角色的访问控制 ](configure-azure-rbac.md) 将数据平面角色分配给用户。 如果对数据平面使用的是外部的辅助 Azure Active directory 租户，则需要为 [FHIR 分配配置本地 RBAC](configure-local-rbac.md) 。

还可以 [使用 Azure CLI 获取用于 FHIR 的 AZURE API](get-healthcare-apis-access-token-cli.md)的令牌。 如果你使用的令牌是使用 Azure CLI 获取的，则应使用授权类型 *持有者令牌*。 直接粘贴标记。

## <a name="inserting-a-patient"></a>插入患者

使用有效的访问令牌，现在可以插入新的患者。 在 Postman 中，通过选择 " **Post**" 更改方法，然后将以下 JSON 文档添加到请求正文中。

[!code-json[](../samples/sample-patient.json)]

选择 " **发送** " 以确定患者是否已成功创建。

![显示患者已成功创建的屏幕截图。](media/tutorial-postman/postman-patient-created.png)

如果重复患者搜索，现在应会看到患者记录。

![已创建患者](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>后续步骤

在本教程中，已使用 Postman 访问了用于 FHIR 的 Azure API。 有关用于 FHIR 的 Azure API 功能的详细信息，请参阅
 
>[!div class="nextstepaction"]
>[受支持的功能](fhir-features-supported.md)
