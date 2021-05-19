---
title: 在 Azure Functions 中为 .NET 和 .NET Core 应用启用 Snapshot Debugger | Microsoft Docs
description: 在 Azure Functions 中为 .NET 和 .NET Core 应用启用 Snapshot Debugger
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 48eb3cf81384446a07fea69572ac16e0b80cee38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025622"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>在 Azure Functions 中为 .NET 和 .NET Core 应用启用 Snapshot Debugger

Snapshot Debugger 当前适用于按 Windows 服务计划在 Azure Functions 上运行的 ASP.NET 和 ASP.NET Core 应用。

建议在使用 Snapshot Debugger 时在基本服务层或更高版本上运行应用程序。

对于大多数应用程序，“免费”和“共享”服务层没有足够的内存或磁盘空间来保存快照。

## <a name="prerequisites"></a>先决条件

* [在函数应用中启用 Application Insights 监视](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>启用快照调试器

如果你在运行另一种类型的 Azure 服务，则下面提供了用于在其他受支持平台上启用 Snapshot Debugger 的说明：
* [Azure 应用服务](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

若要在函数应用中启用 Snapshot Debugger，必须按下面定义的添加属性 `snapshotConfiguration` 来更新 `host.json` 文件，并重新部署函数。

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger 作为 Azure Functions 运行时的一部分预安装，默认情况下处于禁用状态。

由于 Snapshot Debugger 包含在 Azure Functions 运行时中，因此不需要添加额外的 NuGet 包或应用程序设置。

仅作为参考，对于简单的函数应用 (.NET Core)，下面是启用 Snapshot Debugger 后 `.csproj`、`{Your}Function.cs` 和 `host.json` 的外观。

项目 csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function 类

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

主机文件

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>为其他云启用 Snapshot Debugger

目前唯一需要修改终结点的区域是 [Azure 政府](../../azure-government/compare-azure-government-global-azure.md#application-insights)和 [Azure 中国](/azure/china/resources-developer-guide)。

下面是使用美国政府云代理终结点更新 `host.json` 的示例：
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

下面是支持的 Snapshot Debugger 代理终结点的替代项：

|属性    | 美国政府云 | 中国云 |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>禁用快照调试器

若要在函数应用中禁用 Snapshot Debugger，只需将属性 `snapshotConfiguration.isEnabled` 设置为 `false` 来更新 `host.json` 文件。

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

建议在所有应用上启用 Snapshot Debugger，以简化对应用程序异常的诊断。

## <a name="next-steps"></a>后续步骤

- 为应用程序生成可触发异常的流量。 然后等待 10 到 15 分钟，这样快照就会发送到 Application Insights 实例。
- 在 Azure 门户中[查看快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 根据函数应用上的用例自定义 Snapshot Debugger 配置。 有关详细信息，请参阅 [host.json 中的快照配置](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration)。
- 排查 Snapshot Debugger 问题时如需帮助，请参阅 [ Snapshot Debugger 故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。