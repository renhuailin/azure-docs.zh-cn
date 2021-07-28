---
title: Azure Active DirectorySMART on FHIR 代理
description: 本教程介绍如何使用代理通过客户端启用 SMART on FHIR Azure API for FHIR。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: zxue
ms.date: 04/02/2019
ms.openlocfilehash: 77f4d9fad9bca4d544277bc719f633779c920606
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769161"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>教程：Azure Active Directory SMART on FHIR 代理

[SMART on FHIR](https://docs.smarthealthit.org/) 是一套开放式规范，用于将合作伙伴应用程序与 FHIR 服务器和具有 FHIR 接口的电子医疗记录系统集成。 规范的主要用途之一是描述应用程序如何发现 FHIR 服务器的身份验证终结点并启动身份验证序列。 

身份验证基于 OAuth2。 但由于 SMART on FHIR 使用与 Azure Active Directory (Azure AD) 不立即兼容的参数命名约定，Azure API for FHIR 具有内置的 Azure AD SMART on FHIR 代理，该代理启用 SMART on FHIR 启动序列的子集。 具体而言，代理启用 [EHR 启动序列](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)。

本教程介绍如何使用代理通过客户端启用 SMART on FHIR Azure API for FHIR。

## <a name="prerequisites"></a>先决条件

- 实例Azure API for FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>配置Azure AD注册

SMART on FHIR 要求 `Audience` 标识符 URI 等于 FHIR 服务的 URI。 参数的标准Azure API for FHIR使用 `Audience` 值 `https://azurehealthcareapis.com` 。 但是，还可以设置与 FHIR 服务特定 URL 匹配的值， (例如 `https://MYFHIRAPI.azurehealthcareapis.com`) 。 使用 SMART on FHIR 代理时，这是必需的。

还需要客户端应用程序注册。 大多数 SMART on FHIR 应用程序都是单页 JavaScript 应用程序。 因此，应按照有关配置公共客户端应用程序 [Azure AD的说明进行操作](register-public-azure-ad-client-app.md)。

完成这些步骤后，应具有：

- rge 受众设置为 的 FHIR 服务器，其中 是实例 `https://MYFHIRAPI.azurehealthcareapis.com` `MYFHIRAPI` Azure API for FHIR的名称。
- 公共客户端应用程序注册。 记下此客户端应用程序的应用程序 ID。

### <a name="set-admin-consent-for-your-app"></a>为应用设置管理员同意

若要使用 SMART on FHIR，必须先对应用进行身份验证和授权。 首次使用 SMART on FHIR 时，还必须获得管理许可，使应用能够访问 FHIR 资源。

如果在应用中没有所有权角色，请联系应用所有者，并要求他们授予你在应用中的管理员同意。 

如果具有管理权限，请完成以下步骤以直接向自己授予管理员许可。  (稍后在 app.) 系统提示时，也可以向自己授予管理员同意。) 可以完成相同的步骤将其他用户添加为所有者，以便他们可以查看和编辑此应用注册。

将自己或其他用户添加为应用的所有者：

1. 在 Azure 门户中，转到“Azure Active Directory”。
1. 在左侧菜单中，选择"应用 **注册"。**
1. 搜索创建的应用注册，然后选择它。
1. 在左侧菜单的"管理 **"下，** 选择"**所有者"。**
1. 选择 **"添加** 所有者"，然后添加自己或想要获得管理员同意的用户。
1. 选择“保存”。

## <a name="enable-the-smart-on-fhir-proxy"></a>启用 SMART on FHIR 代理

选中"SMART on FHIR代理"复选框，在 Azure API for FHIR 实例的身份验证设置中启用 **SMART on FHIR** 代理：

