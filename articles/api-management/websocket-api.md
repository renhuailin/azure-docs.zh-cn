---
title: 使用 Azure 门户导入 WebSocket API | Microsoft Docs
titleSuffix: ''
description: 了解 API 管理如何支持 WebSocket，了解如何添加 WebSocket API，并了解 WebSocket 限制。
ms.service: api-management
author: v-hhunter
ms.author: v-hhunter
ms.topic: how-to
ms.date: 06/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 887e1257ef5585e99da1922aa840761ebcb7aa05
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439758"
---
# <a name="import-a-websocket-api-preview"></a>导入 WebSocket API（预览版）

借助 API 管理的 WebSocket API 解决方案，现在可以使用 API 管理来管理、保护、观察和公开 WebSocket 和 REST API，并提供用于发现和使用所有 API 的中心。 API 发布者可以通过以下方式在 API 管理中快速添加 WebSocket API：
* Azure 门户中的一个简单手势，以及 
* 管理 API 和 Azure 资源管理器。 

可以应用现有的访问控制策略（如 [JWT 验证](./api-management-access-restriction-policies.md#ValidateJWT)）来保护 WebSocket API。 还可以使用 Azure 门户和开发人员门户中的 API 测试控制台来测试 WebSocket API。 API 管理以现有的可观察性功能为基础，提供用于监视 WebSocket API 和排除其故障的指标和日志。 

[!INCLUDE [preview](./includes/preview/preview-callout-websocket-api.md)]

在本文中，你将：
> [!div class="checklist"]
> * 了解 Websocket 传递流。
> * 将 WebSocket API 添加到 API 管理实例。
> * 测试 WebSocket API。
> * 查看 WebSocket API 的指标和日志。
> * 了解 WebSocket API 的限制。

## <a name="prerequisites"></a>先决条件

- 现有的 API 管理实例。 [如果还没有实例，请创建一个](get-started-create-service-instance.md)。
- [WebSocket API](https://www.websocket.org/echo.html)。

## <a name="websocket-passthrough"></a>WebSocket 传递

API 管理支持 WebSocket 传递。 

:::image type="content" source="./media/websocket-api/websocket-api-passthrough.png" alt-text="WebSocket 传递流的直观图示":::

在 WebSocket 传递过程中，客户端应用程序与 API 管理网关建立 WebSocket 连接，然后与相应的后端服务建立连接。 API 管理然后代理 WebSocket 客户端-服务器消息。

1. 客户端应用程序向 APIM 网关发送 WebSocket 握手请求，调用 onHandshake 操作。
1. APIM 网关向相应的后端服务发送 WebSocket 握手请求。
1. 后端服务升级与 WebSocket 的连接。
1. APIM 网关升级与 WebSocket 的相应连接。
1. 建立连接对后，APIM 将在客户端应用程序和后端服务之间来回传递消息。
1. 客户端应用程序向 APIM 网关发送消息。
1. APIM 网关将消息转发到后端服务。
1. 后端服务向 APIM 网关发送消息。
1. APIM 网关将消息转发到客户端应用程序。
1. 当任一方断开连接时，APIM 终止相应的连接。

> [!NOTE]
> 客户端和后端连接由一对一映射组成。 

## <a name="onhandshake-operation"></a>onHandshake 操作

根据 [WebSocket 协议](https://tools.ietf.org/html/rfc6455)，当客户端应用程序尝试与后端服务建立 WebSocket 连接时，它将首先发送一个[“打开握手”请求](https://tools.ietf.org/html/rfc6455#page-6)。 API 管理中的每个 WebSocket API 都有一个 onHandshake 操作。 onHandshake 是一个不可变、不可移动、自动创建的系统操作。 onHandshake 操作使 API 发布者能够拦截这些握手请求并对它们应用 API 管理策略。

:::image type="content" source="./media/websocket-api/onhandshake-screen.png" alt-text="onHandshake 屏幕示例":::

## <a name="add-a-websocket-api"></a>添加 WebSocket API

1. 导航到 API 管理实例。
1. 在侧面导航菜单中的“API”部分下，选择“API” 。
1. 在“定义新 API”下，选择“WebSocket”图标 。
1. 在对话框中，选择“完整”并填写必填的表单字段。

    | 字段 | 说明 |
    |----------------|-------|
    | 显示名称 | WebSocket API 的显示名称。 |
    | 名称 | WebSocket API 的原始名称。 键入显示名称时自动填充。 |
    | WebSocket URL | 包含 WebSocket 名称的基 URL。 例如 ws://example.com/your-socket-name |
    | 产品 | 将 WebSocket API 与产品关联来发布它。 |
    | 网关 | 将 WebSocket API 与现有网关相关联。 |
 
1. 单击 **“创建”** 。

## <a name="test-your-websocket-api"></a>测试 WebSocket API

1. 导航到 WebSocket API。
1. 在 WebSocket API 中，选择 onHandshake 操作。
1. 选择“测试”选项卡以访问“测试”控制台。 
1. （可选）提供 WebSocket 握手所需的查询字符串参数。

    :::image type="content" source="./media/websocket-api/test-websocket-api.png" alt-text="测试 API 示例":::

1. 单击“连接” 。
1. 在“输出”中查看连接状态。
1. 在“有效负载”中输入值。 
1. 单击“发送”。
1. 在“输出”中查看收到的消息。
1. 重复上述步骤以测试不同的有效负载。
1. 测试完成时，选择“断开连接”。

## <a name="limitations"></a>限制

WebSocket API 为公共预览版，受 Azure 门户、管理 API 和 Azure 资源管理器支持。 以下是 API 管理中 WebSocket 支持的当前限制：

* 消耗层不支持 WebSocket API。
* [自托管网关](./how-to-deploy-self-hosted-gateway-azure-arc.md)不支持 WebSocket API。
* Azure CLI、PowerShell 和 SDK 不支持 WebSocket API 的管理操作。

### <a name="unsupported-policies"></a>不受支持的策略

以下策略不受支持，无法应用于 onHandshake 操作：
*  模拟响应
* 从缓存中获取
* 存储到缓存
* 允许跨域调用
* CORS
* JSONP
*  设置请求方法
* 设置正文
* 将 XML 转换为 JSON
* 将 JSON 转换为 XML
* 使用 XSLT 转换 XML
* 验证内容
* 验证参数
* 验证头
* 验证状态代码

> [!NOTE]
> 如果将这些策略应用于更高的范围（例如全局或产品），并且 WebSocket API 通过策略继承它们，则它们将在运行时被跳过。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)