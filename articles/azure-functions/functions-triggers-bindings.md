---
title: Azure Functions 中的触发器和绑定
description: 了解如何使用触发器和绑定将 Azure 函数连接到联机事件和基于云的服务。
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 4cafe9af1eb5a765ab86bafb63cc9ab7d0889dc8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627593"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 触发器和绑定概念

本文概要介绍有关函数触发器和绑定的概念。

触发器是导致函数运行的因素。 触发器定义函数的调用方式，一个函数必须刚好有一个触发器。 触发器具有关联的数据，这些数据通常作为函数的有效负载提供。 

绑定到函数是以声明方式将另一个资源连接到该函数的一种方式；绑定可以输入绑定和/或输出绑定的形式进行连接。   绑定中的数据作为参数提供给函数。

可根据需要，混合搭配不同的绑定。 绑定是可选的，一个函数可以有一个或多个输入绑定和/或输出绑定。

使用触发器和绑定可以避免对其他服务进行硬编码访问。 函数接收函数参数中的数据（例如，队列消息内容）。 使用函数的返回值发送数据（例如，用于创建队列消息）。 

以下示例演示如何实现不同的函数。

| 示例方案 | 触发器 | 输入绑定 | 输出绑定 |
|-------------|---------|---------------|----------------|
| 新的队列消息抵达，此时会运行一个函数来写入到另一个队列。 | 队列<sup>*</sup> | 无  | 队列<sup>*</sup> |
|计划的作业读取 Blob 存储内容，并创建新的 Cosmos DB 文档。 | Timer | Blob 存储 | Cosmos DB |
|事件网格用于读取 Blob 存储中的映像以及 Cosmos DB 中的文档，以发送电子邮件。 | 事件网格 | Blob 存储和 Cosmos DB | SendGrid |
| 一个 Webhook，它使用 Microsoft Graph 来更新 Excel 工作表。 | HTTP | 无  | Microsoft Graph |

<sup>\*</sup> 表示不同的队列

这些示例并不详尽，旨在演示如何同时使用触发器和绑定。

###  <a name="trigger-and-binding-definitions"></a>触发器和绑定的定义

触发器和绑定的定义根据开发语言的不同而异。

| 语言 | 触发器和绑定的配置方式... |
|-------------|--------------------------------------------|
| C# 类库 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用 C# 特性修饰方法和参数 |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用 Java 注释修饰方法和参数  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;更新 [function.json](./functions-reference.md)（[架构](http://json.schemastore.org/function)） |

对于依赖 function.json 的语言，门户在“集成”选项卡中提供了用于添加绑定的 UI。还可以在门户中函数的“代码 + 测试”选项卡中直接编辑该文件。 使用 Visual Studio Code，可以通过一套方便的提示轻松地[将绑定添加到 function.json 文件](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project)。 

在 .NET 和 Java 中，参数类型定义了输入数据的数据类型。 例如，使用 `string` 绑定到队列触发器的文本、一个要读取为二进制内容的字节数组，以及一个要反序列化为对象的自定义类型。 由于 .NET 类库函数和 Java 函数不依赖 function.json 来绑定定义，所以在门户中不能对它们进行创建和编辑。 C# 门户编辑基于C# 脚本，该脚本使用 function.json 而不是特性。

若要详细了解如何将绑定添加到现有函数，请参阅[使用绑定将函数连接到 Azure 服务](add-bindings-existing-function.md)。

对于动态键入的语言（如 JavaScript），请在 function.json 文件中使用 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，将 `dataType` 设置为 `binary`：

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

`dataType` 的其他选项是 `stream` 和 `string`。

## <a name="binding-direction"></a>绑定方向

所有触发器和绑定在 [function.json](./functions-reference.md) 文件中都有一个 `direction` 属性：

- 对于触发器，方向始终为 `in`
- 输入和输出绑定使用 `in` 和 `out`
- 某些绑定支持特殊方向 `inout`。 如果使用 `inout`，则只能通过门户中的“集成”选项卡使用“高级编辑器”。  

使用[类库中的特性](functions-dotnet-class-library.md)来配置触发器和绑定时，方向在特性构造函数中提供或推断自参数类型。

## <a name="add-bindings-to-a-function"></a>向函数添加绑定

可以使用输入或输出绑定将函数连接到其他服务。 通过向函数添加绑定的具体定义来添加绑定。 若要了解如何操作，请参阅[向 Azure Functions 中的现有函数添加绑定](add-bindings-existing-function.md)。  

## <a name="supported-bindings"></a>支持的绑定

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

有关哪些绑定处于预览状态或已批准在生产环境中使用的信息，请参阅[支持的语言](supported-languages.md)。

## <a name="bindings-code-examples"></a>绑定代码示例

使用下表查找特定绑定类型的示例，这些示例演示如何在函数中使用绑定。 首先，选择与你的项目相对应的语言选项卡。 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>自定义绑定

可以创建自定义输入和输出绑定。 绑定必须以 .NET 编写，但可以在任何支持的语言中使用。 有关创建自定义绑定的详细信息，请参阅[创建自定义输入和输出绑定](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)。

## <a name="resources"></a>资源
- [绑定表达式和模式](./functions-bindings-expressions-patterns.md)
- [使用 Azure 函数返回值](./functions-bindings-return-value.md)
- [如何注册绑定表达式](./functions-bindings-register.md)
- 测试：
  - [在 Azure Functions 中测试代码的策略](functions-test-a-function.md)
  - [手动运行非 HTTP 触发的函数](functions-manually-run-non-http.md)
- [处理绑定错误](./functions-bindings-errors.md)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [注册 Azure Functions 绑定扩展](./functions-bindings-register.md)
