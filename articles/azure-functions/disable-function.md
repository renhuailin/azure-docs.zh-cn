---
title: 如何在 Azure Functions 中禁用函数
description: 了解如何在 Azure Functions 中禁用与启用函数。
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551037"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何在 Azure Functions 中禁用函数

本文介绍如何在 Azure Functions 中禁用函数。 禁用某个函数意味着运行时将忽略针对该函数定义的自动触发器。 这使你可以在不停止整个函数应用的情况下阻止特定函数运行。

若要禁用函数，建议的方式是将格式为 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 的应用设置设为 `true`。 您可以通过多种方式创建和修改此应用程序设置，包括使用 [Azure CLI](/cli/azure/)和 [Azure 门户](https://portal.azure.com)中函数的 "**概述**" 选项卡。 

> [!NOTE]  
> 如果使用本文所述的方法禁用 HTTP 触发的函数，则在本地计算机上运行时，仍然可以访问该终结点。  

## <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中，可以使用 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令来创建和修改应用设置。 以下命令通过创建名为“`AzureWebJobs.QueueTrigger.Disabled`”的应用设置并将其设置为 `true` 来禁用名为“`QueueTrigger`”的函数。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

若要重新启用此函数，请使用值 `false` 重新运行同一命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>使用门户

还可以使用函数的“概览”页上的“启用”和“禁用”按钮。 这两个按钮通过更改 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 应用设置的值来工作。 此特定于函数的设置是在第一次禁用它时创建的。 

![函数状态开关](media/disable-function/function-state-switch.png)

即使从本地项目发布到函数应用时，仍可以使用门户来禁用函数应用中的函数。 

> [!NOTE]  
> 门户集成的测试功能会忽略 `Disabled` 设置。 这意味着，从门户的“测试”窗口启动时，禁用的函数仍会运行。 

## <a name="localsettingsjson"></a>local.settings.json

在本地运行时，可以用相同的方式禁用函数。 若要禁用名为 `HttpExample` 的函数，请在 local.settings.json 文件的“值”集合中添加一个条目，如下所示：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>其他方法

虽然我们建议你将应用程序设置方法用于所有语言和所有运行时版本，但是你也可以使用其他几种方法来禁用函数。 这些方法因语言和运行时版本而异，为了后向兼容而保留。 

### <a name="c-class-libraries"></a>C# 类库

在类库函数中，还可以使用 `Disable` 属性来防止函数被触发。 此属性允许你自定义用于禁用函数的设置的名称。 使用特性的版本，该特性允许您定义引用布尔型应用程序设置的构造函数参数，如以下示例中所示：

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

使用此方法可以通过更改应用设置来启用和禁用函数，而无需重新编译或重新部署。 更改应用设置会导致函数应用重启，因此，可立即识别到禁用状态的更改。

还有一个参数的构造函数，该参数不接受设置名称的字符串。 不建议使用此版本的属性。 如果使用此版本，则必须重新编译并重新部署项目，以更改函数的禁用状态。

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - 脚本语言

在版本 1.x 中，还可以使用 function.json 文件的 `disabled` 属性来指示运行时不要触发函数。 此方法仅适用于脚本语言（例如，C# 脚本和 JavaScript）。 `disabled` 属性可设置为 `true` 或某个应用设置的名称：

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
或 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

在第二个示例中，当存在名为 IS_DISABLED 的应用设置且其值设置为 `true` 或 1 时，将禁用相应的函数。

>[!IMPORTANT]  
>门户使用应用程序设置来禁用 v1. x 函数。 当应用程序设置与 function.json 文件冲突时，可能会出现错误。 应从 function.json 文件中删除 `disabled` 属性，以防止出现错误。 


## <a name="next-steps"></a>后续步骤

本文介绍了如何禁用自动触发器。 有关触发器的详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)。
