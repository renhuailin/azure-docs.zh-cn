---
title: Azure Active Directory FHIR 代理上的智能
description: 本教程介绍如何使用代理通过用于 FHIR 的 Azure API 在 FHIR 应用程序上启用 SMART。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017963"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>教程：在 FHIR 代理上 Azure Active Directory SMART

[SMART ON FHIR](https://docs.smarthealthit.org/) 是一套开放式规范，用于将合作伙伴应用程序与具有 FHIR 接口的 FHIR 服务器和电子医疗记录系统集成。 规范的主要目的之一是说明应用程序应该如何发现 FHIR 服务器的身份验证终结点并启动身份验证序列。 

身份验证基于 OAuth2。 但由于 SMART on FHIR 使用的参数命名约定与 Azure Active Directory (Azure AD) 不一致，因此 FHIR 的 Azure API 具有 FHIR proxy 的内置 Azure AD 智能，可启用智能 on FHIR 启动序列的子集。 具体而言，代理会启用 [EHR 启动序列](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)。

本教程介绍如何使用代理通过用于 FHIR 的 Azure API 在 FHIR 应用程序上启用 SMART。

## <a name="prerequisites"></a>先决条件

- 用于 FHIR 的 Azure API 的实例
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>配置 Azure AD 注册

FHIR 上的 SMART 要求 `Audience` 具有等于 FHIR 服务的 uri 的标识符 uri。 适用于 FHIR 的 Azure API 的标准配置使用 `Audience` 的值 `https://azurehealthcareapis.com` 。 不过，还可以设置与 FHIR 服务的特定 URL 匹配的值， (例如 `https://MYFHIRAPI.azurehealthcareapis.com`) 。 此操作是在 FHIR 代理上使用智能时必需的。

你还需要一个客户端应用程序注册。 大多数智能的 FHIR 应用程序都是单页 JavaScript 应用程序。 因此，你应按照配置 [公用 Azure AD 客户端应用程序](register-public-azure-ad-client-app.md)的说明进行操作。

完成这些步骤后，您应该：

- Rge 受众设置为的 FHIR 服务器 `https://MYFHIRAPI.azurehealthcareapis.com` ，其中 `MYFHIRAPI` 是用于 FHIR 实例的 Azure API 的名称。
- 公共客户端应用程序注册。 记下该客户端应用程序的应用程序 ID。

## <a name="enable-the-smart-on-fhir-proxy"></a>在 FHIR 代理上启用 SMART

通过选中 "**智能启用 FHIR 代理**" 复选框，在 Azure API for FHIR 实例的 **身份验证** 设置中启用 smart on FHIR 代理：

![用于在 FHIR 代理上启用智能的选择](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>启用 CORS

由于最聪明的 FHIR 应用程序是单页 JavaScript 应用，因此需要为用于 FHIR 的 Azure API [ (CORS) 启用跨域资源共享 ](configure-cross-origin-resource-sharing.md) ：

![用于启用 CORS 的选项](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>配置回复 URL

SMART on FHIR proxy 充当 SMART on FHIR 应用与 Azure AD 之间的媒介。 身份验证代码 (身份验证回复) 必须转到智能 FHIR 代理，而不是应用本身。 然后，代理将答复转发到应用。 

由于这是身份验证代码的两步中继，因此需要将 Azure AD 客户端应用程序的 "回复 URL" (回叫) 设置为一个 URL，该 URL 是智能 FHIR 代理的回复 URL 和 SMART on FHIR 应用程序的答复 URL 的组合。 组合回复 URL 采用以下格式：

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

在此回复中， `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` 是 FHIR 应用的 url 安全、base64 编码版本的回复 url。 在 FHIR 应用程序启动器上，当应用在本地运行时，回复 URL 是 `https://localhost:5001/sampleapp/index.html` 。 

可以通过使用如下所示的脚本生成组合的答复 URL：

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

将回复 URL 添加到之前为 Azure AD 创建的公用客户端应用程序：

![为公用客户端配置的回复 URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>获取测试患者

若要测试适用于 FHIR 的 Azure API 和 SMART on FHIR 代理，数据库中至少需要有一个患者。 如果尚未与 API 交互并且数据库中没有数据，请按照 [FHIR API Postman 教程](access-fhir-postman-tutorial.md) 加载患者。 记下特定患者的 ID。

## <a name="download-the-smart-on-fhir-app-launcher"></a>在 FHIR 应用启动器上下载 SMART

[适用于 Azure 存储库的开源 FHIR 服务器](https://github.com/Microsoft/fhir-server)包含一个简单的智能 on FHIR 应用启动器，以及一个 FHIR 应用的示例。 在本教程中，请在本地使用 FHIR 启动器来测试安装。

可以使用以下命令克隆 GitHub 存储库并使用应用程序：

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

应用程序需要几个配置设置，你可以在其中设置这些设置 `appsettings.json` ：

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

建议使用 `dotnet user-secrets` 以下功能：

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

使用此命令来运行应用程序：

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>在 FHIR 代理上测试智能

在 FHIR 应用启动器上启动 SMART 后，可以将浏览器指向 `https://localhost:5001` ，其中显示了以下屏幕：

![FHIR 应用启动器上的 SMART](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

当你输入 **患者**、 **遇到** 或 **实践者** 信息时，你会注意到 **启动上下文** 已更新。 使用用于 FHIR 的 Azure API 时，启动上下文只是包含患者、实践者和其他内容的 JSON 文档。 此启动上下文是 base64 编码的，并作为查询参数传递到 SMART on FHIR 应用 `launch` 。 根据 SMART on FHIR 规范，此变量对 SMART on FHIR 应用不透明，并将其传递给标识提供者。 

SMART on FHIR proxy 使用此信息来填充令牌响应中的字段。 SMART on FHIR 应用 *可以* 使用这些字段来控制它为其请求数据的患者以及它如何在屏幕上呈现应用程序。 SMART on FHIR proxy 支持以下字段：

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

这些字段旨在向应用提供指导，但不会传达任何安全信息。 智能 FHIR 应用程序可以忽略它们。

请注意，SMART on FHIR 应用启动器会更新页面底部的 **启动 URL** 信息。 选择 " **启动** " 以启动示例应用程序，应会看到类似于以下示例的内容：

![FHIR 应用上的智能](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

检查令牌响应，查看启动上下文字段如何传递到应用。

## <a name="next-steps"></a>后续步骤

在本教程中，已在 FHIR 代理上配置 Azure Active Directory 智能。 若要浏览 Azure API for FHIR 和 Azure 开源 FHIR 服务器的使用 FHIR 应用程序的使用情况，请参阅 GitHub 上的 FHIR Server 示例存储库：

>[!div class="nextstepaction"]
>[FHIR 服务器示例](https://github.com/Microsoft/fhir-server-samples)
