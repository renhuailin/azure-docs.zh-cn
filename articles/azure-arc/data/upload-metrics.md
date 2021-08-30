---
title: 将指标上传到 Azure Monitor
description: 将已启用 Azure Arc 的数据服务指标上传到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 56eed522dc28b29f24e97a94a03e848d5cf58898
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725667"
---
# <a name="upload-metrics-to-azure-monitor"></a>将指标上传到 Azure Monitor

用户可以定期导出监视指标，然后将这些指标上传到 Azure。 数据的导出和上传也会在 Azure 中创建并更新数据控制器、SQL 托管实例和超大规模 PostgreSQL 服务器组资源。


## <a name="prerequisites"></a>先决条件

在继续操作之前，请确保已创建所需的服务主体，并已将该主体分配到相应的角色。 有关详细信息，请参阅：
* [创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [将角色分配到服务主体](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>上传指标

利用 Azure Arc 数据服务，用户可以选择将指标上传到 Azure Monitor，以便可以聚合和分析指标，引发警报，发送通知，或触发自动操作。 

通过将数据发送到 Azure Monitor，还可以用非现场和大规模的方式存储指标数据，使数据能够长期存储，以便进行高级分析。

如果有多个具有 Azure Arc 数据服务的站点，可以使用 Azure Monitor 作为中心位置来收集各站点中的所有日志和指标。

## <a name="set-final-environment-variables-and-confirm"></a>设置最终环境变量并确认

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

如果需要，请检查以确保设置了所有必需的环境变量：


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

## <a name="upload-metrics-to-azure-monitor"></a>将指标上传到 Azure Monitor

如果要上传已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的指标，请运行以下 CLI 命令：

1. 使用 `azdata` 登录到数据控制器。
 
1. 将所有指标导出到指定文件：

> [!NOTE]
> 使用命令 `az arcdata dc export` 导出使用情况/计费信息、指标和日志时要求暂时绕过 SSL 验证。  系统将提示你绕过 SSL 验证，你也可设置 `AZDATA_VERIFY_SSL=no` 环境变量来避免出现提示。  目前无法为数据控制器导出 API 配置 SSL 证书。

   ```azurecli
   az arcdata dc export --type metrics --path metrics.json
   ```

2. 将指标上传到 Azure Monitor：

   ```azurecli
   az arcdata dc upload --path metrics.json
   ```

   >[!NOTE]
   >在为第一次上传创建已启用 Azure Arc 的数据实例后，请等待至少 30 分钟。
   >
   >请确保在 `export` 之后立即 `upload` 指标，因为 Azure Monitor 只接受最近 30 分钟的指标。 [了解详细信息](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)。


如果在导出过程中看到任何指示“无法获取指标”的错误，请通过运行以下命令来检查数据收集是否设置为 `true`：

```azurecli
az arcdata dc config show
```

在“安全部分”下查找

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

验证 `allowNodeMetricsCollection` 和 `allowPodMetricsCollection` 属性是否设置为 `true`。

## <a name="view-the-metrics-in-the-portal"></a>在门户中查看指标

在上传指标后，可以从 Azure 门户中看到这些指标。
> [!NOTE]
> 请注意，处理上传的数据可能需要几分钟时间，然后才能在门户中查看这些指标。


如果要在门户中查看指标，请使用此链接来打开门户：<https://portal.azure.com>。然后，在搜索栏中按名称搜索数据库实例：

可以在“概述”页上查看 CPU 利用率，或者，如果需要更详细的指标，可以单击左侧导航面板中的指标

选择 sql server 作为指标命名空间：

选择要可视化的指标（也可以选择多个）：

将频率更改为最近 30 分钟：

> [!NOTE]
> 只能上传最近 30 分钟的指标。 Azure Monitor 拒绝 30 分钟以前的指标。

## <a name="automating-uploads-optional"></a>自动上传（可选）

如果你要按计划上传指标和日志，可以创建脚本，并每隔几分钟按照计时器运行该脚本。 下面是使用 Linux shell 脚本自动执行上传的示例。

在常用的文本/代码编辑器中，将以下脚本添加到文件，并将该文件另存为脚本可执行文件，如 .sh (Linux/Mac) 或 .cmd、.bat、.ps1。

```azurecli
az arcdata dc export --type metrics --path metrics.json --force
az arcdata dc upload --path metrics.json
```

使脚本文件成为可执行文件

```console
chmod +x myuploadscript.sh
```

每隔 20 分钟运行该脚本：

```console
watch -n 1200 ./myuploadscript.sh
```

还可以使用 cron 或 Windows 任务计划程序等作业计划程序或 Ansible、Puppet 或 Chef 等业务流程协调程序。

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>有关导出和上传使用情况和指标的一般原则

针对已启用 Azure Arc 的数据服务的创建、读取、更新和删除 (CRUD) 操作会被记录到日志，以用于计费和监视。 有后台服务会监视这些 CRUD 操作并相应计算消耗。 实际的使用情况或消耗的计算会按计划进行，并且在后台完成。 

每日仅上传一次使用情况数据。 如果在同一个 24 小时内多次导出并上传使用情况信息，则只会更新 Azure 门户中的资源清单，而不会更新资源使用情况。

对于上传指标，Azure Monitor 只接受最近 30 分钟的数据（[了解详细信息](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)）。 上传指标的原则是要在创建导出文件后立即上传指标，这样就可以在 Azure 门户中看到整个数据集。 例如，如果在下午 2:00 导出指标，然后在下午 2:50 运行上传命令。 由于 Azure Monitor 只接受最近 30 分钟的数据，因此在门户中可能看不到任何数据。 

## <a name="next-steps"></a>后续步骤

[将日志上传到 Azure Monitor](upload-logs.md)

[将使用情况数据、指标和日志上传到 Azure Monitor](upload-usage-data.md)

[将计费数据上传到 Azure 并在 Azure 门户中查看该数据](view-billing-data-in-azure.md)

[在 Azure 门户中查看 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
