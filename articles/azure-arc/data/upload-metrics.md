---
title: 将指标上载到 Azure Monitor
description: 将启用了 Azure Arc 的数据服务指标上载到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: d7c611f1cdb5e3294e38f87c0534003813e50388
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575687"
---
# <a name="upload-metrics-to-azure-monitor"></a>将指标上载到 Azure Monitor

你可以定期导出监视指标，并将其上传到 Azure。 数据的导出和上传还会在 Azure 中创建和更新数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组资源。

> [!NOTE] 
> 在预览期间，使用启用了 Azure Arc 的数据服务不会产生费用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

在继续操作之前，请确保已创建了所需的服务主体，并已将其分配到适当的角色。 有关详细信息，请参阅：
* [创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [向服务主体分配角色](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>上传指标

利用 Azure Arc 数据服务，您可以选择将指标上载到 Azure Monitor 以便聚合和分析指标，引发警报，发送通知，或触发自动操作。 

通过将你的数据发送到 Azure Monitor，你还可以将指标数据存储在异地并大规模地存储，从而为高级分析实现数据的长期存储。

如果有多个具有 Azure Arc 数据服务的站点，则可以使用 Azure Monitor 作为中心位置，收集整个站点中的所有日志和指标。

## <a name="set-final-environment-variables-and-confirm"></a>设置最终环境变量和确认

在环境变量中设置 SPN 授权 URL：

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

如果需要，请检查以确保设置所需的所有环境变量：


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>将指标上载到 Azure Monitor

若要上载启用了 Azure arc 的 SQL 托管实例和启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的指标，请运行以下 CLI 命令：

1. 用登录到数据控制器 `azdata` 。
 
1. 将所有指标导出到指定的文件：

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. 将指标上传到 Azure monitor：

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >为第一次上载创建 Azure Arc 启用的数据实例后，请等待至少30分钟。
   >
   >请确保 `upload` 此后的指标 `export` Azure Monitor 仅接受过去30分钟的指标。 [了解详细信息](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)。


如果在导出过程中看到指示 "无法获取指标" 的任何错误，请 `true` 通过运行以下命令来检查数据收集是否设置为：

```console
azdata arc dc config show
```

在 "安全" 部分下查找

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

验证 `allowNodeMetricsCollection` 和属性是否 `allowPodMetricsCollection` 设置为 `true` 。

## <a name="view-the-metrics-in-the-portal"></a>在门户中查看指标

上传度量值后，可以从 Azure 门户查看它们。
> [!NOTE]
> 请注意，可能需要几分钟时间来处理上载的数据，然后才能在门户中查看度量值。


若要在门户中查看度量值，请使用此链接打开门户： <https://portal.azure.com> 然后在搜索栏中按名称搜索数据库实例：

你可以在 "概述" 页上查看 CPU 利用率，或者如果想要更详细的指标，可以单击左侧导航面板中的 "指标"

选择 sql server 作为指标命名空间：

选择要可视化的指标 (也可以选择多个) ：

将频率更改为最近30分钟：

> [!NOTE]
> 只能上载过去30分钟的指标。 Azure Monitor 拒绝超过30分钟的指标。

## <a name="automating-uploads-optional"></a>自动上载 (可选) 

如果要按计划上载指标和日志，可以创建一个脚本，并每隔几分钟在计时器上运行它。 下面是使用 Linux shell 脚本自动执行上载的示例。

在常用文本/代码编辑器中，将以下脚本添加到该文件中，并将其另存为脚本可执行文件，例如 (Linux/Mac) 或 .cmd，.bat，. ps1。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

使脚本文件可执行

```console
chmod +x myuploadscript.sh
```

每隔20分钟运行一次脚本：

```console
watch -n 1200 ./myuploadscript.sh
```

你还可以使用诸如 cron 或 Windows 任务计划程序的作业计划程序或 Ansible、Puppet 或 Chef 等 orchestrator。

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>有关导出和上载使用情况的一般指南，指标

在启用了 Azure Arc 的数据服务上创建、读取、更新和删除 (CRUD) 操作，以便进行计费和监视。 存在监视这些 CRUD 操作并相应计算消耗的后台服务。 实际使用情况或消耗计算按计划进行，并在后台完成。 

在预览期间，此过程在夜间发生。 一般原则是每天仅上载一次使用。 当在同一个24小时内将使用情况信息导出并上传多次时，只 Azure 门户中的资源清单进行更新，而不会更新资源使用情况。

对于上传指标，Azure monitor 只接受过去30分钟的数据 ([了解更多](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)) 。 上传指标的指导是在创建导出文件后立即上载指标，以便查看 Azure 门户中的整个数据集。 例如，如果在 2:00 PM 导出指标，并在 2:50 PM 运行上传命令。 由于 Azure Monitor 仅接受过去30分钟的数据，因此你可能在门户中看不到任何数据。 

## <a name="next-steps"></a>后续步骤

[将日志上传到 Azure Monitor](upload-logs.md)

[将使用情况数据、指标和日志上传到 Azure Monitor](upload-usage-data.md)

[将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md)

[查看 Azure 门户中的 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
