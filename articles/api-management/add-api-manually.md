---
title: 使用 Azure 门户手动添加 API | Microsoft Docs
description: 本教程介绍如何使用 API 管理 (APIM) 手动添加 API。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: tutorial
ms.date: 04/26/2021
ms.author: danlep
ms.openlocfilehash: 2e88bef36179a0e923ad6dfa8c7744f20d0a4d33
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623962"
---
# <a name="add-an-api-manually"></a>手动添加 API

本文中的步骤说明如何使用 Azure 门户手动将 API 添加到 API 管理 (APIM) 实例。 想要模拟某个 API 时，就往往需要创建一个空白 API 并手动对其进行定义。 有关模拟 API 的详细信息，请参阅[模拟 API 响应](mock-api-responses.md)。

若要导入现有的 API，请参阅[相关主题](#related-topics)部分。

本文将会创建一个空白的 API，并将 [httpbin.org](https://httpbin.org)（一个公共测试服务）指定为后端 API。

## <a name="prerequisites"></a>先决条件

完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>创建 API

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”。
2. 在左侧菜单中，选择“+ 添加 API”。
3. 从列表中选择“空白 API”。  
    空白 API![](media/add-api-manually/blank-api.png)  
4. 输入 API 的设置。 在[导入和发布第一个 API](import-and-publish.md#import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
5. 选择“创建” 。

此时，API 管理中没有任何操作映射到后端 API 中的操作。 如果调用通过后端（而不是通过 API 管理）公开的操作，则会收到 404 错误。

>[!NOTE] 
> 默认情况下，在添加某个 API 时，除非你允许，否则即使该 API 已连接到某个后端服务，APIM 也不会公开任何操作。 若要允许后端服务的某个操作，请创建一个映射到后端操作的 APIM 操作。

## <a name="add-and-test-an-operation"></a>添加并测试操作

本部分介绍如何添加“/get”操作，以将其映射到后端“http://httpbin.org/get”操作。

### <a name="add-an-operation"></a>添加操作

1. 选择上一步中创建的 API。
2. 单击“+ 添加操作”。
3. 在“URL”中，选择“GET”，并在资源中输入 `/get` 。
4. 输入“FetchData”作为“显示名称”。
5. 选择“保存” 。

### <a name="test-an-operation"></a>测试操作

在 Azure 门户中测试操作。 或者，可以在 **开发人员门户** 中测试操作。

1. 选择“测试”选项卡。
2. 选择“FetchData”。
3. 按“发送”。

随后将显示“http://httpbin.org/get”操作生成的响应。 若要转换操作，请参阅[转换和保护 API](transform-api.md)。

## <a name="add-and-test-a-parameterized-operation"></a>添加并测试参数化操作

本部分介绍如何添加一个采用参数的操作。 在本例中，我们将该操作映射到“http://httpbin.org/status/200”。

### <a name="add-the-operation"></a>添加操作

1. 选择上一步中创建的 API。
2. 单击“+ 添加操作”。
3. 在“URL”中，选择“GET”，并在资源中输入 `*/status/{code}` 。 （可选）可以提供与此参数关联的某些信息。 例如，为“类型”输入“数字”，为“值”输入“200”（默认值）。
4. 为显示名称输入“WildcardGet”。
5. 选择“保存” 。

### <a name="test-the-operation"></a>测试操作 

在 Azure 门户中测试操作。  或者，可以在 **开发人员门户** 中测试操作。

1. 选择“测试”选项卡。
2. 选择“WildcardGet”。 默认情况下，代码值设置为“200”。 可以更改默认值以测试其他值。 例如，键入“418”。
3. 按“发送”。

    随后将显示“http://httpbin.org/status/200”操作生成的响应。 若要转换操作，请参阅[转换和保护 API](transform-api.md)。

## <a name="add-and-test-a-wildcard-operation"></a>添加和测试通配符操作

此部分显示如何添加通配符操作。 通过通配符操作可使用 HTTP 请求传递任意值。 可创建一个通配符 GET 操作，而不是像前述几部分所示创建单独的 GET 操作。

### <a name="add-the-operation"></a>添加操作

1. 选择上一步中创建的 API。
2. 单击“+ 添加操作”。
3. 在“URL”中，选择“GET”，并在资源中输入 `/*` 。
4. 为显示名称输入“WildcardGet”。
5. 选择“保存” 。

### <a name="test-the-operation"></a>测试操作 

在 Azure 门户中测试操作。  或者，可以在 **开发人员门户** 中测试操作。

1. 选择“测试”选项卡。
2. 选择“WildcardGet”。 尝试在前述部分中已经测试过的一个或多个 GET 操作，或者尝试其他受支持的 GET 操作。 

    例如，在“模板参数”中，将通配符 (*) 名称旁边的值更新为 `headers`。 该操作会返回传入请求的 HTTP 标头。
1. 按“发送”。

    随后将显示“http://httpbin.org/headers”操作生成的响应。 若要转换操作，请参阅[转换和保护 API](transform-api.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
