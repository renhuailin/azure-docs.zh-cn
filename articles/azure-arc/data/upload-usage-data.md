---
title: 将使用情况数据上传到 Azure
description: 将已启用 Azure Arc 的数据服务的使用情况数据上传到 Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 74df592db61e4c9c50f9b199d7803fb8e1481878
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732157"
---
# <a name="upload-usage-data-to-azure"></a>将使用情况数据上传到 Azure

用户可以定期导出使用情况信息。 此信息的导出和上传会在 Azure 中创建并更新数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组资源。

> [!NOTE] 
> 在预览版期间，使用已启用 Azure Arc 的数据服务不会产生费用。



> [!NOTE]
> 创建 Azure Arc 数据控制器后至少等待 24 小时，才能上传使用情况数据。

## <a name="create-service-principal-and-assign-roles"></a>创建服务主体并分配角色

在继续操作之前，请确保已创建所需的服务主体，并已将其分配给适当的角色。 有关详细信息，请参阅：
* [创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [将角色分配给服务主体](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>上传使用数据

可采用以下两步骤方式将使用情况信息（如库存和资源使用情况）上传到 Azure：

1. 使用 `az arcdata dc export` 命令导出使用情况数据，如下所示：

> [!NOTE]
> 使用命令 `az arcdata dc export` 导出使用情况/计费信息、指标和日志要求暂时绕过 SSL 验证。  系统将提示你绕过 SSL 验证，或者你可以设置 `AZDATA_VERIFY_SSL=no` 环境变量以避免出现提示。  目前无法为数据控制器导出 API 配置 SSL 证书。

   ```azurecli
   az arcdata dc export --type usage --path usage.json --k8s-namespace <namespace> --use-k8s
   ```
 
   此命令创建一个 `usage.json` 文件，其中包含在数据控制器上创建的所有已启用 Azure Arc 的数据资源，例如 SQL 托管实例和超大规模 PostgreSQL 实例等。

2. 使用 `upload` 命令上传使用情况数据。

   ```azurecli
   az arcdata dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>自动上传（可选）

如果你要按计划上传指标和日志，可以创建脚本，并每隔几分钟按照计时器运行该脚本。 下面是使用 Linux shell 脚本自动执行上传的示例。

在常用文本/代码编辑器中，将以下脚本添加到该文件中，并将其另存为脚本可执行文件，如 `.sh` (Linux/Mac) 或 `.cmd`、`.bat` 或 `.ps1`。

```azurecli
az arcdata dc export --type usage --path usage.json --force --k8s-namespace <namespace> --use-k8s
az arcdata dc upload --path usage.json
```

使脚本文件可执行。

```console
chmod +x myuploadscript.sh
```

每天运行脚本以获取使用情况：

```console
watch -n 1200 ./myuploadscript.sh
```

还可以使用 cron 或 Windows 任务计划程序等作业计划程序或者 Ansible、Puppet 或 Chef 等业务流程协调程序。

## <a name="next-steps"></a>后续步骤

[将指标和日志上传到 Azure Monitor](upload-metrics.md)

[将日志上传到 Azure Monitor](upload-logs.md)

[将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md)

[在 Azure 门户中查看 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
