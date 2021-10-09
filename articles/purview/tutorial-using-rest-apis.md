---
title: 如何使用 Purview 数据平面的 REST API
description: 本教程介绍如何使用 Azure Purview REST API 访问 Purview 的内容。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/17/2021
ms.openlocfilehash: 5074e0017a5811b9418771aeef3b0883e10891c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212810"
---
# <a name="tutorial-use-the-rest-apis"></a>教程：使用 REST API

本教程介绍如何使用 Azure Purview REST API。 任何人，如果想要将数据提交到 Azure Purview、将 Purview 作为自动化过程的一部分包括在内，或者在 Purview 上构建自己的用户体验，都可以使用 REST API 来实现。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="prerequisites"></a>先决条件

* 若要开始体验，必须具有现有的 Azure Purview 帐户。 如果你没有目录，请参阅[创建 Azure Purview 帐户的快速入门](create-catalog-portal.md)。

## <a name="create-a-service-principal-application"></a>创建服务主体（应用程序）

要让 REST API 客户端访问目录，客户端必须具有服务主体（应用程序）和目录可识别并配置为信任的标识。 对目录进行 REST API 调用时，它们会使用服务主体的标识。

使用现有服务主体（应用程序 ID）的客户失败率很高。 因此，建议创建新的服务主体来调用 API。

创建新的服务主体：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 从门户中搜索并选择“Azure Active Directory”。
1. 在“Azure Active Directory”页面，从左窗格中选择“应用注册” 。
1. 选择“新注册”。
1. 在“注册应用程序”页面上：
    1. 为应用程序输入名称（服务主体名称）。
    1. 选择“仅此组织目录中的帐户(仅 _&lt;租户名称&gt;_ - 单个租户)”。
    1. 对于“重定向 URI (可选)”，选择“Web”并输入一个值 。 此值无需是有效的 URI。
    1. 选择“注册”  。
1. 在“新建服务主体”页上，复制“显示名称”和“应用程序(客户端) ID”的值，保存供稍后使用 。

   应用程序 ID 是示例代码中的 `client_id` 值。

若要使用服务主体（应用程序），需要获取其密码。 下面介绍如何操作：

1. 从 Azure 门户，搜索并选择 Azure Active Directory，然后从左窗格中选择“应用注册” 。
1. 从列表中选择服务主体（应用程序）。
1. 从左窗格中选择“证书和机密”。
1. 选择“新建客户端机密”。
1. 在“客户端机密”页面上，输入“说明”，在“过期”下选择一个过期时间，然后选择“添加”   。

   在“客户端机密”页上，新机密的“值”列中的字符串就是你的密码 。 保存此值。

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="显示客户端机密的屏幕截图。":::

## <a name="set-up-authentication-using-service-principal"></a>使用服务主体设置身份验证

创建服务主体后，需要将 Purview 帐户的数据平面角色分配给上面创建的服务主体。 需要按照以下步骤分配角色，以在服务主体和 Purview 帐户之间建立信任。

1. 导航到 [Purview Studio](https://web.purview.azure.com/resource/)。
1. 在左侧菜单中选择“数据映射”。
1. 选择“集合”。
1. 在“集合”菜单中选择根集合。 这将是列表中的顶级集合，并将与你的 Purview 帐户同名。
1. 选择“角色分配”选项卡。
1. 将以下角色分配给上面创建的服务主体，以访问 Purview 中的各种数据平面。
    1. 用于访问目录数据平面的“数据管护者”角色。
    1. 用于访问扫描数据平面的“数据源管理员”角色。 
    1. 用于访问帐户数据平面的“集合管理员”角色。
    1. 用于访问元数据策略数据平面的“集合管理员”角色。

> [!Note]
> 只有“集合管理员”才能在 Purview 中分配数据平面角色（[Azure Purview 中的访问控制](./catalog-permissions.md)）。

## <a name="get-token"></a>获取令牌
你可以向以下 URL 发送 POST 请求以获取访问令牌。

https://login.microsoftonline.com/{your-tenant-id}/oauth2/token

需要向上述 URL 传递以下参数。

- **client_id**：在 Azure Active Directory 中注册的应用程序的客户端 ID，分配给 Purview 帐户的数据平面角色。
- **client_secret**：为上述应用程序创建的客户端密码。
- **grant_type**：此参数应该为“client_credentials”。
- **resource**：此参数应该为“https://purview.azure.net”
 
示例响应令牌：

```json
    {
        "token_type": "Bearer",
        "expires_in": "86399",
        "ext_expires_in": "86399",
        "expires_on": "1621038348",
        "not_before": "1620951648",
        "resource": "https://purview.azure.net",
        "access_token": "<<access token>>"
    }
```

使用上面的访问令牌来调用数据平面 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理数据源](manage-data-sources.md)
> [Purview 数据平面 REST API](/rest/api/purview/)
