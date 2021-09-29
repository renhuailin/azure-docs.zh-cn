---
title: Azure 开发测试实验室在多个实验室和订阅中的使用情况
description: 了解如何报告 Azure 开发测试实验室在多个实验室和订阅中的使用情况。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 671941e259bd1329dab3e30c1c95eb77ed3091f9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642826"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>报告 Azure 开发测试实验室在多个实验室和订阅中的使用情况

大多数大型组织希望通过将使用过程中的趋势和离群值可视化，来跟踪资源使用情况，以便更有效地使用这些资源。 实验室所有者或经理可根据资源使用情况自定义实验室以[改善资源使用情况和成本](../cost-management-billing/cost-management-billing-overview.md)。 在 Azure 开发测试实验室中，你可以下载每个实验室的资源使用情况，以便从历史的角度更深入地了解使用模式。 可以通过这些使用模式准确了解相关变化以提高效率。 大多数企业既需要了解单个实验室的使用情况，又需要了解[多个实验室和订阅](/azure/architecture/cloud-adoption/decision-guides/subscriptions/)的总体使用情况。 

本文介绍如何跨多个实验室和订阅处理资源使用情况信息。

![报告使用情况](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>单个实验室使用情况

此部分介绍如何导出单个实验室的资源使用情况。

必须先设置 Azure 存储帐户，以便存储不同的包含使用情况数据的文件，然后才能导出开发测试实验室的资源使用情况。 可以通过两种常见方法来执行数据导出操作：

* [开发测试实验室 REST API](/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az.Resource 模块 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)，其中包含操作 `exportResourceUsage`、实验室资源 ID 和必需参数。 

    [导出或删除个人数据](personal-data-delete-export.md)一文有一个示例 PowerShell 脚本，其中包含已导出数据的详细信息。 

    > [!NOTE]
    > 日期参数不包含时间戳，因此数据包含从午夜开始的所有内容，具体取决于实验室所在的时区。

导出完成后，blob 存储中会有多个 CSV 文件，其中包含不同的资源信息。
  
当前有两个 CSV 文件：

* virtualmachines.csv - 包含实验室中虚拟机的信息
* disks.csv - 包含实验室中不同磁盘的信息 

这些文件存储在 labresourceusage blob 容器中，并标注了实验室名称、实验室唯一 ID、执行日期以及根据导出请求确定的完整日期或开始日期。 示例 blob 结构为：

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>为所有实验室导出使用情况

若要为多个实验室导出使用情况信息，请考虑使用： 

* [Azure Functions](../azure-functions/index.yml)，在多种语言（包括 PowerShell）中提供；或 
* [Azure 自动化 runbook](../automation/index.yml)，可使用 PowerShell、Python 或自定义图形设计器来编写导出代码。

使用这些技术，你可以在特定的日期和时间对所有实验室执行单个实验室导出。 

你的 Azure 函数应该将数据推送到长期存储。 导出多个实验室的数据时，导出可能需要一些时间。 为了提高性能和降低信息出现重复的可能性，建议以并行方式执行每个实验室。 若要以并行方式完成，请异步运行 Azure Functions。 还可利用 Azure Functions 提供的计时器触发器。

## <a name="using-a-long-term-storage"></a>使用长期存储

长期存储可将来自不同实验室的导出信息合并成单个数据源。 使用长期存储的另一个好处是，可以从存储帐户中删除这些文件，以减少重复内容并降低成本。 

长期存储可用于执行任何文本操作，例如： 

* 添加易记名称
* 创建复杂分组
* 聚合数据。

一些常见的存储解决方案包括：[SQL Server](https://azure.microsoft.com/services/sql-database/)、[Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/) 和 [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。 选择哪个长期存储解决方案取决于个人偏好。 你可以考虑根据工具在可视化数据时在交互可用性方面的情况来选择工具。

## <a name="visualizing-data-and-gathering-insights"></a>可视化数据和收集见解

使用所选的数据可视化工具连接到长期存储，以显示使用情况数据并收集见解以验证使用效率。 例如，可以使用 [Power BI](/power-bi/power-bi-overview) 来组织和显示使用情况数据。 

可以使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)在单位置接口中创建、链接和管理资源。 如果需要更多控制，则可在单个资源组中创建单个资源，并独立于数据工厂服务对该资源进行管理。  

## <a name="next-steps"></a>后续步骤

设置系统并将数据转移到长期存储后，下一步就是提出需要数据回答的问题。 例如： 

-   什么是 VM 大小使用情况？

    用户是否选择性能高的（更昂贵的）VM 大小？
-   正在使用哪些市场映像？

    自定义映像是否是最常见的 VM 基础映像？是否应生成通用映像存储（例如[共享映像库](../virtual-machines/shared-image-galleries.md)或[映像工厂](image-factory-create.md)）。
-   目前在使用或未使用哪些自定义映像？