---
title: 使用 REST API 来管理 Azure IoT Central 中的用户和角色
description: 如何使用 IoT Central REST API 来管理应用程序中的用户和角色
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 97f97206b5071824f8bde8733d208078ddaf72bb
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473309"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-users-and-roles"></a>如何使用 IoT Central REST API 管理用户和角色

通过 IoT Central REST API 可以开发与 IoT Central 集成的客户端应用程序。 可以使用 REST API 来管理 IoT Central 应用程序中的用户和角色。

每个 IoT Central REST API 调用都需要授权标头。 有关详细信息，请参阅[如何对 IoT Central REST API 调用进行身份验证和授权](howto-authorize-rest-api.md)。

有关 IoT Central REST API 的参考文档，请参阅 [Azure IoT Central REST API 参考](/rest/api/iotcentral/)。

## <a name="manage-roles"></a>管理角色

REST API 允许列出在 IoT Central 应用程序中定义的角色。 使用以下请求从应用程序中检索角色 ID 列表：

```http
GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
```

对此请求的响应类似于以下示例，其中包含三个内置角色和一个自定义角色：

```json
{
  "value": [
    {
      "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4",
      "displayName": "Administrator"
    },
    {
      "id": "ae2c9854-393b-4f97-8c42-479d70ce626e",
      "displayName": "Operator"
    },
    {
      "id": "344138e9-8de4-4497-8c54-5237e96d6aaf",
      "displayName": "Builder"
    },
    {
      "id": "16f8533f-6b82-478f-8ba8-7e676b541b1b",
      "displayName": "Example custom role"
    }
  ]
}
```

## <a name="manage-users"></a>管理用户

REST API 具备以下功能：

- 列出应用程序中的用户
- 检索单个用户的详细信息
- 创建用户
- 修改用户
- 删除用户

### <a name="list-users"></a>列出用户

使用以下请求从应用程序中检索用户列表：

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users?api-version=1.0
```

对此请求的响应如以下示例所示。 角色值标识与用户关联的角色 ID：

```json
{
  "value": [
    {
      "id": "91907508-04fe-4349-91b5-b872f3055a95",
      "type": "email",
      "roles": [
        {
          "role": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        }
      ],
      "email": "user1@contoso.com"
    },
    {
      "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
      "type": "email",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "email": "user2@contoso.com"
    },
    {
      "id": "3ab9375e-d2d9-42da-b419-6ae86a938321",
      "type": "email",
      "roles": [
        {
          "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ],
      "email": "user3@contoso.com"
    },
    {
      "id": "fc5a250b-83fb-433d-892c-e0a144f68c2b",
      "type": "email",
      "roles": [
        {
          "role": "16f8533f-6b82-478f-8ba8-7e676b541b1b"
        }
      ],
      "email": "user4@contoso.com"
    }
  ]
}
```

### <a name="get-a-user"></a>获取用户

使用以下请求从应用程序中检索单个用户的详细信息：

```http
GET https://{your app subdomain}.azureiotcentral.com/api/users/dc1c916b-a652-49ea-b128-7c465a54c759?api-version=1.0
```

对此请求的响应如以下示例所示。 角色值标识与用户关联的角色 ID：

```json
{
  "id": "dc1c916b-a652-49ea-b128-7c465a54c759",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user2@contoso.com"
}
```

### <a name="create-a-user"></a>创建用户

使用以下请求在应用程序中创建用户。 ID 和电子邮件在应用程序中必须是唯一的：

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

在以下请求正文中，`role` 值表示之前检索到的操作员角色：

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

对此请求的响应如以下示例所示。 角色值标识与用户关联的角色：

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
    }
  ],
  "email": "user5@contoso.com"
}
```

如果需要对 REST API 调用使用服务主体身份验证，还可以添加服务主体用户。 有关详细信息，请参阅[添加或更新服务主体用户](/rest/api/iotcentral/1.0/users/create#add-or-update-a-service-principal-user)。

### <a name="change-the-role-of-a-user"></a>更改用户的角色

使用以下请求更改分配给用户的角色。 此示例使用之前检索到的构建者角色的 ID：

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

请求正文。 此值表示先前检索的构建者角色：

```json
{
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ]
}
```

对此请求的响应如以下示例所示：

```json
{
  "id": "user-001",
  "type": "email",
  "roles": [
    {
      "role": "344138e9-8de4-4497-8c54-5237e96d6aaf"
    }
  ],
  "email": "user5@contoso.com"
}
```

### <a name="delete-a-user"></a>删除用户

使用以下请求删除用户：

```http
DELETE https://{your app subdomain}.azureiotcentral.com/api/users/user-001?api-version=1.0
```

## <a name="next-steps"></a>后续步骤

现在你已了解如何使用 REST API 管理用户和角色，建议接下来了解[如何使用 IoT Central REST API 控制设备](howto-control-devices-with-rest-api.md)。
