---
title: 在 Azure IoT Central 中授权 REST API
description: 如何对 IoT Central REST API 调用进行身份验证和授权
author: dominicbetts
ms.author: dobett
ms.date: 08/25/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 39c96be31a8a8be0cf1c2fc4f16fcbfa7468cb41
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584230"
---
# <a name="how-to-authenticate-and-authorize-iot-central-rest-api-calls"></a>如何对 IoT Central REST API 调用进行身份验证和授权

通过 IoT Central REST API 可以开发与 IoT Central 集成的客户端应用程序。 使用 REST API 可以处理 IoT Central 应用程序中的资源，例如设备模板、设备、作业、用户和角色。

每个 IoT Central REST API 调用都需要一个授权头，IoT Central 使用该授权头来确定调用方的标识，以及在应用程序中为该调用方授予的权限。

本文介绍可在授权头中使用的令牌类型，以及如何获取这些令牌。

## <a name="token-types"></a>令牌类型

在自己进行一些自动化/测试/API 调用时，将需要使用用户持有者令牌；在自动化/编写开发环境（即 devops）脚本时，需要要使用 SPN 持有者令牌。 API 令牌可用于这两种情况，但具有到期和泄漏的风险，因此，我们建议尽可能使用持有者。 这是否有用？ 

若要通过 REST API 访问 IoT Central 应用程序，可以使用：

- Azure Active Directory 持有者令牌。 持有者令牌与 Azure Active Directory 用户帐户或服务主体相关联。 该令牌为调用方授予用户或服务主体在 IoT Central 应用程序中所拥有的相同权限。
- IoT Central API 令牌。 API 令牌与 IoT Central 应用程序中的角色相关联。

若要详细了解 IoT Central 中的用户和角色，请参阅[在 IoT Central 应用程序中管理用户和角色](howto-manage-users-roles.md)。

## <a name="get-a-bearer-token"></a>获取持有者令牌

若要获取 Azure Active Directory 用户帐户的持有者令牌，请使用以下 Azure CLI 命令：

```azurecli
az login
az account get-access-token --resource https://apps.azureiotcentral.com
```

> [!IMPORTANT]
> 即使使用的是 Cloud Shell，也需要运行 `az login` 命令。

上述命令的 JSON 输出如以下示例所示：

```json
{
  "accessToken": "eyJ0eX...fNQ",
  "expiresOn": "2021-03-22 11:11:16.072222",
  "subscription": "{your subscription id}",
  "tenant": "{your tenant id}",
  "tokenType": "Bearer"
}
```

持有者令牌的有效期大约为一小时，此期限过后需要创建新令牌。

若要获取服务主体的持有者令牌，请参阅[服务主体身份验证](/rest/api/iotcentral/authentication#service-principal-authentication)。

## <a name="get-an-api-token"></a>获取 API 令牌

若要获取 API 令牌，可以使用 IoT Central UI 或 REST API 调用。 与根组织关联的管理员以及被分配了正确角色的用户可以创建 API 令牌。

在 IoT Central UI 中：

1. 导航到“管理”>“API 令牌”。
1. 选择“+ 创建令牌”。
1. 输入令牌的名称并选择一个角色和[组织](howto-create-organizations.md)。
1. 然后选择“生成”  。
1. IoT Central 将显示如以下示例所示的令牌：

    `SharedAccessSignature sr=5782ed70...&sig=dvZZE...&skn=operator-token&se=1647948035850`

    API 令牌只会在此屏幕中显示一次，如果丢失此令牌，则需要生成新令牌。

API 令牌的有效期大约为一年。 可以在 IoT Central 应用程序中为内置角色和自定义角色生成令牌。 在创建 API 令牌时选择的组织决定了 API 可以访问哪些设备。 在将任何组织添加到应用程序之前创建的任何 API 令牌都与根组织相关联。

如果需要撤销访问权限，可以在 IoT Central UI 中删除 API 令牌。

使用 REST API：

1. 使用 REST API 从应用程序中检索角色 ID 列表：

    ```http
    GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
    ```

    对此请求的响应如以下示例所示：

    ```json
    {
      "value": [
        {
          "displayName": "Administrator",
          "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        },
        {
          "displayName": "Operator",
          "id": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        },
        {
          "displayName": "Builder",
          "id": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ]
    }
    ```

1. 使用 REST API 为角色创建 API 令牌。 例如，若要为操作员角色创建名 `operator-token` 为的 API 令牌，请运行以下命令：

    ```http
    PUT https://{your app subdomain}.azureiotcentral.com/api/roles/operator-token?api-version=1.0
    ```

    请求正文：

    ```json
    {
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ]
    }
    ```

    对上述命令的响应如以下 JSON 所示：

    ```json
    {
      "expiry": "2022-03-22T12:01:27.889Z",
      "id": "operator-token",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "token": "SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889"
    }
    ```

    只能通过此响应访问此 API 令牌一次，如果丢失此令牌，则需要生成新令牌。

可以使用 REST API 列出和删除应用程序中的 API 令牌。

> [!TIP]
> [预览 API](/rest/api/iotcentral/1.1-previewdataplane/api-tokens) 支持新的[组织功能](howto-create-organizations.md)。

## <a name="use-a-bearer-token"></a>使用持有者令牌

若要在发出 REST API 调用时使用持有者令牌，授权头应如以下示例所示：

`Authorization: Bearer eyJ0eX...fNQ`

## <a name="use-an-api-token"></a>使用 API 令牌

若要在发出 REST API 调用时使用 API 令牌，授权头应如以下示例所示：

`Authorization: SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889`

## <a name="next-steps"></a>后续步骤

现在你已了解如何授权 REST API 调用，建议接下来了解[如何使用 IoT Central REST API 管理用户和角色](howto-manage-users-roles-with-rest-api.md)。
