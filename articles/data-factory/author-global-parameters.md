---
title: 全局参数
description: 为每个 Azure 数据工厂环境设置全局参数
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: minhe-msft
ms.author: hemin
ms.date: 05/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b634f3866432c9cba3ae35f18fe9d146dabea585
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741018"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure 数据工厂中的全局参数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

全局参数是整个数据工厂的常量，可以由任何表达式中的管道使用。 当多个管道具有相同的参数名称和值时，这些全局参数会很有用。 使用持续集成和部署过程 (CI/CD) 提升数据工厂时，可以在每个环境中替代这些参数。 

## <a name="creating-global-parameters"></a>创建全局参数

若要创建全局参数，请转到“管理” 部分中的“全局参数”选项卡。 选择“新建”以打开“创建”侧导航栏。

![突出显示选择用于创建全局参数的“新建”按钮的屏幕截图。](media/author-global-parameters/create-global-parameter-1.png)

在侧导航栏中，输入名称，选择数据类型，并指定参数的值。

![显示添加新的全局参数的名称、数据类型和值的位置的屏幕截图。](media/author-global-parameters/create-global-parameter-2.png)

创建全局参数后，可以通过单击参数的名称对其进行编辑。 若要同时更改多个参数，请选择“全部编辑”。

![创建全局参数](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>在管道中使用全局参数

全局参数可用于任何[管道表达式](control-flow-expression-language-functions.md)。 如果管道引用其他资源（如数据集或数据流），则可以通过该资源的参数向下传递全局参数值。 全局参数以 `pipeline().globalParameters.<parameterName>` 形式进行引用。

![使用全局参数](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD 中的全局参数

可以通过两种方式在持续集成和部署解决方案中集成全局参数：

* 在 ARM 模板中包含全局参数
* 通过 PowerShell 脚本部署全局参数

对于一般用例，建议在 ARM 模板中包含全局参数。 这会与 [CI/CD 文档](continuous-integration-deployment.md)中列出的解决方案进行原生集成。在使用自动发布和 Purview 连接的情况下，需要使用 PowerShell 脚本方法。 稍后可以找到有关 PowerShell 脚本方法的更多信息。 默认情况下，全局参数将作为 ARM 模板参数添加，因为它们经常根据不同的环境而变化。 可以从“管理”中心启用将全局参数包含在 ARM 模板中的设置。

![包含在 ARM 模板中](media/author-global-parameters/include-arm-template.png)

> [!NOTE]
> 只能在“Git 模式”下使用“包含在 ARM 模板中”配置。 目前，在“实时模式”或“数据工厂”模式下会禁用此配置。 在使用自动发布或 Purview 连接的情况下，不要使用包含全局参数的方法；请使用 PowerShell 脚本方法。 

> [!WARNING]
>不能在参数名称中使用“-”。 否则将收到错误代码 "{"code":"BadRequest","message":"ErrorCode=InvalidTemplate,ErrorMessage=The expression >'pipeline().globalParameters.myparam-dbtest-url' is not valid: .....}"。 但可以在参数名称中使用“_”。 

将全局参数添加到 ARM 模板会添加一个工厂级设置，该设置将替代其他工厂级设置，例如客户管理的密钥，或其他环境中的 Git 配置。 如果在提升的环境（如 UAT 或 PROD）中启用这些设置，则最好在下面突出显示的步骤中通过 PowerShell 脚本部署全局参数。 


### <a name="deploying-using-powershell"></a>使用 PowerShell 进行部署

以下步骤概述如何通过 PowerShell 部署全局参数。 当目标工厂具有工厂级设置（如客户管理的密钥）时，此方法非常有用。

发布工厂或导出包含全局参数的 ARM 模板时，会创建一个名为 *globalParameters* 的文件夹，其中包含名为 *your-factory-name_GlobalParameters.json* 的文件。 此文件是一个 JSON 对象，其中包含已发布的工厂中的每个全局参数类型和值。

![发布全局参数](media/author-global-parameters/global-parameters-adf-publish.png)

如果要部署到新的环境（如 TEST 或 PROD），则建议创建此全局参数文件的副本，并覆盖相应的特定于环境的值。 重新发布时，将覆盖原始全局参数文件，但用于其他环境的副本将保持不变。

例如，如果你有一个名为“ADF-DEV”的工厂，以及一个名为“environment”、值为“dev”的字符串类型的全局参数，则在发布时，将生成名为 *ADF-DEV_GlobalParameters.json* 的文件。 如果部署到名为“ADF_TEST”的测试工厂，请创建 JSON 文件（例如，名为 ADF-TEST_GlobalParameters.json 的文件）的副本，并将参数值替换为特定于环境的值。 参数“environment”现在可能具有值“test”。 

![部署全局参数](media/author-global-parameters/powershell-task.png)

使用以下 PowerShell 脚本将全局参数提升到其他环境。 在进行 ARM 模板部署之前添加一个 Azure PowerShell DevOps 任务。 在该 DevOps 任务中，必须指定新参数文件、目标资源组和目标数据工厂的位置。

> [!NOTE]
> 若要使用 PowerShell 部署全局参数，必须至少使用 Az 模块版本 4.4.0。

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>后续步骤

* 了解 Azure 数据工厂的[持续集成和部署过程](continuous-integration-deployment.md)
* 了解如何使用[控制流表达式语言](control-flow-expression-language-functions.md)
