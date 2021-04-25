---
title: 将日志上传到 Azure Monitor
description: 将已启用 Azure Arc 的数据服务的日志上传到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92375217"
---
# <a name="upload-logs-to-azure-monitor"></a>将日志上传到 Azure Monitor

可以定期导出日志，然后将其上传到 Azure。 导出和上传数据也会在 Azure 中创建和更新数据控制器、SQL 托管实例及超大规模 PostgreSQL 服务器组资源。

> [!NOTE] 
> 在预览期，使用已启用 Azure Arc 的数据服务不会产生费用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>开始之前

在上传日志之前，需要执行以下操作： 

1. [创建 Log Analytics 工作区](#create-a-log-analytics-workspace)
1. [将 ID 和共享密钥分配到环境变量](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

若要创建 Log Analytics 工作区，请执行这些命令以创建 Log Analytics 工作区，并将访问信息设置到环境变量中。

> [!NOTE]
> 如果你已有工作区，请跳过此步骤。

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

示例输出：

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>将 ID 和共享密钥分配到环境变量

将 Log Analytics 工作区 `customerId` 保存为环境变量，供稍后使用：

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

此命令返回连接到 Log Analytics 工作区所需的访问密钥：

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

示例输出：

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

在环境变量中保存主密钥，供稍后使用：

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>设置最终环境变量并确认

在环境变量中设置 SPN 机构 URL：

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>验证环境变量

如果需要，请检查以确保设置了所有必需的环境变量：


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

设置环境变量后，可将日志上传到日志工作区。 

## <a name="upload-logs-to-azure-monitor"></a>将日志上传到 Azure Monitor

 若要上传已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的日志，请运行以下 CLI 命令 -

1. 使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 登录到 Azure Arc 数据控制器。

   ```console
   azdata login
   ```

   按照提示设置命名空间、管理员用户名和密码。 

1. 将所有日志导出到指定文件：

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. 将日志上传到 Azure Monitor Log Analytics 工作区：

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>在 Azure 门户中查看日志

上传日志后，你应该能够使用日志查询资源管理器对其进行查询，如下所示：

1. 打开 Azure 门户，在顶部搜索栏中按名称搜索你的工作区，然后选择该工作区。
2. 在左侧面板中选择“日志”。
3. 选择“开始”（如果你不熟悉 Log Analytics，可以选择“入门”页上的链接来详细了解它）。
4. 如果你是第一次使用 Log Analytics，请遵循教程详细了解 Log Analytics。
5. 展开表列表底部的“自定义日志”，你将看到一个名为“sql_instance_logs_CL”的表。
6. 选择表名称旁边的“眼睛”图标。
7. 选择“在查询编辑器中查看”按钮。
8. 查询编辑器中现在有一个查询，它会显示日志中最近的 10 个事件。
9. 在这里，可以使用查询编辑器、设置警报等来尝试查询日志。

## <a name="automating-uploads-optional"></a>自动上传（可选）

如果你要按计划上传指标和日志，可以创建脚本，并每隔几分钟按照计时器运行该脚本。 下面是使用 Linux shell 脚本自动执行上传的示例。

在你常用的文本/代码编辑器中，将以下脚本添加到文件，并将该文件另存为脚本可执行文件，如 .sh (Linux/Mac) 或 .cmd、.bat、.ps1。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

使脚本文件成为可执行文件

```console
chmod +x myuploadscript.sh
```

每隔 20 分钟运行该脚本：

```console
watch -n 1200 ./myuploadscript.sh
```

还可以使用 cron 或 Windows 任务计划程序等作业计划程序或者 Ansible、Puppet 或 Chef 等业务流程协调程序。

## <a name="next-steps"></a>后续步骤

[将指标和日志上传到 Azure Monitor](upload-metrics.md)

[将使用情况数据、指标和日志上传到 Azure Monitor](upload-usage-data.md)

[将账单数据上传到 Azure 并在 Azure 门户中查看该数据](view-billing-data-in-azure.md)

[在 Azure 门户中查看 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
