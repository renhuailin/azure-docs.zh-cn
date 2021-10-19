---
title: Azure Active Directory SMART on FHIR 代理
description: 本教程介绍如何使用 Azure API for FHIR 通过一个代理来启用 SMART on FHIR 应用程序。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: zxue
ms.date: 09/10/2019
ms.openlocfilehash: d5f690b3941e09f7e2a4e999aa59a97872aa3019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742791"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>教程：Azure Active Directory SMART on FHIR 代理

[SMART on FHIR](https://docs.smarthealthit.org/) 是一套开放式规范，用于将合作伙伴应用程序与 FHIR 服务器和具有 FHIR 接口的电子医疗记录系统集成。 这些规范的主要目的之一是说明应用程序应如何发现 FHIR 服务器的身份验证终结点并启动身份验证序列。 

身份验证基于 OAuth2。 但是，由于 SMART on FHIR 使用的参数命名约定不直接与 Azure Active Directory (Azure AD) 兼容，因此 Azure API for FHIR 提供了一个内置的 Azure AD SMART on FHIR 代理来启用一部分 SMART on FHIR 启动序列。 具体而言，该代理启用 [EHR 启动序列](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)。

本教程将介绍如何使用 Azure API for FHIR 通过该代理来启用 SMART on FHIR 应用程序。

## <a name="prerequisites"></a>先决条件

- Azure API for FHIR 的实例
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>配置 Azure AD 注册

SMART on FHIR 要求 `Audience` 的标识符 URI 与 FHIR 服务的 URI 相同。 Azure API for FHIR 的标准配置使用 `https://azurehealthcareapis.com` 的 `Audience` 值。 但是，你也可以设置与 FHIR 服务的特定 URL 匹配的值（例如 `https://MYFHIRAPI.azurehealthcareapis.com`）。 使用 SMART on FHIR 代理时必须这样做。

此外，还需要客户端应用程序注册。 大多数 SMART on FHIR 应用程序是单页 JavaScript 应用程序。 因此，应遵循在 中配置公共[客户端应用程序的Azure AD。](register-public-azure-ad-client-app.md)

完成这些步骤后，应会获得：

- 受众设置为 的 FHIR 服务器，其中 是实例 `https://MYFHIRAPI.azurehealthcareapis.com` `MYFHIRAPI` Azure API for FHIR的名称。
- 公共客户端应用程序注册。 请记下此客户端应用程序的应用程序 ID。

### <a name="set-admin-consent-for-your-app"></a>为应用设置管理员同意

若要使用 SMART on FHIR，必须先对应用进行身份验证和授权。 首次使用 SMART on FHIR 时，还必须获得管理许可，使应用能够访问 FHIR 资源。

如果在应用中没有所有权角色，请联系应用所有者，并要求他们授予你在应用中的管理员同意。 

如果具有管理权限，请完成以下步骤以直接向自己授予管理员许可。  (稍后在 app.) 中系统提示时，也可以向自己授予管理员同意。) 可以完成相同的步骤将其他用户添加为所有者，以便他们可以查看和编辑此应用注册。

将自己或其他用户添加为应用的所有者：

1. 在 Azure 门户中，转到“Azure Active Directory”。
1. 在左侧菜单中，选择"应用 **注册"。**
1. 搜索创建的应用注册，然后选择它。
1. 在左侧菜单的"管理 **"下，** 选择"**所有者"。**
1. 选择 **"添加所有者**"，然后添加自己或想要获得管理员同意的用户。
1. 选择“保存”。

## <a name="enable-the-smart-on-fhir-proxy"></a>启用 SMART on FHIR 代理

在 Azure API for FHIR 实例的“身份验证”设置中，通过选中“SMART on FHIR 代理”复选框启用 SMART on FHIR 代理： 

