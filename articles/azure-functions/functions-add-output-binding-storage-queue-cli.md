---
title: 使用命令行工具将 Azure Functions 连接到 Azure 存储
description: 了解如何通过将输出绑定添加到命令行项目，将 Azure Functions 连接到 Azure 存储队列。
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 7bbdd8b280782578bb5d6fadf7fb8de360e07bda
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831222"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>使用命令行工具将 Azure Functions 连接到 Azure 存储

本文介绍如何将 Azure 存储队列与在前一篇快速入门中创建的函数和存储帐户相集成。 可以使用一个输出绑定来实现这种集成。该绑定可将 HTTP 请求中的数据写入队列中的消息。  除了在前一篇快速入门中提到的几美分费用以外，完成本文不会产生其他费用。 有关绑定的详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

## <a name="configure-your-local-environment"></a>配置本地环境

::: zone pivot="programming-language-csharp"  
在开始之前，必须完成文章[快速入门：从命令行创建 Azure Functions 项目](create-first-function-cli-csharp.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end  
::: zone pivot="programming-language-javascript"  
在开始之前，必须完成文章[快速入门：从命令行创建 Azure Functions 项目](create-first-function-cli-node.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end   
::: zone pivot="programming-language-java"  
在开始之前，必须完成文章[快速入门：从命令行创建 Azure Functions 项目](create-first-function-cli-java.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end   
::: zone pivot="programming-language-typescript"  
在开始之前，必须完成文章[快速入门：从命令行创建 Azure Functions 项目](create-first-function-cli-typescript.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end   
::: zone pivot="programming-language-python"  
在开始之前，必须完成文章[快速入门：从命令行创建 Azure Functions 项目](create-first-function-cli-python.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end   
::: zone pivot="programming-language-powershell"  
在开始之前，必须完成文章[快速入门：从命令行创建 Azure Functions 项目](create-first-function-cli-powershell.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>注册绑定扩展
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

有关绑定的详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)和[队列输出配置](functions-bindings-storage-queue-output.md#configuration)。

## <a name="add-code-to-use-the-output-binding"></a>添加使用输出绑定的代码

定义队列绑定后，现在可以更新函数，以接收 `msg` 输出参数并将消息写入队列。

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

请注意，不需要编写任何用于身份验证、获取队列引用或写入数据的代码。  在 Azure Functions 运行时和队列输出绑定中可以方便地处理所有这些集成任务。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>查看 Azure 存储队列中的消息

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>将项目重新部署到 Azure

在本地验证函数已将消息写入 Azure 存储队列后，接下来可以重新部署项目以更新 Azure 上运行的终结点。

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
在 *LocalFunctionsProj* 文件夹中，使用 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) 命令重新部署项目（请将 `<APP_NAME>` 替换为你的应用的名称）。

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

在本地项目文件夹中，使用以下 Maven 命令重新发布项目：
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>在 Azure 中验证

1. 像在上一篇快速入门中一样，使用浏览器或 CURL 来测试重新部署的函数。

    # <a name="browser"></a>[浏览器](#tab/browser)
    
    将 publish 命令的输出中显示的完整“调用 URL”复制到浏览器的地址栏，并追加查询参数 `&name=Functions`。 浏览器显示的输出应与本地运行函数时显示的输出类似。

    ![在 Azure 上运行函数后浏览器中的输出](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    结合“调用 URL”运行 [`curl`](https://curl.haxx.se/)，并追加参数 `&name=Functions`。 该命令的输出应是文本“Hello Functions”。
    
    ![使用 CURL 在 Azure 上运行函数后的输出](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. 按上一部分所述再次检查存储队列，验证它是否包含已写入到其中的新消息。

## <a name="clean-up-resources"></a>清理资源

完成后，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>后续步骤

现已更新 HTTP 触发的函数，使其将数据写入存储队列。 现在，可以详细了解如何使用 Core Tools 和 Azure CLI 通过命令行进行 Functions 开发：

+ [使用 Azure Functions Core Tools](functions-run-local.md)  

+ [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [C# 中完整 Function 项目的示例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 中完整函数项目的示例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions JavaScript 开发人员指南](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 中完整函数项目的示例](/samples/browse/?products=azure-functions&languages=typescript)。

+ [Azure Functions TypeScript 开发人员指南](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python 中完整函数项目的示例](/samples/browse/?products=azure-functions&languages=python)。

+ [Azure Functions Python 开发人员指南](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 中完整函数项目的示例](/samples/browse/?products=azure-functions&languages=azurepowershell)。

+ [Azure Functions PowerShell 开发人员指南](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
