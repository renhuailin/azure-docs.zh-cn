---
title: Azure Functions PowerShell 开发人员参考
description: 了解如何使用 PowerShell 开发函数。
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 297d8af86f22cc588060cb90f327ad6dd335437d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741389"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 开发人员指南

本文详细介绍了如何使用 PowerShell 编写 Azure Functions。

PowerShell Azure 函数（简称函数）表示为在触发时执行的 PowerShell 脚本。 每个函数脚本都有一个相关的 `function.json` 文件，用于定义该函数的行为方式，例如其触发方式及其输入和输出参数。 若要了解详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)一文。 

与其他类型的函数一样，PowerShell 脚本函数采用与 `function.json` 文件中定义的所有输入绑定的名称相匹配的参数。 此外还传递了一个 `TriggerMetadata` 参数，该参数包含启动了该函数的触发器的附加信息。

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 你应当也已完成创建第一个 PowerShell 函数所需的 [PowerShell 函数快速入门](./create-first-function-vs-code-powershell.md)。

## <a name="folder-structure"></a>文件夹结构

PowerShell 项目所需的文件夹结构如下所示。 可更改此默认值。 有关详细信息，请参阅下面的 [scriptFile](#configure-function-scriptfile) 部分。

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

项目的根目录中有共享的 [`host.json`](functions-host-json.md) 文件，可用于配置函数应用。 每个函数都有一个文件夹，其中包含它自己的代码文件 (.ps1) 和绑定配置文件 (`function.json`)。 function.json 文件的父目录的名称始终是函数的名称。

某些绑定要求存在 `extensions.csproj` 文件。 [2.x 及更高版本](functions-versions.md)的 Functions 运行时中所需的绑定扩展在 `extensions.csproj` 文件中定义，实际库文件位于 `bin` 文件夹中。 本地开发时，必须[注册绑定扩展](functions-bindings-register.md#extension-bundles)。 在 Azure 门户中开发函数时，系统将为你完成此注册。

在 PowerShell 函数应用中，你可以选择创建一个在函数应用开始运行（也称为[冷启动](#cold-start)）时运行的 `profile.ps1`。 有关详细信息，请参阅 [PowerShell 配置文件](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>将 PowerShell 脚本定义为函数

默认情况下，Functions 运行时会在 `run.ps1` 中查找你的函数，其中，`run.ps1` 与其相应的 `function.json` 共享同一个父目录。

在执行时，会向你的脚本传递一些参数。 若要处理这些参数，请将 `param` 块添加到脚本顶部，如以下示例所示：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 参数

`TriggerMetadata` 参数用于提供有关触发器的附加信息。 附加元数据因绑定而异，但是它们都包含一个 `sys` 属性，该属性包含以下数据：

```powershell
$TriggerMetadata.sys
```

| Property   | 说明                                     | 类型     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 函数的触发时间（采用 UTC 格式）        | DateTime |
| MethodName | 已触发的函数的名称     | 字符串   |
| RandGuid   | 针对函数的此执行操作的唯一 GUID | 字符串   |

每个触发器类型都有一组不同的元数据。 例如，`QueueTrigger` 的 `$TriggerMetadata` 包含 `InsertionTime`、`Id`、`DequeueCount` 等内容。 有关队列触发器的元数据的详细信息，请参阅[队列触发器官方文档](functions-bindings-storage-queue-trigger.md#message-metadata)。 查看你在使用的[触发器](functions-triggers-bindings.md)的相关文档，以了解触发器元数据内部的情况。

## <a name="bindings"></a>绑定

在 PowerShell 中，需在函数的 function.json 中配置和定义[绑定](functions-triggers-bindings.md)。 函数通过多种方式来与绑定交互。

### <a name="reading-trigger-and-input-data"></a>读取触发器和输入数据

触发器和输入绑定被读取为传递给函数的参数。 输入绑定在 function.json 中有一个设置为 `in` 的 `direction`。 在 `function.json` 中定义的 `name` 属性是 `param` 块中参数的名称。 由于 PowerShell 使用已命名参数进行绑定，因此参数的顺序并不重要。 不过，最佳做法是遵循 `function.json` 中定义的绑定的顺序。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>写入输出数据

在 Functions 中，输出绑定在 function.json 中有一个设置为 `out` 的 `direction`。 你可以使用 `Push-OutputBinding` cmdlet（可用于 Functions 运行时）将数据写入到输出绑定。 在所有情况下，`function.json` 中定义的绑定的 `name` 属性都对应于 `Push-OutputBinding` cmdlet 的 `Name` 参数。

下面展示了如何在函数脚本中调用 `Push-OutputBinding`：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

还可以通过管道传入特定绑定的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 的行为因为 `-Name` 指定的值而异：

* 当指定的名称无法解析为有效的输出绑定时，会引发错误。

* 当输出绑定接受值的集合时，可以重复调用 `Push-OutputBinding` 来推送多个值。

* 当输出绑定只接受单一实例值时，第二次调用 `Push-OutputBinding` 会引发错误。

#### <a name="push-outputbinding-syntax"></a>Push-OutputBinding 语法

下面是用于调用 `Push-OutputBinding` 的有效参数：

| 名称 | 类型 | 位置 | 说明 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | 字符串 | 1 | 你要设置的输出绑定的名称。 |
| **`-Value`** | Object | 2 | 你要设置的输出绑定的值，它是从管道 ByValue 接受的。 |
| **`-Clobber`** | SwitchParameter | 名为 | （可选）指定了此项时，系统会强制为指定的输出绑定设置值。 | 

还支持以下常用参数： 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

有关详细信息，请参阅[关于 CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding 示例：HTTP 响应

HTTP 触发器使用名为 `response` 的输出绑定返回响应。 在下面的示例中，`response` 的输出绑定具有“output #1”值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

由于输出是针对 HTTP（仅接受单一实例值），因此在第二次调用 `Push-OutputBinding` 时会引发错误。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

对于仅接受单一实例值的输出，可以使用 `-Clobber` 参数替代旧值，而不要尝试将内容添加到集合。 下面的示例假定你已经添加了一个值。 通过使用 `-Clobber`，以下示例中的响应会替代现有值以返回“output #3”值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding 示例：队列输出绑定

`Push-OutputBinding` 用于将数据发送到输出绑定，例如 [Azure 队列存储输出绑定](functions-bindings-storage-queue-output.md)。 在下面的示例中，写入到队列的消息具有值“output #1”：

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

存储队列的输出绑定接受多个输出值。 在本例中，在第一个示例之后调用以下示例会向队列中写入具有以下两个项的列表：“output #1”和“output #2”。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

在前两个示例之后调用时，以下示例向输出集合添加两个额外的值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

写入到队列时，消息包含这四个值：“output #1”、“output #2”、“output #3”和“output #4”。

#### <a name="get-outputbinding-cmdlet"></a>Get-OutputBinding cmdlet

可以使用 `Get-OutputBinding` cmdlet 来检索当前为你的输出绑定设置的值。 此 cmdlet 会检索一个包含输出绑定名称及其各自值的哈希表。 

下面是使用 `Get-OutputBinding` 返回当前绑定值的示例：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 还包含一个名为 `-Name` 的参数，该参数可用于筛选返回的绑定，如以下示例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

`Get-OutputBinding` 支持通配符 (*)。

## <a name="logging"></a>Logging

PowerShell 函数中的日志记录的工作方式类似于常规的 PowerShell 日志记录。 你可以使用日志记录 cmdlet 将内容写入到每个输出流。 每个 cmdlet 都映射到 Functions 使用的一个日志级别。

| Functions 日志记录级别 | 日志记录 cmdlet |
| ------------- | -------------- |
| 错误 | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 信息 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`** <br/> 写入到信息级别的日志记录。 |
| 调试 | **`Write-Debug`** |
| 跟踪 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了这些 cmdlet 外，写入到管道的任何内容都会重定向到 `Information` 日志级别，并以默认的 PowerShell 格式设置进行显示。

> [!IMPORTANT]
> 并非仅使用 `Write-Verbose` 或 `Write-Debug` cmdlet 就可以查看详细级别和调试级别的日志记录。 你还必须配置日志级别阈值，该阈值声明你实际关注的日志级别。 若要了解详细信息，请参阅[配置函数应用日志级别](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>配置函数应用日志级别

可以使用 Azure Functions 来定义阈值级别，以便轻松控制 Functions 向日志写入内容的方式。 若要针对写入到控制台的所有跟踪设置阈值，请在 [`host.json` 文件][host.json 参考]中使用 `logging.logLevel.default` 属性。 此设置应用于 Function App 中的所有函数。

以下示例将阈值设置为对所有函数启用详细日志记录，但将阈值设置为对名为 `MyFunction` 的函数启用调试日志记录：

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

有关详细信息，请参阅 [host.json 参考]。

### <a name="viewing-the-logs"></a>查看日志

如果你的函数应用在 Azure 中运行，则可以使用 Application Insights 监视它。 若要了解有关查看和查询函数日志的详细信息，请阅读[监视 Azure Functions](functions-monitoring.md)。

如果要在本地运行函数应用以进行开发，则日志会默认保存到文件系统。 若要在控制台中查看日志，请在启动函数应用之前将 `AZURE_FUNCTIONS_ENVIRONMENT` 环境变量设置为 `Development`。

## <a name="triggers-and-bindings-types"></a>触发器和绑定类型

你可以将许多触发器和绑定用于你的函数应用。 [可在此处找到](functions-triggers-bindings.md#supported-bindings)触发器和绑定的完整列表。

所有触发器和绑定在代码中都表示为一些真实的数据类型：

* Hashtable
* 字符串
* byte[]
* int
* Double
* HttpRequestContext
* HttpResponseContext

此列表中的前五个类型是标准 .NET 类型。 最后两个类型仅由 [HttpTrigger 触发器](#http-triggers-and-bindings)使用。

函数中的每个绑定参数都必须是这些类型之一。

### <a name="http-triggers-and-bindings"></a>HTTP 触发器和绑定

HTTP 和 webhook 触发器以及 HTTP 输出绑定使用请求和响应对象来表示 HTTP 消息。

#### <a name="request-object"></a>请求对象

传递给脚本的请求对象是 `HttpRequestContext` 类型的，它具有以下属性：

| Property  | 说明                                                    | 类型                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 一个包含请求正文的对象。 `Body` 基于数据序列化为最佳类型。 例如，如果数据是 JSON，则它将作为哈希表传入。 如果数据是字符串，则它将作为字符串传入。 | 对象 |
| **`Headers`** | 一个包含请求头的字典。                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | 请求的 HTTP 方法。                                | 字符串                    |
| **`Params`**  | 一个包含请求的路由参数的对象。 | Dictionary<string,string><sup>*</sup> |
| **`Query`** | 一个包含查询参数的对象。                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | 请求的 URL。                                        | 字符串                    |

<sup>*</sup> 所有 `Dictionary<string,string>` 键都不区分大小写。

#### <a name="response-object"></a>响应对象

应当发送回的响应对象的类型为 `HttpResponseContext`，该类型具有以下属性：

| Property      | 说明                                                 | 类型                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 一个包含响应正文的对象。           | 对象                    |
| **`ContentType`** | 一种速记形式，用于设置响应的内容类型。 | 字符串                    |
| **`Headers`** | 一个包含响应标头的对象。               | 字典或哈希表   |
| **`StatusCode`**  | 响应的 HTTP 状态代码。                       | 字符串或整数             |

#### <a name="accessing-the-request-and-response"></a>访问请求和响应

使用 HTTP 触发器时，可以使用与任何其他输入绑定相同的方式访问 HTTP 请求。 它位于 `param` 块中。

使用 `HttpResponseContext` 对象返回一个响应，如下所示：

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

调用此函数的结果为：

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>触发器和绑定的类型强制转换

对于某些绑定（例如 blob 绑定），你可以指定参数的类型。

例如，若要将 Blob 存储中的数据作为字符串提供，请将以下类型强制转换添加到 `param` 块：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 配置文件

在 PowerShell 中存在 PowerShell 配置文件的概念。 如果你不熟悉 PowerShell 配置文件，请参阅[关于配置文件](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函数中，在首次部署并闲置后，会在应用中为每个 PowerShell 工作进程实例执行一次配置文件脚本（[冷启动](#cold-start)）。 如果通过设置 [PSWorkerInProcConcurrencyUpperBound](#concurrency) 值启用了并发，则会为所创建的每个运行空间运行配置文件脚本。

当你使用工具（例如 Visual Studio Code 和 Azure Functions Core Tools）创建函数应用时，系统会为你创建一个默认的 `profile.ps1`。 默认配置文件在 [Core Tools GitHub 存储库](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)中维护，其中包含：

* 自动向 Azure 进行 MSI 身份验证的功能。
* 根据需要启用 Azure PowerShell `AzureRM` PowerShell 别名的功能。

## <a name="powershell-versions"></a>PowerShell 版本

下表显示了适用于 Functions 运行时的每个主要版本的 PowerShell 版本，以及所需的 .NET 版本：

| Functions 版本 | PowerShell 版本                               | .NET 版本  | 
|-------------------|--------------------------------------------------|---------------|
| 3.x（建议） | PowerShell 7（建议）<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

可以通过输出任何函数的 `$PSVersionTable` 来查看当前版本。

若要详细了解 Azure Functions 运行时支持策略，请参阅[本文](./language-support-policy.md)

### <a name="running-local-on-a-specific-version"></a>在特定版本上以本地方式运行

以本地方式运行时，Azure Functions 运行时默认为使用 PowerShell Core 6。 若要在以本地方式运行时改用 PowerShell 7，则需要在项目根目录的 local.setting.json 文件的 `Values` 数组中添加设置 `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"`。 在 PowerShell 7 上以本地方式运行时，你的 local.settings.json 文件类似于以下示例： 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>更改 PowerShell 版本

函数应用必须在版本 3.x 上运行才能从 PowerShell Core 6 升级到 PowerShell 7。 若要了解如何执行此操作，请参阅[查看和更新当前运行时版本](set-runtime-version.md#view-and-update-the-current-runtime-version)。


请使用以下步骤更改函数应用使用的 PowerShell 版本。 可以通过 Azure 门户或 PowerShell 执行此操作。

# <a name="portal"></a>[门户](#tab/portal)

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到你的函数应用。

1. 在“设置”下，选择“配置” 。 在“常规设置”选项卡中，找到“PowerShell 版本”。 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="选择函数应用使用的 PowerShell 版本"::: 

1. 选择所需的 **PowerShell Core 版本** 并选择“保存”。 收到等待重启的警告时，请选择“继续”。 函数应用会在所选的 PowerShell 版本上重启。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

运行以下脚本以更改 PowerShell 版本： 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

将 `<SUBSCRIPTION_ID>`、`<RESOURCE_GROUP>` 和 `<FUNCTION_APP>` 分别替换为你的 Azure 订阅的 ID、你的资源组的名称和函数应用。  另外，将 `<VERSION>` 替换为 `~6` 或 `~7`。 你可以在返回的哈希表的 `Properties` 中验证 `powerShellVersion` 设置更新后的值。 

---

在对配置进行更改后，函数应用会重启。

## <a name="dependency-management"></a>依赖项管理

Functions 允许你利用 [PowerShell 库](https://www.powershellgallery.com)来管理依赖项。 启用依赖项管理后，使用 requirements.psd1 文件来自动下载所需的模块。 启用此行为的方法是：在 [host.json 文件](functions-host-json.md)的根目录中，将 `managedDependency` 属性设置为 `true`，如以下示例所示：

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

创建新的 PowerShell 函数项目时，默认情况下会启用依赖项管理，并且会包括 Azure [`Az` 模块](/powershell/azure/new-azureps-module-az)。 当前支持的最大模块数为 10。 支持的语法为 _`MajorNumber`_ `.*` 或确切的模块版本，如以下 requirements.psd1 示例所示：

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

更新 requirements.psd1 文件时，会在重启后安装更新的模块。

### <a name="target-specific-versions"></a>目标特定版本

你可能希望将特定版本的模块定位到 requirements.psd1 文件中。 例如，如果想要使用的 Az.Accounts 版本比包含的 Az 模块中的版本更旧，则需要以特定版本为目标，如下方示例中所示： 

```powershell
@{
    'Az.Accounts' = '1.9.5'
}
```

在这种情况下，还需要将 import 语句添加到 profile.ps1 文件的顶层，下方下示例所示：

```powershell
Import-Module Az.Accounts -RequiredVersion '1.9.5'
```

通过这种方式，当函数启动时，系统将最先加载旧版 Az.Account 模块。

### <a name="dependency-management-considerations"></a>依赖关系管理注意事项

使用依赖关系管理时，需考虑以下注意事项：

+ 托管依赖关系需要访问 <https://www.powershellgallery.com> 以下载模块。 在本地运行时，请通过添加任何所需的防火墙规则来确保运行时可以访问此 URL。

+ 托管依赖项当前不支持要求用户接受许可证的模块，无论是通过交互方式接受许可证，还是通过在调用 `Install-Module` 时提供 `-AcceptLicense` 开关。

### <a name="dependency-management-app-settings"></a>依赖关系管理应用设置

可以使用以下应用程序设置来更改下载和安装托管依赖项的方式。 

| 函数应用设置              | 默认值             | 说明                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| MDMaxBackgroundUpgradePeriod      | `7.00:00:00`（七天）     | 控制 PowerShell 函数应用的后台更新时间段。 若要了解详细信息，请参阅 [MDMaxBackgroundUpgradePeriod](functions-app-settings.md#mdmaxbackgroundupgradeperiod)。 | 
| MDNewSnapshotCheckPeriod         | `01:00:00`（一小时）       | 指定每个 PowerShell 辅助角色检查是否已安装托管依赖关系升级的频率。 若要了解详细信息，请参阅 [MDNewSnapshotCheckPeriod](functions-app-settings.md#mdnewsnapshotcheckperiod)。|
| MDMinBackgroundUpgradePeriod      | `1.00:00:00`（一天）     | 在上一次升级检查结束后但下一次升级检查开始前的时间段。 若要了解详细信息，请参阅 [MDMinBackgroundUpgradePeriod](functions-app-settings.md#mdminbackgroundupgradeperiod)。|

基本上，你的应用升级会在 `MDMaxBackgroundUpgradePeriod` 内启动，升级进程会在大约 `MDNewSnapshotCheckPeriod` 内完成。

## <a name="custom-modules"></a>自定义模块

在 Azure Functions 中利用你自己的自定义模块与你在 PowerShell 中通常使用的方法不同。

在本地计算机上，该模块安装在 `$env:PSModulePath` 的全局可用文件夹之一中。 在 Azure 中运行时，你无法访问计算机上安装的模块。 这意味着 PowerShell 函数应用的 `$env:PSModulePath` 与常规 PowerShell 脚本中的 `$env:PSModulePath` 不同。

在 Functions 中，`PSModulePath` 包含两个路径：

* 位于你的函数应用根目录中的 `Modules` 文件夹。
* 由 PowerShell 语言工作进程控制的 `Modules` 文件夹的路径。

### <a name="function-app-level-modules-folder"></a>函数应用级模块文件夹

若要使用自定义模块，可以将你的函数依赖的模块放置在 `Modules` 文件夹中。 在此文件夹中，模块可以自动用于函数运行时。 函数应用中的任何函数都可以使用这些模块。 

> [!NOTE]
> [requirements.psd1](#dependency-management) 文件中指定的模块会自动下载并包含在路径中，因此无需将其包含在模块文件夹中。 它们存储在本地的 `$env:LOCALAPPDATA/AzureFunctions` 文件夹中；在云中运行时，它们存储在 `/data/ManagedDependencies` 文件夹中。

若要利用自定义模块功能，请在你的函数应用的根目录中创建一个 `Modules` 文件夹。 将需要在函数中使用的模块复制到此位置。

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

对于 `Modules` 文件夹，函数应用应具有以下文件夹结构：

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

当你启动函数应用时，PowerShell 语言工作进程会将此 `Modules` 文件夹添加到 `$env:PSModulePath` 中，以便你可以像在常规 PowerShell 脚本中那样依赖于模块自动加载。

### <a name="language-worker-level-modules-folder"></a>语言辅助角色级模块文件夹

PowerShell 语言工作进程通常使用几个模块。 这些模块在 `PSModulePath` 的最后一个位置中定义。 

模块的当前列表如下所示：

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)：用于处理存档（例如 `.zip`、`.nupkg` 和其他存档）的模块。
* **ThreadJob**：PowerShell 作业 API 的基于线程的实现。

默认情况下，函数使用这些模块的最新版本。 若要使用特定的模块版本，请将该特定版本放置在你的函数应用的 `Modules` 文件夹中。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](functions-app-settings.md)在执行过程中将公开为环境变量。 可以使用 `$env:NAME_OF_ENV_VAR` 访问这些设置，如以下示例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本地运行时，可从 [local.settings.json](functions-develop-local.md#local-settings-file) 项目文件读取应用设置。

## <a name="concurrency"></a>并发

默认情况下，Functions PowerShell 运行时一次只能处理一个函数调用。 但是，在以下情况下，此并发级别可能不够高：

* 当你尝试同时处理大量调用时。
* 当函数调用同一函数应用内的其他函数时。

你可以根据工作负荷的类型来探索几个并发模型：

* 增大 ```FUNCTIONS_WORKER_PROCESS_COUNT```。 这样就可以在同一实例的多个进程中处理函数调用，这会带来一定的 CPU 和内存开销。 通常，I/O 绑定函数不受此开销影响。 对于 CPU 绑定函数，影响可能会很大。

* 增大 ```PSWorkerInProcConcurrencyUpperBound``` 应用设置值。 这样就可以在同一进程中创建多个运行空间，从而大大降低 CPU 和内存开销。

可以在函数应用的[应用设置](functions-app-settings.md)中设置这些环境变量。

Durable Functions 可能会显著提高可伸缩性，具体取决于你的用例。 若要了解详细信息，请参阅 [Durable Functions 应用程序模式](./durable/durable-functions-overview.md?tabs=powershell#application-patterns)。

>[!NOTE]
> 你可能会收到“请求正在排队，因为没有可用的运行空间”警告，请注意这不是错误。 此消息通知你，请求正在排队，会在前面的请求完成后获得处理。

### <a name="considerations-for-using-concurrency"></a>使用并发时的注意事项

默认情况下，PowerShell 是单线程脚本语言。 但是，可以通过在同一进程中使用多个 PowerShell 运行空间来添加并发。 创建的运行空间数量将与 ```PSWorkerInProcConcurrencyUpperBound``` 应用程序设置匹配。 吞吐量会受选定计划中可用的 CPU 和内存量影响。

Azure PowerShell 使用某些进程级上下文和状态，避免你进行过多的键入。 但是，如果你在函数应用中启用并发，并调用可更改状态的操作，则可能会出现争用情况。 这些争用情况很难调试，因为一个调用依赖于某个特定状态，而另一个调用更改了该状态。

Azure PowerShell 提供的并发性具有巨大的价值，因为某些操作可能需要相当长的时间。 但是，你必须谨慎操作。 如果怀疑出现争用情况，请将 PSWorkerInProcConcurrencyUpperBound 应用设置设为 `1`，并且为并发改用[语言工作进程级隔离](functions-app-settings.md#functions_worker_process_count)。

## <a name="configure-function-scriptfile"></a>配置函数 scriptFile

默认情况下通过 `run.ps1`（一个文件，与对应的 `function.json` 共享相同父目录）执行 PowerShell 函数。

可以使用 `function.json` 中的 `scriptFile` 属性来获取以下示例所示的文件夹结构：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在本例中，`myFunction` 的 `function.json` 包括一个 `scriptFile` 属性，该属性引用要运行的已导出函数所在的文件。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>通过配置 entryPoint 来使用 PowerShell 模块

本文展示了模板生成的默认 `run.ps1` 脚本文件中的 PowerShell 函数。
但是，你还可以在 PowerShell 模块中包含你的函数。 你可以通过在 function.json 配置文件中使用 `scriptFile` 和 `entryPoint` 字段来引用模块中的特定函数代码。

在本例中，`entryPoint` 是 `scriptFile` 中引用的 PowerShell 模块中的函数或 cmdlet 的名称。

考虑以下文件夹结构：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

其中，`PSFunction.psm1` 包含：

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

在此示例中，`myFunction` 的配置包含 `scriptFile` 属性，该属性引用 `PSFunction.psm1`，这是另一文件夹中的 PowerShell 模块。  `entryPoint` 属性引用 `Invoke-PSTestFunc` 函数，该函数是模块中的入口点。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此配置，`Invoke-PSTestFunc` 会完全像 `run.ps1` 那样执行。

## <a name="considerations-for-powershell-functions"></a>PowerShell 函数的注意事项

使用 PowerShell 函数时，请注意以下各部分中的注意事项。

### <a name="cold-start"></a>冷启动

当在[无服务器托管模型](consumption-plan.md)中开发 Azure Functions 时，冷启动是存在的现实情况。 冷启动指的是函数应用开始运行以处理请求所经历的时间段。 在消耗计划中，冷启动的发生更频繁，因为函数应用在非活动期间会关闭。

### <a name="bundle-modules-instead-of-using-install-module"></a>绑定模块，而不是使用安装-模块

脚本在每次调用时运行。 避免在脚本中使用 `Install-Module`。 在发布前改用 `Save-Module`，以便你的函数无需浪费时间来下载模块。 如果冷启动会影响你的函数，请考虑将函数应用部署到设置为“始终可用”或设置为[高级计划](functions-premium-plan.md)的[应用服务计划](dedicated-plan.md)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

[host.json 引用]: functions-host-json.md