![用于启用 SMART on FHIR 代理的选择](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>启用 CORS

由于大多数 SMART on FHIR 应用程序都是单页 JavaScript 应用，因此需要在 [CORS ](configure-cross-origin-resource-sharing.md) (启用跨) 资源共享Azure API for FHIR：

![用于启用 CORS 的选择](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>配置回复 URL

SMART on FHIR 代理充当 SMART on FHIR 应用与应用之间的Azure AD。 身份验证回复 (身份验证) 必须转到 SMART on FHIR 代理，而不是应用本身。 然后，代理将回复转发到应用。 

由于身份验证代码的这两步中继，需要将 Azure AD 客户端应用程序的回复 URL (回调) 设置为 URL，该 URL 是 SMART on FHIR 代理的回复 URL 和 SMART on FHIR 应用的回复 URL 的组合。 组合的回复 URL 采用以下格式：

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

在回复中， 是 SMART on FHIR 应用回复 URL 的 URL 安全 `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` base64 编码版本。 对于 SMART on FHIR 应用启动器，当应用在本地运行时，回复 URL 为 `https://localhost:5001/sampleapp/index.html` 。 

可以使用如下所示的脚本生成组合回复 URL：

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

将回复 URL 添加到之前为应用程序创建的公共客户端Azure AD：

![为公共客户端配置的回复 URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>获取测试患者

若要测试Azure API for FHIR SMART on FHIR 代理，数据库中需要至少有一名患者。 如果尚未与 API 交互，并且数据库中没有数据，请按照 [FHIR API Postman](access-fhir-postman-tutorial.md) 教程加载患者。 记下特定患者 ID。

## <a name="download-the-smart-on-fhir-app-launcher"></a>下载 SMART on FHIR 应用启动器

适用于 Azure 的开源 [FHIR 服务器存储库](https://github.com/Microsoft/fhir-server) 包括简单的 SMART on FHIR 应用启动器和示例 SMART on FHIR 应用。 在本教程中，在本地使用此 SMART on FHIR 启动器来测试设置。

可以使用以下GitHub克隆该存储库并转到应用程序：

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

应用程序需要一些配置设置，可以在 中设置 `appsettings.json` 这些设置：

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

我们建议使用 此功能 `dotnet user-secrets` ：

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

使用此命令运行应用程序：

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>测试 SMART on FHIR 代理

启动 SMART on FHIR 应用启动器后，可以将浏览器指向 `https://localhost:5001` ，应会看到以下屏幕：

![SMART on FHIR 应用启动器](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

输入"**患者****"、"遇到**"或"医生"信息时，你会注意到"**启动"上下文** 已更新。 使用患者和患者Azure API for FHIR，启动上下文只是一个 JSON 文档，其中包含有关患者、医生等的信息。 此启动上下文是 base64 编码的，并作为查询参数传递给 SMART on FHIR `launch` 应用。 根据 SMART on FHIR 规范，此变量对 SMART on FHIR 应用是不透明的，并传递给标识提供者。 

SMART on FHIR 代理使用此信息填充令牌响应中的字段。 SMART on FHIR *应用* 可以使用这些字段来控制请求数据的患者及其在屏幕上呈现应用程序时如何。 SMART on FHIR 代理支持以下字段：

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

这些字段旨在向应用提供指导，但它们不会传达任何安全信息。 SMART on FHIR 应用程序可以忽略它们。

请注意，SMART on FHIR 应用启动器更新页面底部的"启动 **URL"** 信息。 选择 **"** 启动"以启动示例应用，应会看到类似此示例：

![SMART on FHIR 应用](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

检查令牌响应，查看启动上下文字段如何传递到应用。

## <a name="next-steps"></a>后续步骤

在本教程中，你已配置 Azure Active Directory SMART on FHIR 代理。 若要探索 SMART on FHIR 应用程序与 Azure API for FHIR 和适用于 Azure 的开源 FHIR 服务器的使用，请转到以下位置的 FHIR 服务器示例GitHub：

>[!div class="nextstepaction"]
>[FHIR 服务器示例](https://github.com/Microsoft/fhir-server-samples)
