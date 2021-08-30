---
title: Azure Functions 的应用设置参考
description: 有关 Azure Functions 应用设置或环境变量的参考文档。
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 7275d81401444dffbe0917bdb72ba79100880749
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862685"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions 的应用设置参考

函数应用中的应用设置包含对该函数应用的所有函数产生影响的全局配置选项。 在本地运行时，这些设置将作为本地[环境变量](functions-develop-local.md#local-settings-file)进行访问。 本文列出可在函数应用中使用的应用设置。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) 文件和 [local.settings.json](functions-develop-local.md#local-settings-file) 文件中提供了其他全局配置选项。

> [!NOTE]
> 可以使用应用程序设置替代 host.json 设置值，而不必更改 host.json 文件本身。 这对于需要针对特定环境配置或修改特定 host.json 设置的方案很有用。 这也让你可以更改 host.json 设置，而不必重新发布项目。 若要了解详细信息，请参阅 [host.json 参考文章](functions-host-json.md#override-hostjson-values)。 如果更改函数应用设置，则需要重启函数应用。

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights 的检测密钥。 仅使用 `APPINSIGHTS_INSTRUMENTATIONKEY` 或 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中的一个。 当 Application Insights 在主权云中运行时，请使用 `APPLICATIONINSIGHTS_CONNECTION_STRING`。 有关详细信息，请参阅[如何配置对 Azure Functions 的监视](configure-monitoring.md)。

|键|示例值|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights 的连接字符串。 在以下情况下使用 `APPLICATIONINSIGHTS_CONNECTION_STRING` 而不是 `APPINSIGHTS_INSTRUMENTATIONKEY`：

+ 当函数应用需要通过连接字符串提供支持的已添加自定义项时。
+ 当 Application Insights 实例在需要自定义终结点的主权云中运行时。

有关详细信息，请参阅[连接字符串](../azure-monitor/app/sdk-connection-string.md)。

|键|示例值|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[key];IngestionEndpoint=[url];LiveEndpoint=[url];ProfilerEndpoint=[url];SnapshotEndpoint=[url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

默认情况下，[Functions 代理](functions-proxies.md)使用快捷方式从代理直接将 API 调用发送到同一函数应用中的函数。 使用此快捷方式取代创建新的 HTTP 请求。 此设置让你能够禁用该快捷方式行为。

|键|值|说明|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|是|具有指向本地函数应用中函数的后端 URL 的调用不会直接发送到函数， 相反，请求会定向回函数应用的 HTTP 前端。|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|具有指向本地函数应用中函数的后端 URL 的调用会直接转发到函数。 这是默认值。 |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

此设置控制字符 `%2F` 在路由参数插入后端 URL 时是否在路由参数中解码为斜杠。

|键|值|说明|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|是|包含编码斜杠的路由参数已解码。 |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|所有路由参数均原样传递，这是默认行为。 |

例如，考虑位于 `myfunction.com` 域的函数应用的 proxies.json 文件。

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

如果 `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` 设置为 `true`，则 URL `example.com/api%2ftest` 解析为 `example.com/api/test`。 默认情况下，URL 保持为 `example.com/test%2fapi` 不变。 有关详细信息，请参阅 [Functions 代理](functions-proxies.md)。

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

在 2.x 和更高版本的 Functions 运行时中，基于运行时环境配置应用行为。 在初始化期间读取此值，并且可以将其设置为任意值。 运行时仅接受 `Development`、`Staging` 和 `Production` 的值。 如果在 Azure 中运行时此应用程序设置不存在，则假定环境为 `Production`。 如果需要将 Azure 中的运行时环境更改为 `Production` 以外的其他内容，请使用此设置，而不是 `ASPNETCORE_ENVIRONMENT`。 在本地计算机上运行时，Azure Functions Core Tools 将 `AZURE_FUNCTIONS_ENVIRONMENT` 设置为 `Development`，并且无法在 local.settings.json 文件中重写。 若要了解详细信息，请参阅[基于环境的启动类和方法](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods)。

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

在 Functions 运行时的版本 2.x 和更高版本中，应用程序设置可以替代当前环境中的 [host.json](functions-host-json.md) 设置。 这些替代表示为名为 `AzureFunctionsJobHost__path__to__setting` 的应用程序设置。 有关详细信息，请参阅[替代 host.json 值](functions-host-json.md#override-hostjson-values)。

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

用于存储日志并在门户上的“监视”选项卡中显示这些日志的可选存储帐户连接字符串。 此设置仅对面向 Azure Functions 运行时版本 1.x 的应用有效。 存储帐户必须是支持 Blob、队列和表的通用帐户。 有关详细信息，请参阅[存储帐户要求](storage-considerations.md#storage-account-requirements)。

|键|示例值|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> 为了获得更好的性能和体验，运行时版本 2.x 及更高版本使用 APPINSIGHTS_INSTRUMENTATIONKEY 和 App Insights 进行监视，而不是使用 `AzureWebJobsDashboard`。

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` 表示禁用针对函数应用根 URL 显示的默认登录页。 默认值为 `false`。

|键|示例值|
|---|------------|
|AzureWebJobsDisableHomepage|是|

如果省略此应用设置或将其设置为 `false`，则会显示类似于以下示例的页来响应 URL `<functionappname>.azurewebsites.net`。

![函数应用登录页](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` 表示在编译 .NET 代码时使用“发布”模式；`false` 表示使用“调试”模式。 默认值为 `true`。

|键|示例值|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|是|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

要启用的 beta 功能的逗号分隔列表。 由这些标志启用的 Beta 功能尚未准备好用于生产，但在发布这些功能之前可针对试验目的启用这些功能。

|键|示例值|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

指定用于密钥存储的存储库或提供程序。 目前，支持的存储库包括 blob 存储（“Blob”）和本地文件系统（“Files”）。 默认为在版本 2 中使用 blob，在版本 1 中使用文件系统。

|键|示例值|
|---|------------|
|AzureWebJobsSecretStorageType|文件|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 运行时使用此存储帐户连接字符串执行常规操作。 此存储帐户的一些用途包括密钥管理、计时器触发器管理和事件中心检查点。 存储帐户必须是支持 Blob、队列和表的通用帐户。 请参阅[存储帐户](functions-infrastructure-as-code.md#storage-account)和[存储帐户要求](storage-considerations.md#storage-account-requirements)。

|键|示例值|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

用于 TypeScript 的编译器的路径。 允许根据需要重写默认值。

|键|示例值|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

指示是否在 Azure 门户中启用了编辑。 有效值为“readwrite”和“readonly”。

|键|示例值|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

托管函数应用的 Functions 运行时版本。 波浪符（`~`）加主要版本号表示使用该主要版本的最新版本（例如“~3”）。 当同一主要版本的新版本可用时，会自动在函数应用中安装新版本。 若要让应用固定使用特定的版本，请使用完整版本号（例如“3.0.12345”）。 默认值为“~3”。 值 `~1` 让应用固定使用运行时版本 1.x。 有关详细信息，请参阅 [Azure Functions 运行时版本概述](functions-versions.md)。 若为值 `~4`，可运行预览版 Azure Functions 以使用 .NET 6.0 预览版。 若要了解详细信息，请参阅 [Azure Functions v4 早期预览版](https://aka.ms/functions-dotnet6earlypreview-wiki)页面。

|键|示例值|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~3|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

此设置使函数应用能够在版本 3.x 运行时上以版本 2.x 兼容模式运行。 仅当在[将函数应用从运行时版本 2.x 升级到版本 3.x](functions-versions.md#migrating-from-2x-to-3x) 时遇到问题的情况下，才使用此设置。

>[!IMPORTANT]
> 在更新应用以便在版本 3.x 上正常运行时，此设置仅用作短期解决方法。 只要 [2.x 运行时受支持](functions-versions.md)，此设置就受支持。 如果在不使用此设置的情况下遇到阻止应用在版本 3.x 上运行的问题，请[报告问题](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)。

需要 [FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) 设置为 `~3`。

|键|示例值|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|是|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

指定语言工作进程的最大数量，其默认值为 `1`。 允许的最大值为 `10`。 函数调用均匀地分布在语言工作进程中。 语言工作进程每 10 秒生成一次，直到达到 FUNCTIONS\_WORKER\_PROCESS\_COUNT 设置的计数。 使用多个语言工作进程与[缩放](functions-scale.md)不同。 当工作负荷混合使用 CPU 绑定和 I/O 绑定调用时，请考虑使用此设置。 此设置适用于所有非 .NET 语言。

|键|示例值|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|

## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

要在函数应用中加载的语言辅助角色运行时。  此项对应于应用程序中正在使用的语言（例如，“`dotnet`”）。 从 Azure Functions 运行时版本 2.x 开始，给定函数应用只能支持一种语言。

|键|示例值|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|节点|

有效值：

| 值 | 语言 |
|---|---|
| `dotnet` | [C#（类库）](functions-dotnet-class-library.md)<br/>[C#（脚本）](functions-reference-csharp.md) |
| `dotnet-isolated` | [C#（隔离进程）](dotnet-isolated-process-guide.md) |
| `java` | [Java](functions-reference-java.md) |
| `node` | [JavaScript](functions-reference-node.md)<br/>[TypeScript](functions-reference-node.md#typescript) |
| `powershell` | [PowerShell](functions-reference-powershell.md) |
| `python` | [Python](functions-reference-python.md) |

## <a name="mdmaxbackgroundupgradeperiod"></a>MDMaxBackgroundUpgradePeriod

控制 PowerShell 函数应用的托管依赖项后台更新周期，默认值为 `7.00:00:00`（每周）。

每个 PowerShell 工作进程都会在进程启动时检查 PowerShell 库上的模块升级，并在之后每 `MDMaxBackgroundUpgradePeriod` 检查一次。 当 PowerShell 库中有新的模块版本时，该版本会被安装到文件系统，并提供给 PowerShell 工作进程。 减小此值后，函数应用不但可以更快地获取较新的模块版本，而且可以增加应用资源使用率（网络 I/O、CPU、存储）。 增大此值会减少应用的资源使用率，但也可能会延迟将新的模块版本传递给你的应用。

|键|示例值|
|---|------------|
|MDMaxBackgroundUpgradePeriod|7.00:00:00|

有关详细信息，请参阅[依赖项管理](functions-reference-powershell.md#dependency-management)。

## <a name="mdnewsnapshotcheckperiod"></a>MDNewSnapshotCheckPeriod

指定每个 PowerShell 辅助角色检查是否已安装托管依赖关系升级的频率。 默认频率为 `01:00:00`（每小时）。

将新的模块版本安装到文件系统后，必须重启每个 PowerShell 工作进程。 重启 PowerShell 工作进程会影响应用可用性，因为它可能会中断当前的函数执行操作。 在所有 PowerShell 工作进程都重启之前，函数调用可能使用旧的模块版本，也可能使用新的模块版本。 重启所有 PowerShell 工作进程的操作会在 `MDNewSnapshotCheckPeriod` 内完成。

在每个 `MDNewSnapshotCheckPeriod` 中，PowerShell 工作进程会检查是否已安装托管依赖关系升级。 安装升级后，重启随即开始。 增加此值会降低因重启而中断的频率。 但是，这种增加也可能会增加函数调用使用新/旧模块版本的时间，这种情况不确定。

|键|示例值|
|---|------------|
|MDNewSnapshotCheckPeriod|01:00:00|

有关详细信息，请参阅[依赖项管理](functions-reference-powershell.md#dependency-management)。


## <a name="mdminbackgroundupgradeperiod"></a>MDMinBackgroundUpgradePeriod

在前一次托管依赖关系升级检查之后，启动下一次升级检查之前的时间段，默认值为 `1.00:00:00`（每天）。

为了避免在频繁重启工作进程时进行过多的模块升级，当任何工作进程在上一个 `MDMinBackgroundUpgradePeriod` 中启动了模块升级检查时，系统不会执行该检查。

|键|示例值|
|---|------------|
|MDMinBackgroundUpgradePeriod|1.00:00:00|

有关详细信息，请参阅[依赖项管理](functions-reference-powershell.md#dependency-management)。

## <a name="pip_extra_index_url"></a>PIP\_EXTRA\_INDEX\_URL

此设置的值指示 Python 应用的自定义包索引 URL。 如果需要使用在额外的包索引中找到的自定义依赖项运行远程生成，请使用此设置。

|键|示例值|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|http://my.custom.package.repo/simple |

若要了解详细信息，请参阅 Python 开发人员参考中的[自定义依赖项](functions-reference-python.md#remote-build-with-extra-index-url)。

## <a name="python_isolate_worker_dependencies"></a>PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES

配置特定于 Python 函数应用。 它定义模块加载顺序的优先级。 当 Python 函数应用面临与模块冲突相关的问题时（例如，在项目中使用 protobuf、tensorflow 或 grpcio 时），将此应用设置配置为 `1` 应该能够解决问题。 默认情况下，此值设置为 `0`。

|键|值|说明|
|---|-----|-----------|
|PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES|0| 优先从内部 Python 辅助角色的依赖项加载 Python 库。 requirements.txt 中定义的第三方库可能会被隐藏。 |
|PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES|1| 优先从 requirements.txt 中定义的应用程序包加载 Python 库。 这可以防止你的库与内部 Python 辅助角色的库发生冲突。 |

## <a name="python_enable_worker_extensions"></a>PYTHON\_ENABLE\_WORKER\_EXTENSIONS

配置特定于 Python 函数应用。 将其设置为 `1` 后，工作线程能够加载 requirements.txt 中定义的 [Python工作线程扩展](functions-reference-python.md#python-worker-extensions)。 通过该扩展，函数应用能够访问第三方包提供的新功能。 它也可能改变函数在应用中加载和调用的行为。 请确保所选扩展可靠，因为你需要承担使用它所带来的风险。 Azure Functions 不提供针对任何扩展的明示保证。 有关如何使用扩展的详细说明，请访问相关扩展的手册页或自述文件。此值默认设置为 `0`。

|键|值|说明|
|---|-----|-----------|
|PYTHON\_ENABLE\_WORKER\_EXTENSIONS|0| 禁用任何 Python 工作线程扩展。 |
|PYTHON\_ENABLE\_WORKER\_EXTENSIONS|1| 允许 Python 工作线程从 requirements.txt 加载扩展。 |

## <a name="python_threadpool_thread_count"></a>PYTHON\_THREADPOOL\_THREAD\_COUNT

指定 Python 语言辅助角色将用于执行函数调用的最大线程数，对于 Python 版本 `3.8` 及更低版本，默认值为 `1`。 对于 Python 版本 `3.9` 及更高版本，该值设置为 `None`。 请注意，此设置不能保证在执行过程中设置的线程数。 此设置允许 Python 将线程数扩展到指定的值。 此设置仅适用于 Python 函数应用。 此外，此设置还适用于同步函数调用，而不适用于协同例程。

|键|示例值|最大值|
|---|------------|---------|
|PYTHON\_THREADPOOL\_THREAD\_COUNT|2|32|

## <a name="scale_controller_logging_enabled"></a>SCALE\_CONTROLLER\_LOGGING\_ENABLED

_此设置当前处于预览状态。_

此设置控制 Azure Functions 缩放控制器中的日志记录。 有关详细信息，请参阅[缩放控制器日志](functions-monitoring.md#scale-controller-logs)。

|键|示例值|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights:Verbose|

此键的值以 `<DESTINATION>:<VERBOSITY>` 格式提供，其定义如下：

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="scm_logstream_timeout"></a>SCM\_LOGSTREAM\_TIMEOUT

控制连接到流式处理日志时的超时（秒）。 默认值为 7200（2 小时）。 

|键|示例值|
|-|-|
|SCM_LOGSTREAM_TIMEOUT|1800|

上述示例值 `1800` 设置超时 30 分钟。 有关详细信息，请参阅[启用流式处理日志](functions-run-local.md#enable-streaming-logs)。

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

存储帐户的连接字符串，该帐户的函数应用代码和配置存储在 Windows 上运行的事件驱动缩放计划中。 有关详细信息，请参阅[创建函数应用](functions-infrastructure-as-code.md#windows)。

|键|示例值|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

仅在部署到 Windows 运行上的高级计划或消耗计划时使用。 不支持运行 Linux 的消耗计划。 更改或删除此设置可能会导致函数应用无法启动。 若要了解详细信息，请参阅[此故障排除文章](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)。

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

将存储帐户限制在虚拟网络中时，值 `1` 允许函数应用进行缩放。 将存储帐户限制于一个虚拟网络时，应启用此设置。 若要了解详细信息，请参阅[将存储帐户限制在虚拟网络中](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)。

|键|示例值|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

在运行 Windows 的[高级计划](functions-premium-plan.md)和[专用（应用服务）计划](dedicated-plan.md)（标准版及更高版本）上受支持。 当前不支持运行 Linux 的消耗计划和高级计划。 

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Windows 上事件驱动的缩放计划中函数应用代码和配置的文件路径。 与 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING 结合使用。 默认值是以函数应用名称开头的唯一字符串。 请参阅[创建函数应用](functions-infrastructure-as-code.md#windows)。

|键|示例值|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

仅在部署到 Windows 运行上的高级计划或消耗计划时使用。 不支持运行 Linux 的消耗计划。 更改或删除此设置可能会导致函数应用无法启动。 若要了解详细信息，请参阅[此故障排除文章](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)。

在部署期间使用 Azure 资源管理器模板创建函数应用时，请勿在该模板中包括 WEBSITE_CONTENTSHARE。 此应用程序设置是在部署过程中生成的。 若要了解详细信息，请参阅[为函数应用自动执行资源部署](functions-infrastructure-as-code.md#windows)。

## <a name="website_dns_server"></a>WEBSITE\_DNS\_SERVER

设置应用在解析 IP 地址时使用的 DNS 服务器。 使用某些网络功能（如 [Azure DNS 专用区域](functions-networking-options.md#azure-dns-private-zones)和[专用终结点](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)）时，通常需要此设置。

|键|示例值|
|---|------------|
|WEBSITE\_DNS\_SERVER|168.63.129.16|

## <a name="website_enable_brotli_encoding"></a>WEBSITE\_ENABLE\_BROTLI\_ENCODING

控制是否将 Brotli 编码用于压缩，代替默认的 gzip 压缩。 当 `WEBSITE_ENABLE_BROTLI_ENCODING` 设置为 `1` 时，就会使用 Brotli 编码，否则使用 gzip 编码。

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

应用可以横向扩展到的最大实例数。 默认值为无限制。

> [!IMPORTANT]
> 此设置处于预览状态。  添加了一个[函数应用横向扩展上限属性](./event-driven-scaling.md#limit-scale-out)，建议使用此方法限制横向扩展。

|键|示例值|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_仅限 Windows_。
设置在 Windows 上运行函数应用时要使用的 Node.js 版本。 应使用波形符 (~) 让运行时使用目标主版本的最新可用版本。 例如，当设置为 `~10` 时，将使用最新版本 Node.js 10。 当目标主版本带有波形符时，无需手动更新次版本。

|键|示例值|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

让函数应用从已装载的包文件运行。

|键|示例值|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

有效值是解析为部署包文件位置的 URL 或 `1`。 设置为 `1` 时，包必须位于 `d:\home\data\SitePackages` 文件夹中。 使用此设置的 zip 部署时，包将自动上传到此位置。 在预览版中，此设置名为 `WEBSITE_RUN_FROM_ZIP`。 有关详细信息，请参阅[从包文件运行函数](run-functions-from-deployment-package.md)。

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

允许为函数应用设置时区。

|键|(OS)|示例值|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|东部标准时间|
|WEBSITE\_TIME\_ZONE|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>WEBSITE\_VNET\_ROUTE\_ALL

指示是否通过虚拟网络路由应用的所有出站流量。 设置值 `1` 指示所有流量都通过虚拟网络进行路由。 使用[区域虚拟网络集成](functions-networking-options.md#regional-virtual-network-integration)功能时，需要使用此设置。 [使用虚拟网络 NAT 网关定义静态出站 IP 地址](functions-how-to-use-nat-gateway.md)时，也可以使用此设置。

|键|示例值|
|---|------------|
|WEBSITE\_VNET\_ROUTE\_ALL|1|

## <a name="next-steps"></a>后续步骤

[了解如何更新应用设置](functions-how-to-use-azure-function-app-settings.md#settings)

[查看 host.json 文件中的全局设置](functions-host-json.md)

[查看应用服务应用的其他应用设置](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
