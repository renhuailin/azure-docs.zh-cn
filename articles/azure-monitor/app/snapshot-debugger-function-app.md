---
title: 启用 Azure Functions 中的 .NET 和 .NET Core 应用的 Snapshot Debugger |Microsoft Docs
description: 启用 Azure Functions 中的 .NET 和 .NET Core 应用的 Snapshot Debugger
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: d86455eae0834f29099c7d5c96f8326408daf519
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675523"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>启用 Azure Functions 中的 .NET 和 .NET Core 应用的 Snapshot Debugger

Snapshot Debugger 当前适用于在 Windows 服务计划的 Azure Functions 上运行的 ASP.NET 和 ASP.NET Core 应用。

建议你在使用 Snapshot Debugger 时，使用基本服务层或更高级别运行应用程序。

对于大多数应用程序，免费和共享服务层没有足够的内存或磁盘空间来保存快照。

## <a name="prerequisites"></a>先决条件

* [启用 Function App 中的 Application Insights 监视](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>启用快照调试器

如果运行的是其他类型的 Azure 服务，请参阅以下说明，了解如何在其他受支持的平台上启用 Snapshot Debugger：
* [Azure 应用服务](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

若要在 Function app 中启用 Snapshot Debugger，必须 `host.json` 通过添加下面定义的属性来更新文件， `snapshotConfiguration` 然后重新部署函数。

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

Snapshot Debugger 作为 Azure Functions 运行时的一部分预安装，这在默认情况下处于禁用状态。

由于 Snapshot Debugger 包含在 Azure Functions 运行时中，因此不需要添加额外的 NuGet 包或应用程序设置。

就像引用一样，对于简单的函数应用 ( .NET Core) ，下面是它在 `.csproj` `{Your}Function.cs` `host.json` Snapshot Debugger 上启用、和后的外观。

项目 .csproj

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

## <a name="disable-snapshot-debugger"></a>禁用快照调试器

若要在 Function app 中禁用 Snapshot Debugger，只需 `host.json` 通过将设置为属性来更新文件 `false` `snapshotConfiguration.isEnabled` 。

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

建议你在所有应用上都启用了 Snapshot Debugger，以方便诊断应用程序异常。

## <a name="next-steps"></a>后续步骤

- 为应用程序生成可触发异常的流量。 然后等待 10 到 15 分钟，这样快照就会发送到 Application Insights 实例。
- [查看](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) Azure 门户中的快照。
- 基于函数应用上的用例自定义 Snapshot Debugger 配置。 有关详细信息，请参阅 [中 host.js的快照配置](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration)。
- 排查 Snapshot Debugger 问题时如需帮助，请参阅 [ Snapshot Debugger 故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。