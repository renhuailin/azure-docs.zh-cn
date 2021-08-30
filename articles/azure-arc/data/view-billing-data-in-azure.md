---
title: 将计费数据上传到 Azure 并在 Azure 门户中查看该数据
description: 将计费数据上传到 Azure 并在 Azure 门户中查看该数据
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 2c4e25aebf46ea13b69b8ca24d1336c4ba5521ad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751490"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>将计费数据上传到 Azure 并在 Azure 门户中查看该数据

> [!IMPORTANT] 
>  预览期间使用已启用 Azure Arc 的数据服务不会产生费用。 尽管计费系统端到端工作，但计费计量器设置为 0 美元。  如果遵循此方案，用户将在计费中看到当前名为“混合数据服务”的服务条目和“Microsoft.AzureArcData/”类型的资源条目 **`<resource type>`** 。 用户将能够看到创建的每个数据服务 - Azure Arc 的记录，但每个记录将收取 0 美元。


## <a name="connectivity-modes---implications-for-billing-data"></a>连接模式 - 计费数据的含意

将来，可以通过两种模式运行已启用 Azure Arc 的数据服务：

- **间接连接** - 不会直接连接到 Azure。 数据仅通过导出/上传过程发送到 Azure。
- 直接连接 - 在此模式下，将依赖于已启用 Azure Arc 的 Kubernetes 服务，在 Azure 与已启用 Azure Arc 的数据服务运行的 Kubernetes 群集之间提供直接连接。 这可以实现更多功能，还可让用户使用 Azure 门户和 Azure CLI 管理已启用 Azure Arc 的数据服务，就像在 Azure PaaS 中管理数据服务一样。  预览版目前尚不提供此连接模式，但即将推出。

可以阅读有关[连接模式](./connectivity.md)之间差异的详细信息。

在间接连接模式下，计费数据会定期从 Azure Arc 数据控制器导出到安全文件，然后上传到 Azure 并进行处理。  在即将推出的直接连接模式下，计费数据会大约每小时自动发送到 Azure 1 次，以便准实时查看服务成本。 在间接连接模式下，也可以使用脚本自动进行导出和上传数据的过程，或者我们可以构建一个为用户执行此操作的服务。

## <a name="upload-billing-data-to-azure"></a>将计费数据上传到 Azure

如果要将计费数据上传到 Azure，应首先执行以下操作：

1. 如果还没有已启用 Azure Arc 的数据服务，请先创建。 例如，创建以下服务之一：
   - [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)
   - [创建已启用 Azure Arc 的 PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)
1. 如果尚未将资源清单、使用情况数据、指标和日志上传到 Azure Monitor，请[上传](upload-metrics-and-logs-to-azure-monitor.md)。
1. 创建数据服务之后至少等待 2 小时，以便计费遥测收集过程收集一些计费数据。

运行以下命令以导出计费数据：

```azurecli
az arcdata dc export -t usage -p usage.json --k8s-namespace <namespace> --use-k8s
```

目前，文件未加密以便查看内容。 可以随意在文本编辑器中打开，并查看内容的呈现情况。

你会注意到有两个数据集：`resources` 和 `data`。 `resources` 是数据控制器、PostgreSQL 超大规模服务器组和 SQL 托管实例。 数据中的 `resources` 记录捕获资源历史记录中的相关事件 - 创建时间、更新时间以及删除时间。 `data` 记录捕获给定实例每小时可使用的核心数。

`resource` 条目的示例：

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

`data` 条目的示例：

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

运行以下命令，将 usage.json 文件上传到 Azure：

```azurecli
az arcdata dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>在 Azure 门户中查看计费数据

请按照以下步骤在 Azure 门户中查看计费数据：

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理”中屏幕类型顶部的搜索框中，单击“成本管理”服务。
1. 在“成本管理概述”下，单击“成本管理”选项卡。 
1. 单击左侧的“成本分析”选项卡。
1. 单击视图顶部的“按资源划分的成本”按钮。
1. 确保“范围”设置为在其中创建了数据服务资源的订阅。
1. 在视图顶部附近的“范围”选择器旁的“视图”下拉菜单中选择“按资源划分的成本”。
1. 请确保日期筛选器设置为“本月”，或设置为鉴于创建数据服务资源的时间确定的其他一些合理时间范围。
1. 如果要筛选出仅一种类型的已启用 Azure Arc 的数据服务，请单击“添加筛选器”以按“资源类型”  = `Microsoft.AzureArcData/<data service type>`添加筛选器。
1. 现在，你将看到已创建并上传到 Azure 的所有资源的列表。 由于计费计量器为 0 美元，成本始终为 0 美元。

## <a name="download-billing-data"></a>下载计费数据

你可以直接从 Azure 门户下载计费摘要数据。

1. 在按照上述说明转到的同一“成本分析 -> 按资源类型查看”视图中，单击顶部附近的“下载”按钮。
1. 选择下载文件类型 - Excel 或 CSV，并单击“下载数据”按钮。
1. 根据所选的文件类型，在适当的编辑器中打开文件。

## <a name="export-billing-data"></a>导出计费数据

用户还可以创建计费导出作业，定期自动将“详细的”使用情况和计费数据导出到 Azure 存储容器。 如果用户想要查看计费的详细信息，例如计费周期内某个给定实例计费了几个小时，此功能会很有用。

请按照以下步骤设置计费导出作业：

1. 单击左侧的“导出”。
1. 单击“添加” 。
1. 输入名称和导出频率，并单击“下一步”。
1. 选择创建新的存储帐户或使用现有的存储帐户，并填写表单以指定要将计费数据文件导出到的存储帐户、容器和目录路径，然后单击“下一步”。
1. 单击“创建”。

计费数据导出文件将在大约 4 小时后可用，并将按照创建计费导出作业时指定的计划导出。

按照以下步骤查看已导出的计费数据文件：

可以在 Azure 门户中验证计费数据文件。 

> [!IMPORTANT]
> 创建计费导出作业后，请等待 4 小时，然后继续执行以下步骤。

1. 在门户顶部的“搜索”框中，输入“存储帐户”，并单击“存储帐户”。 
3. 单击创建上述计费导出作业时指定的存储帐户。
4. 单击左侧的“容器”。
5. 单击创建上述计费导出作业时指定的容器。
6. 单击创建上述计费导出作业时指定的文件夹。
7. 向下钻取已生成的文件夹和文件，然后单击其中一个生成的 .csv 文件。
8. 单击“下载”按钮，该按钮会将文件保存到本地“下载”文件夹。
9. 使用 .csv 文件查看器（例如 Excel）打开该文件。
10. 筛选结果以仅显示有“资源类型” = `Microsoft.AzureArcData/<data service resource type`的行。
11. 你会在 UsageQuantity 列中看到当前 24 小时内使用实例的小时数。