![用于启用 SMART on FHIR 代理的选项](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>启用 CORS

由于大多数 SMART on FHIR 应用程序是单页 JavaScript 应用，因此需要为 Azure API for FHIR [启用跨源资源共享 (CORS)](configure-cross-origin-resource-sharing.md)：

![用于启用 CORS 的选项](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>配置回复 URL

SMART on FHIR 代理充当 SMART on FHIR 应用与 Azure AD 之间的中介。 身份验证回复（身份验证代码）必须进入 SMART on FHIR 代理，而不是进入应用本身。 然后，代理会将回复转发到应用。 

由于身份验证代码的这种双步中继方式，你需要将 Azure AD 客户端应用程序的回复 URL（回调）设置为由 SMART on FHIR 代理的回复 URL 与 SMART on FHIR 应用的回复 URL 组合而成的 URL。 组合的回复 URL 采用以下形式：

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

在该回复中，`aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` 是 SMART on FHIR 应用回复 URL 的 base64 编码的 URL 安全式版本。 对于 SMART on FHIR 应用启动器，当应用在本地运行时，回复 URL 将是 `https://localhost:5001/sampleapp/index.html`。 

可以使用如下所示的脚本生成组合的回复 URL：

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

将回复 URL 添加到前面为 Azure AD 创建的公共客户端应用程序：

![为公共客户端配置的回复 URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>获取测试患者

若要测试 Azure API for FHIR 和 SMART on FHIR 代理，数据库中至少需要有一个患者。 如果尚未与 API 交互，并且数据库中没有数据，请参阅使用 Postman 访问 [FHIR](./../use-postman.md) 服务以加载患者。 请记下具体患者的 ID。

## <a name="download-the-smart-on-fhir-app-launcher"></a>下载 SMART on FHIR 应用启动器

开源的[适用于 Azure 的 FHIR 服务器存储库](https://github.com/Microsoft/fhir-server)包含一个简单的 SMART on FHIR 应用启动器和一个示例 SMART on FHIR 应用。 本教程将在本地使用 SMART on FHIR 启动器来测试设置。

可使用以下命令克隆 GitHub 存储库并转到应用程序：

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

该应用程序需要一些配置设置，可以在 `appsettings.json` 中进行设置：

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

我们建议使用 `dotnet user-secrets` 功能：

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

使用以下命令运行应用程序：

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>测试 SMART on FHIR 代理

启动 SMART on FHIR 应用启动器后，可将浏览器指向 `https://localhost:5001`，在其中应会看到以下屏幕：

![SMART on FHIR 应用启动器](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

输入“患者”、“就诊”或“医师”信息时，会发现“启动上下文”已更新。    使用 Azure API for FHIR 时，启动上下文只是一个 JSON 文档，其中包含有关患者、医师等等的信息。 此启动上下文采用 base64 编码，作为 `launch` 查询参数传递给 SMART on FHIR 应用。 根据 SMART on FHIR 规范，此变量对于 SMART on FHIR 应用是不透明的，并且将传递给标识提供者。 

SMART on FHIR 代理使用此信息来填充令牌响应中的字段。 SMART on FHIR 应用可以使用这些字段来控制它要请求哪个患者的数据，以及如何在屏幕上呈现应用程序。 SMART on FHIR 代理支持以下字段：

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

这些字段旨在向应用提供指导，而不会传达任何安全信息。 SMART on FHIR 应用程序可以忽略这些字段。

请注意，SMART on FHIR 应用启动器会在页面底部更新“启动 URL”信息。 选择“启动”可启动示例应用，此时应会看到类似于以下示例的内容：

![SMART on FHIR 应用](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

检查令牌响应，以查看启动上下文字段如何传递给应用。

## <a name="next-steps"></a>后续步骤

在本教程中，你已配置了 Azure Active Directory SMART on FHIR 代理。 若要探索如何将 SMART on FHIR 应用程序与 Azure API for FHIR 和开源的适用于 Azure 的 FHIR 服务器配合使用，请转到 GitHub 上的 FHIR 服务器示例存储库：

>[!div class="nextstepaction"]
>[FHIR 服务器示例](https://github.com/Microsoft/fhir-server-samples)
