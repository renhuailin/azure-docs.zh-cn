---
title: 使用 Postman 访问 Azure 医疗保健 API FHIR 服务
description: 本文介绍如何使用 Postman 访问 Azure 医疗保健 API FHIR 服务。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: 775c8a4366a381d90f8e24772097249b8cba844a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187407"
---
# <a name="access-using-postman"></a>使用 Postman 访问

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍访问医疗保健 API FHIR 服务的步骤， ([Postman](https://www.getpostman.com/)的 FHIR) 服务。

## <a name="prerequisites"></a>先决条件

* Azure 中部署的 FHIR 服务。 若要了解如何部署 FHIR 服务，请参阅部署 [FHIR 服务](fhir/fhir-portal-quickstart.md)。
* 一个已注册的客户端应用程序，该应用程序访问 FHIR 服务。 若要了解如何注册客户端应用程序，请参阅在 中注册[服务客户端Azure Active Directory。](register-application.md) 
* 授予客户端应用程序和用户帐户（例如"FHIR 数据参与者"）访问 FHIR 服务的权限。 
* Postman 安装在本地。 有关 Postman 的信息，请参阅使用[Postman 入门。](https://www.getpostman.com/)

## <a name="using-postman-create-workspace-collection-and-environment"></a>使用 Postman：创建工作区、集合和环境

如果对 Postman 很新，请执行以下步骤。 否则可以跳过此步骤。
 
Postman 引入了工作区概念，使你和团队能够共享 API、集合、环境和其他组件。 可以使用默认的"我的工作区"或"团队工作区"，或者为你或团队创建新工作区。
 
[![在 Postman 中创建新工作区的屏幕截图。 ](media/postman/postman-create-new-workspace.png) ](media/postman/postman-create-new-workspace.png#lightbox)

接下来，创建一个新集合，可在其中对相关的请求REST API分组。 在工作区中，选择"**创建集合"。** 可以保留默认名称"新建 **集合"或** 对其进行重命名。 更改将自动保存。

[![创建新集合的屏幕截图。 ](media/postman/postman-create-a-new-collection.png) ](media/postman/postman-create-a-new-collection.png#lightbox)

还可以导入和导出 Postman 集合。 有关详细信息，请参阅 [Postman 文档](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/)。

[![导入数据的屏幕截图。 ](media/postman/postman-import-data.png) ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>创建或更新环境变量

虽然可以在请求中使用完整 URL，但建议将 URL 和其他数据存储在变量中并使用它们。

若要访问 FHIR 服务，需要创建或更新以下变量。

* **tenantid** – 部署 FHIR 服务的 Azure 租户。 它位于"应用程序注册概述 **"菜单** 选项中。
* **subid** - 部署 FHIR 服务的 Azure 订阅。 它位于 **FHIR** 服务概述菜单选项中。
* **clientid** – 应用程序客户端注册 ID。
* **clientsecret** – 应用程序客户端注册机密。
* **fhirurl** - FHIR 服务完整 URL。 例如，`https://xxx.azurehealthcareapis.com`。 它位于 **FHIR** 服务概述菜单选项中。
* **bearerToken** – 用于存储脚本Azure Active Directory (Azure AD) 访问令牌的变量。 将此字段留空。

> [!NOTE]
> 确保在客户端应用程序注册中配置重定向 URL `https://www.getpostman.com/oauth2/callback` 。

[![环境变量的屏幕截图。 ](media/postman/postman-environments-variable.png) ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>连接 FHIR 服务器

打开 Postman，**选择想要** 使用的 **工作区、集合** 和环境。 选择 `+` 图标以创建新请求。 

[![创建新请求的屏幕截图。 ](media/postman/postman-create-new-request.png) ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>获取功能语句

在 `{{fhirurl}}/metadata` 请求中 `GET` 输入 ，然后点击 `Send` 。 应会看到 FHIR 服务的 capability 语句。

[![功能语句参数的屏幕截图。 ](media/postman/postman-capability-statement.png) ](media/postman/postman-capability-statement.png#lightbox)

[![保存请求的屏幕截图。 ](media/postman/postman-save-request.png) ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>获取Azure AD令牌

FHIR 服务由 Azure AD。 无法禁用默认身份验证。 若要访问 FHIR 服务，必须先获取Azure AD令牌。 有关详细信息，请参阅Microsoft 标识平台[令牌](./../active-directory/develop/access-tokens.md)。

创建新 `POST` 请求：

1. 在请求标头中输入： `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. 选择"**正文"** 选项卡，然后选择 **"x-www-form-urlencoded"。** 在键和值部分中输入以下值：
    - **grant_type：**`Client_Credentials`
    - **client_id：**`{{clientid}}`
    - **client_secret：**`{{clientsecret}}`
    - **资源**： `{{fhirurl}}`
    
3. 选择" **测试"** 选项卡，在文本部分输入： `pm.environment.set("bearerToken", pm.response.json().access_token);`
4. 选择“保存”，保存这些设置。
5. 点击 **"发送"。** 应会看到包含Azure AD令牌的响应，该令牌会自动保存到变量 `bearerToken` 。 然后，可以在所有 FHIR 服务 API 请求中使用它。

  [![发送按钮的屏幕截图。 ](media/postman/postman-send-button.png) ](media/postman/postman-send-button.png#lightbox)

可以使用联机工具（如 ）检查访问令牌 [https://jwt.ms](https://jwt.ms) 。 选择" **声明"** 选项卡以查看令牌中每个声明的详细说明。

[![访问令牌声明的屏幕截图。 ](media/postman/postman-access-token-claims.png) ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>获取 FHIR 资源

获取访问令牌Azure AD，可以访问 FHIR 数据。 在新建 `GET` 请求中，输入 `{{fhirurl}}/Patient` 。

选择 **"Bearer Token"** 作为授权类型。  在 `{{bearerToken}}` "令牌 **"部分** 输入 。 选择 **Send**。 作为响应，应在 FHIR 资源中查看患者列表。

[![选择"bearer"令牌的屏幕截图。 ](media/postman/postman-select-bearer-token.png) ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>创建或更新 FHIR 资源

获取访问令牌Azure AD，可以创建或更新 FHIR 数据。 例如，你可以创建新患者或更新现有患者。
 
创建新请求，将 方法更改为"Post"，然后输入请求部分中的值。

`{{fhirurl}}/Patient`

选择 **"Bearer Token"** 作为授权类型。  在 `{{bearerToken}}` "令牌 **"部分** 输入 。 选择" **正文"** 选项卡。选择原始 **选项** ，并选择 **JSON** 作为正文文本格式。 将文本复制并粘贴到正文部分。 


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
选择 **Send**。 应在 JSON 响应中看到一个新患者。

[![用于创建新患者发送按钮的屏幕截图。 ](media/postman/postman-send-create-new-patient.png) ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>导出 FHIR 数据

获取访问令牌Azure AD，可以将 FHIR 数据导出到 Azure 存储帐户。

创建新 `GET` 请求： `{{fhirurl}}/$export?_container=export`

选择 **"Bearer Token"** 作为授权类型。  在 `{{bearerToken}}` "令牌 **"部分** 输入 。 选择 **"标头"** 以添加两个新标头：

- **接受**： `application/fhir+json`
- **首选**：  `respond-async`

按 **Send**。 你应注意到一个 `202 Accepted` 响应。 选择响应的 " **标头** " 选项卡，并记下 " **内容-位置**" 中的值。 您可以使用值查询导出作业状态。

[![用于创建新的患者202接受响应的 post 屏幕截图。 ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>后续步骤

本文介绍了如何通过 Postman 访问 Azure 医疗保健 Api 中的 FHIR 服务。 有关 Azure 医疗保健 Api 中的 FHIR 服务的信息，请参阅

>[!div class="nextstepaction"]
>[什么是 FHIR 服务？](overview.md)
