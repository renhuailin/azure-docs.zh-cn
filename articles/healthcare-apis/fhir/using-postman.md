---
title: 使用 Postman 访问 Azure 医疗保健 Api FHIR 服务
description: 本文介绍如何使用 Postman 访问 Azure 医疗保健 Api FHIR 服务。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: 908e9a9c17f93725b3cfb7a464224539379fddd1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777592"
---
# <a name="access-the-fhir-service-using-postman"></a>使用 Postman 访问 FHIR 服务

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本文中，我们将逐步介绍如何使用 [Postman](https://www.getpostman.com/) (特此称为 FHIR service) 访问医疗保健 api FHIR service。

## <a name="prerequisites"></a>先决条件

* 部署在 Azure 中的 FHIR 服务。 有关如何部署 FHIR 服务的信息，请参阅 [部署 FHIR 服务](fhir-portal-quickstart.md)。
* 用于访问 FHIR 服务的已注册客户端应用程序。 有关如何注册客户端应用程序的信息，请参阅[在 Azure Active Directory 中注册服务客户端应用程序](../register-application.md)。 
* 授予客户端应用程序和你的用户帐户的权限，例如 "FHIR 数据参与者"，以访问 FHIR 服务。 有关详细信息，请参阅 [在 Azure 医疗保健 api 中部署 FHIR 服务](fhir-portal-quickstart.md)。
* Postman 安装在本地。 有关 Postman 的详细信息，请参阅[与 Postman 入门](https://www.getpostman.com/)。

## <a name="using-postman-create-workspace-collection-and-environment"></a>使用 Postman：创建工作区、集合和环境

如果不熟悉 Postman，请执行下面的步骤。 否则可以跳过此步骤。
 
Postman 引入了工作区概念，使你和你的团队能够共享 Api、集合、环境和其他组件。 你可以使用默认的 "我的工作区" 或 "团队工作区"，或为你或你的团队创建新的工作区。
 
[![在 Postman 中创建新的工作区。 ](media/postman/postman-create-new-workspace.png) ](media/postman/postman-create-new-workspace.png#lightbox)

接下来，创建一个可对所有相关 REST API 请求进行分组的新集合。 在工作区中，选择 " **创建集合**"。 您可以保留默认名称 " **新建集合** " 或 "重命名"。 更改将自动保存。

[![创建新集合。 ](media/postman/postman-create-a-new-collection.png) ](media/postman/postman-create-a-new-collection.png#lightbox)

还可以导入和导出 Postman 集合。 有关详细信息，请参阅 [Postman 文档](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/)。

[![导入数据。 ](media/postman/postman-import-data.png) ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>创建或更新环境变量

虽然你可以在请求中使用完整 url，但建议你将 url 和其他数据存储在变量中并使用它们。

若要访问 FHIR 服务，需要创建或更新以下变量。

* **tenantid** –部署 FHIR 服务的 Azure 租户。 它位于 " **应用程序注册概述** " 菜单选项中。
* **subid** –其中部署了 FHIR 服务的 Azure 订阅。 它位于 **FHIR 服务概述** 菜单选项中。
* **clientid** –应用程序客户端注册 ID。
* **clientsecret** –应用程序客户端注册机密。
* **fhirurl** – FHIR 服务完整 URL。 例如，`https://xxx.azurehealthcareapis.com`。 它位于 **FHIR 服务概述** 菜单选项中。
* **bearerToken** –用于存储 Azure Active Directory 的变量 (在脚本中 Azure AD) 的访问令牌。 将此字段留空。

> [!NOTE]
> 确保已 [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) 在客户端应用程序注册中配置重定向 URL。

[![环境变量。 ](media/postman/postman-environments-variable.png) ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>连接到 FHIR 服务器

打开 Postman，选择要使用的 **工作区**、 **集合** 和 **环境** 。 选择 `+` 图标以创建新的请求。 

[![创建新的请求。 ](media/postman/postman-create-new-request.png) ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>Get 功能语句

`{{fhirurl}}/metadata`在请求中输入 `GET` ，然后按 `Send` 。 应会看到 FHIR 服务的功能声明。

[![功能语句参数。 ](media/postman/postman-capability-statement.png) ](media/postman/postman-capability-statement.png#lightbox)

[![保存请求。 ](media/postman/postman-save-request.png) ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>获取 Azure AD 访问令牌

Azure AD 保护 FHIR 服务。 不能禁用默认身份验证。 若要访问 FHIR 服务，必须首先获取 Azure AD 访问令牌。 有关详细信息，请参阅[Microsoft 标识平台访问令牌](../../active-directory/develop/access-tokens.md)。

创建新 `POST` 请求：

1. 在请求标头中输入： `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. 选择 " **正文** " 选项卡，然后选择 " **url 编码**"。 在 "密钥和值" 部分输入以下值：
    - **grant_type**： `Client_Credentials`
    - **client_id**： `{{clientid}}`
    - **client_secret**： `{{clientsecret}}`
    - **资源**： `{{fhirurl}}`
    
3. 选择 " **测试** " 选项卡，然后在文本部分中输入： `pm.environment.set("bearerToken", pm.response.json().access_token);`
4. 选择“保存”，保存这些设置。
5. 按 **Send**。 应会看到带有 Azure AD 访问令牌的响应，该令牌将自动保存到变量中 `accessToken` 。 然后，可以在所有 FHIR 服务 API 请求中使用它。

  [" ![ 发送" 按钮。 ](media/postman/postman-send-button.png) ](media/postman/postman-send-button.png#lightbox)

可以使用联机工具（例如）检查访问令牌 [https://jwt.ms](https://jwt.ms) 。 选择 " **声明** " 选项卡以查看令牌中每个声明的详细说明。

[![访问令牌声明。 ](media/postman/postman-access-token-claims.png) ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>获取 FHIR 资源

获取 Azure AD 访问令牌后，可以访问 FHIR 数据。 在新 `GET` 请求中，输入 `{{fhirurl}}/Patient` 。

选择 " **持有者令牌** " 作为 "授权类型"。  `{{bearerToken}}`在 "**令牌**" 部分输入。 选择 **Send**。 作为响应，你应该会在 FHIR 资源中看到患者列表。

[![选择 "持有者令牌"。 ](media/postman/postman-select-bearer-token.png) ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>创建或更新 FHIR 资源

获取 Azure AD 访问令牌后，可以创建或更新 FHIR 数据。 例如，可以创建新的患者，或更新现有患者。
 
创建新请求，将方法更改为 "Post"，然后在 "请求" 部分中输入值。

`{{fhirurl}}/Patient`

选择 " **持有者令牌** " 作为 "授权类型"。  `{{bearerToken}}`在 "**令牌**" 部分输入。 选择 " **正文** " 选项卡。选择 **原始** 选项和 **JSON** 作为正文文本格式。 将文本复制并粘贴到正文部分。 


```
{
    "resourceType": "Patient",
    "active": true,
    "name": [
        {
            "use": "official",
            "family": "Kirk",
            "given": [
                "James",
                "Tiberious"
            ]
        },
        {
            "use": "usual",
            "given": [
                "Jim"
            ]
        }
    ],
    "gender": "male",
    "birthDate": "1960-12-25"
}
```
选择 **Send**。 你应会在 JSON 响应中看到一个新的患者。

[" ![ 发送" 按钮以创建新的患者。 ](media/postman/postman-send-create-new-patient.png) ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>导出 FHIR 数据

获取 Azure AD 访问令牌后，可以将 FHIR 数据导出到 Azure 存储帐户。

创建新 `GET` 请求： `{{fhirurl}}/$export?_container=export`

选择 " **持有者令牌** " 作为 "授权类型"。  `{{bearerToken}}`在 "**令牌**" 部分输入。 选择 **"标头"** 以添加两个新标头：

- **接受**： `application/fhir+json`
- **首选**：  `respond-async`

点击 **"发送"。** 应注意到响应 `202 Accepted` 。 选择 **响应的"** 标头"选项卡，并记下 **Content-Location 中的值**。 可以使用 值查询导出作业状态。

[![发布以创建新的患者 202 接受的响应。 ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Postman 访问 Azure 医疗保健 API 中的 FHIR 服务。 有关 Azure 医疗保健 API 中的 FHIR 服务的信息，请参阅

>[!div class="nextstepaction"]
>[什么是 FHIR 服务？](overview.md)