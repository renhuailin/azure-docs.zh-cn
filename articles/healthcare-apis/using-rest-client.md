---
title: 使用 REST 客户端访问 Azure 医疗保健 API
description: 本文介绍如何使用 VSCode 中的 REST 客户端扩展访问医疗保健 API
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 615aaed8be507ed19314e7f7329d7ca2f15a5f0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042345"
---
# <a name="accessing-the-healthcare-apis-preview-using-the-rest-client-extension-in-visual-studio-code"></a>使用中 REST 客户端扩展 (医疗保健 API) 预览版Visual Studio Code

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍在 中如何使用 REST 客户端扩展[访问医疗保健Visual Studio Code。](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

## <a name="install-rest-client-extension"></a>安装 REST 客户端扩展

选择应用程序左侧面板上的"扩展"图标Visual Studio Code搜索"REST 客户端"。 找到 [REST 客户端扩展并](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) 安装。

[![REST 客户端 VSCode 扩展 ](media/rest-install.png) ](media/rest-install.png#lightbox)

## <a name="create-a-http-file-and-define-variables"></a>创建 `.http` 文件并定义变量

在 Visual Studio Code 中创建新文件。 在 `GET` 文件中输入请求命令行，并将其另存为 `test.http` 。 文件后缀 `.http` 会自动激活 REST 客户端环境。 单击 `Send Request` 获取元数据。 

[![发送请求 ](media/rest-send-request.png) ](media/rest-send-request.png#lightbox)

## <a name="get-client-application-values"></a>获取客户端应用程序值

> [!Important]
> 在调用 FHIR 服务器REST API (获取元数据之前，) 应用程序 **[注册](register-application.md)**。 记下 Azure 租户 **ID、****客户端 ID、****客户端机密** 和服务 **URL。**

虽然可以直接在调用 REST API 中使用客户端 ID 等值，但建议为这些值定义几个变量，并改为使用变量。

在 `test.http` 文件中，包括从注册应用程序获取的以下信息： 

```
### REST Client
@fhirurl =https://xxx.azurehealthcareapis.com
@clientid =xxx....
@clientsecret =xxx....
@tenantid =xxx....
```

## <a name="get-azure-ad-access-token"></a>获取Azure AD访问令牌

在文件包含以下信息后 `test.http` ，点击 `Send Request` 。 你将看到包含访问令牌的 HTTP 响应。

以 开始 `@name` 行包含一个变量，该变量捕获包含访问令牌的 HTTP 响应。 变量 `@token` 用于存储访问令牌。

>[!Note] 
>`grant_type`的 `client_credentials` 用于获取访问令牌。

```
### Get access token 
@name getAADToken 
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&resource={{fhirurl}}
&client_id={{clientid}}
&client_secret={{clientsecret}}

### Extract access token from getAADToken request
@token = {{getAADToken.response.body.access_token}}
```

[![获取访问令牌 ](media/rest-config.png) ](media/rest-config.png#lightbox)

## <a name="get-fhir-patient-data"></a>`GET` FHIR 患者数据

现在，可以使用请求获取患者或特定患者 `GET` 的列表。 包含 的 `Authorization` 行是请求的标头 `GET` 信息。 还可以发送 `PUT` 或 `POST` 请求创建/更新 FHIR 资源。

```
### GET Patient 
GET {{fhirurl}}/Patient/<patientid>
Authorization: Bearer {{token}}
```

[![GET Patient ](media/rest-patient.png) ](media/rest-patient.png#lightbox)

## <a name="run-powershell-or-cli"></a>运行 PowerShell 或 CLI

可以在 Visual Studio Code 中运行 PowerShell 或 CLI 脚本。 按 `CTRL` 和 键 `~` ，然后选择"PowerShell"或"Bash"。 可以在集成终端 上 [找到更多详细信息](https://code.visualstudio.com/docs/editor/integrated-terminal)。

### <a name="powershell-in-visual-studio-code"></a>PowerShell Visual Studio Code
[![运行 PowerShell ](media/rest-powershell.png) ](media/rest-powershell.png#lightbox)

### <a name="cli-in-visual-studio-code"></a>CLI Visual Studio Code
[![运行 CLI ](media/rest-cli.png) ](media/rest-cli.png#lightbox)

## <a name="troubleshooting"></a>疑难解答

如果无法获取元数据，而该元数据不需要基于 HL7 规范的访问令牌，请检查 FHIR 服务器是否正常运行。

如果无法获取访问令牌，请确保客户端应用程序已正确注册，并且使用的是应用程序注册步骤中的正确值。

如果无法从 FHIR 服务器获取数据，请确保客户端应用程序 (或服务主体) 已被授予对 FHIR 服务器的"FHIR 数据参与者"等访问权限。
