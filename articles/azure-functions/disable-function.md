---
title: 如何在 Azure Functions 中禁用函数
description: 了解如何在 Azure Functions 中禁用与启用函数。
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 24ee2ce703287e62f1271c14f639048bb88da404
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663680"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何在 Azure Functions 中禁用函数

本文介绍如何在 Azure Functions 中禁用函数。 禁用某个函数意味着运行时将忽略针对该函数定义的自动触发器。 这使你可以在不停止整个函数应用的情况下阻止特定函数运行。

若要禁用函数，建议的方式是将格式为 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 的应用设置设为 `true`。 可以通过多种方式创建和修改此应用程序设置，包括使用 [Azure CLI](/cli/azure/) 以及 [Azure 门户](https://portal.azure.com)中函数的“概述”选项卡。 

## <a name="disable-a-function"></a>禁用函数

# <a name="portal"></a>[Portal](#tab/portal)

请使用函数的“概述”页上的“启用”和“禁用”按钮  。 这两个按钮通过更改 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 应用设置的值来工作。 此特定于函数的设置是在第一次禁用它时创建的。 

![函数状态开关](media/disable-function/function-state-switch.png)

即使从本地项目发布到函数应用时，仍可以使用门户来禁用函数应用中的函数。 

> [!NOTE]  
> 门户集成的测试功能会忽略 `Disabled` 设置。 这意味着，从门户的“测试”窗口启动时，禁用的函数仍会运行。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

在 Azure CLI 中，可以使用 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) 命令来创建和修改应用设置。 以下命令通过创建名为 `AzureWebJobs.QueueTrigger.Disabled` 的应用设置并将其设置为 `true` 来禁用名为 `QueueTrigger` 的函数。 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

若要重新启用此函数，请使用值 `false` 重新运行同一命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) 命令添加或更新某个应用程序设置。 以下命令通过创建名为 `AzureWebJobs.QueueTrigger.Disabled` 的应用设置并将其设置为 `true` 来禁用名为 `QueueTrigger` 的函数。 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

若要重新启用此函数，请使用值 `false` 重新运行同一命令。

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>槽位中的函数

在默认情况下，应用设置还会应用于在部署槽位中运行的应用。 但是，可以通过设置特定于槽位的应用设置来替代该槽位使用的应用设置。 例如，可能需要让某个函数（例如计时器触发的函数）在生产中处于活动状态，但不希望它在部署测试过程中处于活动状态。 

若要只在过渡槽中禁用函数，请执行以下操作：

# <a name="portal"></a>[Portal](#tab/portal)

导航到函数应用的槽位实例，方法是：选择“部署”下的“部署槽位”，选择槽位，然后选择槽位实例中的“函数”  。  选择函数，然后使用函数的“概述”页上的“启用”和“禁用”按钮  。 这两个按钮通过更改 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 应用设置的值来工作。 此特定于函数的设置是在第一次禁用它时创建的。 

还可以在该槽位实例的“配置”中直接添加名为 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 且值为 `true` 的应用设置。 在添加特定于槽位的应用设置时，请确保选中“部署槽位设置”框。 这样就会在交换期间保持槽位的设置值。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
若要重新启用此函数，请使用值 `false` 重新运行同一命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell 目前不支持此功能。

---

若要了解详细信息，请参阅 [Azure Functions 部署槽位](functions-deployment-slots.md)。

## <a name="localsettingsjson"></a>local.settings.json

在本地运行时，可以用相同的方式禁用函数。 若要禁用名为 `HttpExample` 的函数，请在 local.settings.json 文件的“值”集合中添加一个条目，如下所示：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": true
  }
}
``` 

## <a name="other-methods"></a>其他方法

虽然我们建议你将应用程序设置方法用于所有语言和所有运行时版本，但是你也可以使用其他几种方法来禁用函数。 这些方法因语言和运行时版本而异，为了后向兼容而保留。 

### <a name="c-class-libraries"></a>C# 类库

在类库函数中，还可以使用 `Disable` 属性来防止函数被触发。 通过此属性可自定义用于禁用函数的设置的名称。 使用可定义引用布尔应用设置的构造函数参数的属性版本，如以下示例中所示：

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
>门户使用应用程序设置来禁用 v1.x 函数。 当应用程序设置与 function.json 文件冲突时，可能会出现错误。 应从 function.json 文件中删除 `disabled` 属性，以防止出现错误。 

## <a name="considerations"></a>注意事项

禁用函数时，请谨记以下注意事项：

+ 如果使用本文所述的方法禁用 HTTP 触发的函数，则在本地计算机上运行时，仍然可以访问该终结点。  

+ 目前，在专用（应用服务）计划中当函数在 Linux 上运行时，无法禁用名称中包含连字符 (`-`) 的函数。 如果需要在专用计划中当函数在 Linux 上运行时禁用函数，请勿在函数名称中使用连字符。

## <a name="next-steps"></a>后续步骤

本文介绍了如何禁用自动触发器。 有关触发器的详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)。
