---
title: Application Insights 的版本批注 | Microsoft 文档
description: 了解如何通过 Application Insights 创建注释以跟踪部署或其他重要事件。
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: cfd1c9b28a79d68983e49ef5d6dfd70dd357ab47
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071022"
---
# <a name="release-annotations-for-application-insights"></a>Application Insights 的版本注释

注释显示将新版本部署到了何处，或者显示其他重要事件。 使用批注可让轻松查看更改是否对应用程序的性能产生了任何影响。 [Azure Pipelines](/azure/devops/pipelines/tasks/) 生成系统可自动创建批注。 也可以通过 PowerShell 创建批注，用于标记所要处理的任何事件。

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 生成中的版本批注

版本批注是 Azure DevOps 的基于云的 Azure Pipelines 服务功能。

如果满足以下所有条件，部署任务会自动创建发布注释：

- 你要部署的资源链接到 Application Insights（通过 `APPINSIGHTS_INSTRUMENTATIONKEY` 应用设置）。
- Application Insights 资源与要部署到的资源位于同一订阅中。
- 你使用的是以下 Azure DevOps 管道任务之一：

    | 任务代码                 | 任务名称                     | 版本     |
    |---------------------------|-------------------------------|--------------|
    | AzureAppServiceSettings   | Azure 应用服务设置    | 任意          |
    | AzureRmWebAppDeployment   | Azure 应用服务部署      | V3 和更高版本 |
    | AzureFunctionApp          | Azure Functions               | 任意          |
    | AzureFunctionAppContainer | 适用于容器的 Azure Functions | 任意          |
    | AzureWebAppContainer      | 用于容器的 Azure Web 应用  | 任意          |
    | AzureWebApp               | Azure Web 应用                 | 任意          |

> [!NOTE]
> 如果仍在使用 Application Insights 注释部署任务，则应将其删除。

### <a name="configure-release-annotations"></a>配置版本批注

如果无法使用上一部分中的部署任务，则需要在部署管道中添加内联脚本任务。

1. 导航到新的或现有的管道，然后选择一项任务。
    :::image type="content" source="./media/annotations/task.png" alt-text="选定阶段中任务的屏幕截图。" lightbox="./media/annotations/task.png":::
1. 添加一个新任务，然后选择“Azure CLI”。
    :::image type="content" source="./media/annotations/add-azure-cli.png" alt-text="添加新任务并选择 Azure CLI 的屏幕截图。" lightbox="./media/annotations/add-azure-cli.png":::
1. 指定相关的 Azure 订阅。  将“脚本类型”更改为“PowerShell”，将“脚本位置”更改为“内联”。
1. 将 [下一节中步骤 2 中的 PowerShell 脚本](#create-release-annotations-with-azure-cli)添加到 **内联脚本**。
1. 添加下面的参数，并将带尖括号的占位符替换为脚本参数的值。 -releaseProperties 是可选的。

    ```powershell
        -aiResourceId "<aiResourceId>" `
        -releaseName "<releaseName>" `
        -releaseProperties @{"ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
    ```

    :::image type="content" source="./media/annotations/inline-script.png" alt-text="Azure CLI 任务设置的屏幕截图，突出显示了脚本类型、脚本位置、内联脚本和脚本参数。" lightbox="./media/annotations/inline-script.png":::

1. 保存。

## <a name="create-release-annotations-with-azure-cli"></a>使用 Azure CLI 创建版本注释

可以使用 CreateReleaseAnnotation PowerShell 脚本，通过所需的任何流程创建批注，而无需使用 Azure DevOps。

1. 登录到 [Azure CLI](/cli/azure/authenticate-azure-cli)。

2. 生成以下脚本的本地副本，并将其命名为 CreateReleaseAnnotation.ps1。

    ```powershell
    param(
        [parameter(Mandatory = $true)][string]$aiResourceId,
        [parameter(Mandatory = $true)][string]$releaseName,
        [parameter(Mandatory = $false)]$releaseProperties = @()
    )
    
    $annotation = @{
        Id = [GUID]::NewGuid();
        AnnotationName = $releaseName;
        EventTime = (Get-Date).ToUniversalTime().GetDateTimeFormats("s")[0];
        Category = "Deployment";
        Properties = ConvertTo-Json $releaseProperties -Compress
    }
    
    $body = (ConvertTo-Json $annotation -Compress) -replace '(\\+)"', '$1$1"' -replace "`"", "`"`""

    az rest --method put --uri "$($aiResourceId)/Annotations?api-version=2015-05-01" --body "$($body) "
    ```

3. 使用以下代码调用 PowerShell 脚本（请将带尖括号的占位符替换为你自己的值）。 -releaseProperties 是可选的。

    ```powershell
         .\CreateReleaseAnnotation.ps1 `
          -aiResourceId "<aiResourceId>" `
          -releaseName "<releaseName>" `
          -releaseProperties @{"ReleaseDescription"="<a description>";
              "TriggerBy"="<Your name>" }
    ```

|参数 | 定义 | 注意|
|--------------|-----------------------|--------------------|
|aiResourceId | 目标 Application Insights 资源的资源 ID。 | 示例：<br> /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRGName/providers/microsoft.insights/components/MyResourceName|
|releaseName | 为创建的版本注释提供的名称。 | | 
|releaseProperties | 用于将自定义元数据附加到注释。 | 可选|

## <a name="view-annotations"></a>查看批注

> [!NOTE]
> 发布注释目前在 Application Insights 的“指标”窗格中不可用

现在，每当使用发布模板部署新版本时，就会将批注发送到 Application Insights。 可在以下位置查看注释：

- 性能

    :::image type="content" source="./media/annotations/performance.png" alt-text="“性能”选项卡的屏幕截图，其中选中了版本注释（蓝色箭头）以显示“版本属性”选项卡。" lightbox="./media/annotations/performance.png":::

- 失败数

    :::image type="content" source="./media/annotations/failures.png" alt-text="“失败数”选项卡的屏幕截图，其中选中了版本注释（蓝色箭头）以显示“版本属性”选项卡。" lightbox="./media/annotations/failures.png":::
- 使用情况

    :::image type="content" source="./media/annotations/usage-pane.png" alt-text="“用户”选项卡的屏幕截图，其中选中了版本注释。版本注释在图表上方显示为蓝色箭头，指示版本发布的时刻。" lightbox="./media/annotations/usage-pane.png":::

- 工作簿

    在任何基于日志的工作簿查询中，可视化效果沿 x 轴显示时间。
    
    :::image type="content" source="./media/annotations/workbooks-annotations.png" alt-text="工作簿窗格的屏幕截图，其中显示了带注释的基于时序日志的查询。" lightbox="./media/annotations/workbooks-annotations.png":::
    
    要在工作簿中启用注释，请转到“高级设置”，然后选择“显示注释”。
    
    :::image type="content" source="./media/annotations/workbook-show-annotations.png" alt-text="“高级设置”菜单的屏幕截图，其中突出显示了“显示注释”复选框。":::

选择任一注释标记可打开有关该发布的详细信息，包括请求者、源代码管理分支、发布管道和环境。

## <a name="next-steps"></a>后续步骤

* [创建工作项](./diagnostic-search.md#create-work-item)
* [使用 PowerShell 自动化](./powershell.md)
