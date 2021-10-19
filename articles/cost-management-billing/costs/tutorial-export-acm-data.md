---
title: 教程 - 从 Azure 成本管理创建和管理导出的数据
description: 本文介绍如何创建和管理导出的成本管理数据，以便在外部系统中使用。
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a08c6a6b00273e21c517162260f54ed3e6fc65ff
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711357"
---
# <a name="tutorial-create-and-manage-exported-data"></a>教程：创建和管理导出的数据

如果你已阅读成本分析教程，则会熟悉如何手动下载成本管理数据。 但是，可以创建一个定期任务，按日、按周或按月自动将成本管理数据导出到 Azure 存储。 导出的数据采用 CSV 格式，它包含成本管理收集的所有信息。 然后，可以在外部系统使用从 Azure 存储导出的数据，并将其与自己的自定义数据相结合。 在类似仪表板或其他财务系统等的外部系统中，你都可以使用导出的数据。

观看[如何使用成本管理计划到存储的导出](https://www.youtube.com/watch?v=rWa_xI1aRzo)视频，了解如何创建 Azure 成本数据到 Azure 存储的计划导出。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

本教程将通过示例，指导你导出成本管理数据，然后验证数据是否已成功导出。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建每日导出
> * 验证收集的数据

## <a name="prerequisites"></a>先决条件

数据导出适用于各种 Azure 帐户类型，包括[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 和 [Microsoft 客户协议](get-started-partners.md)客户。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 对于按用户和组导出的数据，每个订阅均支持以下 Azure 权限或作用域。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除计划导出。
- 参与者 - 可以创建、修改或删除自己的计划导出。 可以修改其他人创建的计划导出的名称。
- 读者 - 可以计划他们有权访问的导出。

有关范围的详细信息，包括为企业协议和 Microsoft 客户协议范围配置导出所需的访问权限，请参阅[了解和使用范围](understand-work-scopes.md)。

对于 Azure 存储帐户：
- 无论导出权限如何，更改配置的存储帐户都需要写入权限。
- 必须为 Blob 或文件存储配置 Azure 存储帐户。

如果你有新订阅，则无法立即使用成本管理功能。 最多可能需要 48 小时才能使用所有成本管理功能。

## <a name="sign-in-to-azure"></a>登录 Azure
在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。

## <a name="create-a-daily-export"></a>创建每日导出

### <a name="portal"></a>[门户](#tab/azure-portal)

要创建或查看数据导出或计划导出，请在 Azure 门户中选择范围，然后在菜单中选择“成本分析”。 例如，导航到“订阅”，从列表中选择订阅，然后在菜单中选择“成本分析” 。 在“成本分析”页的顶部，选择“设置”，然后“导出”。

> [!NOTE]
> - 除了订阅之外，还可以针对资源组、管理组、部门和注册创建导出。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。
> - 在计费帐户范围内或在客户的租户上以合作伙伴身份登录时，可以将数据导出到链接到合作伙伴存储帐户的 Azure 存储帐户。 但是，必须在 CSP 租户中具有活动订阅。

1. 选择“添加”，然后键入导出的名称。
1. 对于“指标”，请选择：
    - **实际成本（使用量和购买量）** - 选择此项可导出标准使用量和购买量
    - **摊销成本（使用量和购买量）** - 选择此项可针对购买量（如 Azure 预留）导出摊销成本
1. 对于“导出类型”，请选择：
    - **每日导出本月累计成本** - 每日提供一个新的本月累计成本的导出文件。 最新数据是根据以前的每日导出聚合的。
    - **每周导出过去七天的成本** - 创建自选定的导出开始日期起过去七天的每周成本导出文件。
    - **每月导出上个月的成本** - 提供上月成本的导出文件（与创建导出的当前月份进行比较）。 然后，计划在每月的第五天运行导出，其中包含以前的月份成本。
    - **一次性导出** - 允许你选择要导出到 Azure blob 存储的历史数据的日期范围。 你可以导出从所选的那一天起最多 90 天的历史成本。 此导出会立即运行，最多 2 小时后便可在存储帐户中获取它。
        根据你的导出类型，请选择“开始日期”，或选择“从”和“到”日期 。
1. 指定 Azure 存储帐户的订阅，然后选择一个资源组，或者创建一个新的资源组。
1. 选择存储帐户名称或新建一个。
1. 选择位置（Azure 区域）。
1. 指定要将导出文件放置到其中的存储容器和目录路径。
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="新导出示例" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. 检查导出详细信息，然后选择“创建”。

新导出将出现在导出列表中。 默认情况下，新导出为启用状态。 如果要禁用或删除计划的导出，请选择列表中的任何项，然后选择“禁用”或“删除”。

最初，在导出运行之前，可能需要 12 到 24 个小时。 但是，可能需要更长时间才能在导出的文件中显示数据。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

以编程方式创建导出时，必须手动将 `Microsoft.CostManagementExports` 资源提供程序注册到存储帐户所在的订阅。 使用 Azure 门户创建导出时，会自动进行注册。 有关如何注册资源提供程序的详细信息，请参阅[注册资源提供程序](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. 登录后，若要查看当前导出，请使用 [az costmanagement export list](/cli/azure/costmanagement/export#az_costmanagement_export_list) 命令：

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* 除了订阅之外，还可以针对资源组和管理组创建导出。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。
   >* 在计费帐户范围内或在客户的租户上以合作伙伴身份登录时，可以将数据导出到链接到合作伙伴存储帐户的 Azure 存储帐户。 但是，必须在 CSP 租户中具有活动订阅。

1. 创建一个资源组或使用现有资源组。 若要创建资源组，请使用 [az group create](/cli/azure/group#az_group_create) 命令：

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. 可以创建一个存储帐户或使用现有存储账户来接收导出。 如需创建存储帐户，可使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令：

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. 运行 [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) 命令以创建导出：

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   对于 --type 参数，可以选择 `ActualCost`、`AmortizedCost` 或 `Usage`。

   本示例使用 `MonthToDate`。 导出会每天为你的本月至今成本创建一个导出文件。 最新数据是根据本月的以前每日导出聚合的。

1. 若要查看导出操作的详细信息，请使用 [az costmanagement export show](/cli/azure/costmanagement/export#az_costmanagement_export_show) 命令：

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. 使用 [az costmanagement export update](/cli/azure/costmanagement/export#az_costmanagement_export_update) 命令更新导出：

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   此示例更改导出目录。

>[!NOTE]
>最初，在导出运行之前，可能需要 12 到 24 个小时。 但是，可能需要更长时间才能在导出的文件中显示数据。

可以使用 [az costmanagement export delete](/cli/azure/costmanagement/export#az_costmanagement_export_delete) 命令删除导出：

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

以编程方式创建导出时，必须手动将 `Microsoft.CostManagementExports` 资源提供程序注册到存储帐户所在的订阅。 使用 Azure 门户创建导出时，会自动进行注册。 有关如何注册资源提供程序的详细信息，请参阅[注册资源提供程序](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。

首先为 Azure PowerShell 准备环境：

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > 尽管 Az.CostManagement PowerShell 模块为预览版，但必须使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. 登录后，若要查看当前导出，请使用 [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) cmdlet：

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* 除了订阅之外，还可以针对资源组和管理组创建导出。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。
   >* 在计费帐户范围内或在客户的租户上以合作伙伴身份登录时，可以将数据导出到链接到合作伙伴存储帐户的 Azure 存储帐户。 但是，必须在 CSP 租户中具有活动订阅。

1. 创建一个资源组或使用现有资源组。 若要创建资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet：

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. 可以创建一个存储帐户或使用现有存储账户来接收导出。 若要创建存储帐户，请使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet：

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. 运行 [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) cmdlet 以创建导出：

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   对于 DefinitionType 参数，可以选择 `ActualCost`、`AmortizedCost` 或 `Usage`。

   本示例使用 `MonthToDate`。 导出会每天为你的本月至今成本创建一个导出文件。 最新数据是根据本月的以前每日导出聚合的。

1. 若要查看导出操作的详细信息，请使用 `Get-AzCostManagementExport` cmdlet：

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. 使用 [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) cmdlet 更新导出：

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   此示例更改导出目录。

>[!NOTE]
>最初，在导出运行之前，可能需要 12 到 24 个小时。 但是，可能需要更长时间才能在导出的文件中显示数据。

可以使用 [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) cmdlet 删除导出：

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>导出计划

计划的导出受刚开始创建导出时的时间以及星期几的影响。 创建计划的导出后，随后的每次导出都会以相同的频率运行。 例如，对于设置为每天频率的每日的本月至今累计成本导出，导出每天都会运行。 同样，对于每周导出，导出每周按计划在同一天运行。 不保证导出的确切交付时间，但导出的数据在运行导出后 4 小时内可用。

每次导出都会创建新文件，因此旧的导出不会被覆盖。

#### <a name="create-an-export-for-multiple-subscriptions"></a>为多个订阅创建导出

如果你有企业协议，则可以使用管理组在单个容器中聚合订阅成本信息。 然后，可以导出管理组的成本管理数据。 管理组的导出仅支持实际成本。

不支持导出其他订阅类型的管理组。

1. 如果尚未创建管理组，请创建一个，并为其分配订阅。
1. 在成本分析中，将范围设置为管理组，并选择“选择此管理组”。
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="显示“选择此管理组”选项的示例" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. 在范围内创建导出，获取管理组中订阅的成本管理数据。
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="显示具有管理组范围的“新建导出”选项的示例":::

### <a name="file-partitioning-for-large-datasets"></a>大型数据集的文件分区

如果你有 Microsoft 客户协议、Microsoft 合作伙伴协议或企业协议，则可以启用导出将文件分块到多个较小的文件分区中，以帮助进行数据引入。 首次配置导出时，请将“文件分区”设置设置为“启用” 。 默认情况下设置为“关闭”。

:::image type="content" source="./media/tutorial-export-acm-data/file-partition.png" alt-text="显示“文件分区”选项的屏幕截图。" lightbox="./media/tutorial-export-acm-data/file-partition.png" :::

如果没有 Microsoft 客户协议、Microsoft 合作伙伴协议或企业协议，就不会看到“文件分区”选项。

#### <a name="update-existing-exports-to-use-file-partitioning"></a>更新现有导出以使用文件分区

如果你有现有导出，并且想要设置文件分区，请新建导出。 文件分区仅适用于最新的导出版本。 创建的使用情况文件中的某些字段可能有一些细微更改。

如果对现有导出启用文件分区，则可能会在文件输出中看到字段的细微更改。 任何更改都是由最初设置后对导出进行的更新产生的。

#### <a name="partitioning-output"></a>分区输出

启用文件分区后，你将获得导出中的每个数据分区的文件以及 _manifest.json 文件。 清单包含完整数据集的摘要，以及其中每个文件分区的信息。 每个文件分区都有标题，只包含完整数据集的一个子集。 若要处理完整的数据集，必须引入导出的每个分区。

下面是 _manifest.json 示例清单文件。

```json
{
  "manifestVersion": "2021-01-01",
  "dataFormat": "csv",
  "blobCount": 1,
  "byteCount": 160769,
  "dataRowCount": 136,
  "blobs": [
    {
      "blobName": "blobName.csv",
      "byteCount": 160769,
      "dataRowCount": 136,
      "headerRowCount": 1,
      "contentMD5": "md5Hash"
    }
  ]
}
```

### <a name="export-versions"></a>导出版本

在 Azure 门户中或使用 API 创建计划导出后，该导出将始终在创建时使用的导出版本上运行。 Azure 将你之前创建的导出保留在相同版本中，除非你更新它。 如果导出版本发生更改，这样做会阻止费用更改和 CSV 字段更改。 导出功能随着时间而变化，字段名称有时也会更改，还会添加新的字段。

如果你想要使用最新的可用数据和字段，建议在 Azure 门户中创建新的导出。 若要将现有导出更新到最新版本，请在 Azure 门户中或者使用最新版的导出 API 来更新它。 如果更新现有导致，则你可能会在字段中看到细微差异，而且之后生成的文件也可能产生费用。


## <a name="verify-that-data-is-collected"></a>验证收集的数据

可以轻松验证正在收集的成本管理数据，并使用 Azure 存储资源管理器查看导出的 CSV 文件。

在导出列表中，选择存储帐户名称。 在存储帐户页上，选择“在资源管理器中打开”。 如果看到确认框，选择“是”，即可在 Azure 存储资源管理器中打开该文件。

![存储帐户页面，其中显示示例信息和“在资源管理器中打开”链接](./media/tutorial-export-acm-data/storage-account-page.png)

在存储资源管理器中，导航到你想要打开的容器，并选择与当前月份相对应的文件夹。 然后将显示 CSV 文件列表。 选择一个文件，然后选择“打开”。

![存储资源管理器中显示的示例信息](./media/tutorial-export-acm-data/storage-explorer.png)

该文件将通过设置为打开 CSV 文件扩展名的程序或应用程序打开。 下面是 Excel 中的一个示例。

![Excel 中显示的导出 CSV 数据示例](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>下载导出的 CSV 数据文件

还可以在 Azure 门户中下载导出的 CSV 文件。 以下步骤说明如何从成本分析中查找它。

1. 在成本分析中，依次选择“设置”和“导出” 。
1. 在导出列表中，选择导出的存储帐户。
1. 在存储帐户中，选择“容器”。
1. 在容器列表中，选择该容器。
1. 在目录和存储 blob 中导航至所需日期。
1. 选择 CSV 文件，然后选择“下载”。

[![示例导出下载](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>查看导出运行历史记录

你可以通过在导出列表页中选择单个导出来查看计划导出的运行历史记录。 导出列表页还提供了快速访问，可查看先前、下次以及将来导出的运行时间。 下面是显示运行历史记录的示例。

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="屏幕截图显示“导出”窗格。":::

选择导出以查看其运行历史记录。

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="屏幕截图显示导出的运行历史记录。":::

## <a name="access-exported-data-from-other-systems"></a>从其他系统访问导出的数据

导出成本管理数据的用途之一是从外部系统访问数据。 你可能使用的是仪表板系统或其他财务系统。 此类系统各不相同，因此，只通过一个示例说明并不能解决实际问题。  但是，你可以在 [Azure 存储简介](../../storage/common/storage-introduction.md)中开始学习如何访问应用程序中的数据。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建每日导出
> * 验证收集的数据

进入下一个教程，通过识别闲置和未充分利用的资源来优化和提高效率。

> [!div class="nextstepaction"]
> [查看并采纳优化建议](tutorial-acm-opt-recommendations.md)
