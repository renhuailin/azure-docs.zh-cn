---
title: 将函数连接到其他 Azure 服务
description: 了解如何向 Azure Functions 项目中的现有函数添加连接到其他 Azure 服务的绑定。
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258195"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>使用绑定将函数连接到 Azure 服务

创建函数时，请将特定于语言的触发器代码从一组触发器模板添加到项目中。 若要使用输入或输出绑定将函数连接到其他服务，则必须在函数中添加特定的绑定定义。 有关绑定的详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

## <a name="local-development"></a>本地开发       

在本地开发函数时，需要更新函数代码以添加绑定。 使用 Visual Studio Code 可以更轻松地将绑定添加到函数。  

### <a name="visual-studio-code"></a>Visual Studio Code

使用 Visual Studio Code 开发函数且函数使用 function.json 文件时，Azure Functions 扩展可自动将绑定添加到现有 function.json 文件。 若要了解详细信息，请参阅[添加输入和输出绑定](functions-develop-vs-code.md#add-input-and-output-bindings)。   

### <a name="manually-add-bindings-based-on-examples"></a>根据示例手动添加绑定

向现有函数添加绑定时，需要同时更新函数代码和 function.json 配置文件（如果语言使用它）。 .NET 类库和 Java 函数都使用属性而不是 function.json，因此你需要改为更新属性。

使用下表查找特定绑定类型的示例，这些示例可用来指导你更新现有函数。 首先，选择与你的项目相对应的语言选项卡。 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com)中开发函数时，可以在给定函数的“集成”选项卡中添加输入和输出绑定。 新绑定将添加到 function.json 文件或方法属性，具体取决于你的语言。 以下文章显示的示例介绍了如何在门户中向现有函数添加绑定：

+ [队列存储输出绑定](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB 输出绑定](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)
