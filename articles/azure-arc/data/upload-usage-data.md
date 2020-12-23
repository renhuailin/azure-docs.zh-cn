---
title: 将使用情况数据上传到 Azure Monitor
description: 将使用情况 Azure Arc 启用的数据服务数据上传到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375212"
---
# <a name="upload-usage-data-to-azure-monitor"></a>将使用情况数据上传到 Azure Monitor

你可以定期导出使用情况信息。 此信息的导出和上载将在 Azure 中创建和更新数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组资源。

> [!NOTE] 
> 在预览期间，使用启用了 Azure Arc 的数据服务不会产生费用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> 在上载使用情况数据之前，请在创建 Azure Arc 数据控制器之前等待至少24小时。

## <a name="create-service-principal-and-assign-roles"></a>创建服务主体并分配角色

在继续操作之前，请确保已创建了所需的服务主体，并已将其分配到适当的角色。 有关详细信息，请参阅：
* [创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [向服务主体分配角色](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>上载使用情况数据

可以通过以下两个步骤将使用情况信息（如清单和资源使用情况）上传到 Azure：

1. 登录到数据控制器。 在提示符下输入这些值。 

   ```console
   azdata login
   ```

1. 使用命令导出使用情况数据 `azdata arc dc export` ，如下所示：

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   此命令创建一个 `usage.json` 文件，其中包含所有启用了 Azure Arc 的数据资源，如 SQL 托管实例和在数据控制器上创建的 PostgreSQL 超大规模实例等。

2. 使用命令上载使用情况数据 ```azdata upload```

   ```console
   azdata arc dc upload --path usage.json
   ```

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

## <a name="next-steps"></a>后续步骤

[将指标和日志上传到 Azure Monitor](upload-metrics.md)

[将日志上传到 Azure Monitor](upload-logs.md)

[将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md)

[查看 Azure 门户中的 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
