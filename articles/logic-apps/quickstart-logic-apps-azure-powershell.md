---
title: 快速入门 - 在多租户 Azure 逻辑应用中使用 Azure PowerShell 创建和管理工作流
description: 使用 PowerShell 在多租户 Azure 逻辑应用中创建和管理逻辑应用工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, contperf-fy21q2
ms.date: 07/26/2021
ms.openlocfilehash: 42f6ac6ce06f1b852af2027e6ef79f347d8050a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783022"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-powershell-in-multi-tenant-azure-logic-apps"></a>快速入门：在多租户 Azure 逻辑应用中使用 Azure PowerShell 创建和管理工作流

本快速入门演示如何使用 [Azure PowerShell](/powershell/azure/install-az-ps) 创建和管理逻辑应用。 在 PowerShell 中，可以使用逻辑应用工作流定义的 JSON 文件来创建逻辑应用。 随后可以通过在 [Az.LogicApp](/powershell/module/az.logicapp/) PowerShell 模块中运行 cmdlet 来管理逻辑应用。

如果你不熟悉 Azure 逻辑应用，还可以了解如何[通过 Azure 门户](quickstart-create-first-logic-app-workflow.md)创建第一个逻辑应用，以及如何[在 Visual Studio 中](quickstart-create-logic-apps-with-visual-studio.md)和[在 Visual Studio Code 中](quickstart-create-logic-apps-visual-studio-code.md)创建第一个逻辑应用。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在本地计算机上安装 [Az PowerShell](/powershell/azure/install-az-ps) 模块。
* 要在其中创建逻辑应用的 [ Azure 资源组](#example---create-resource-group)。

### <a name="prerequisite-check"></a>先决条件检查

开始之前验证环境：

* 登录 Azure 门户，并通过运行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 来检查订阅是否处于活动状态。
* 通过运行 `Get-InstalledModule -Name Az` 来检查Azure PowerShell 的版本。 有关最新版本，请参阅[最新发行说明](/powershell/azure/migrate-az-6.0.0)。
  * 如果没有最新版本，请遵循[更新 Azure PowerShell 模块](/powershell/azure/install-az-ps#update-the-azure-powershell-module)来更新你的安装。

### <a name="example---create-resource-group"></a>示例 - 创建资源组

如果还没有用于逻辑应用的资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建该组。 例如，以下命令在位置 `westus` 创建名为 `testResourceGroup` 的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name testResourceGroup -Location westus
```

已成功创建资源组时，输出会将 `ProvisioningState` 显示为 `Succeeded`：

```Output
ResourceGroupName : testResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup
```

## <a name="workflow-definition"></a>工作流定义

使用 Azure PowerShell [创建新逻辑应用](#create-logic-apps-from-powershell)或[更新现有逻辑应用](#update-logic-apps-from-powershell)之前，需要逻辑应用的工作流定义。 在 Azure 门户中，可以通过从“设计器”视图切换到“代码”视图，以 JSON 格式查看逻辑应用的基础工作流定义。

运行命令以创建或更新逻辑应用时，工作流定义会作为必需参数 (`Definition`) 或 (`DefinitionFilePath`)（具体取决于参数集）进行上传。 必须将工作流定义创建为遵循[工作流定义语言架构](./logic-apps-workflow-definition-language.md)的 JSON 文件。

## <a name="create-logic-apps-from-powershell"></a>从 PowerShell 创建逻辑应用

可以使用 cmdlet [`New-AzLogicApp`](/powershell/module/az.logicapp/new-azlogicapp) 以及定义的 JSON 文件，从 Azure PowerShell 创建逻辑应用工作流。

### <a name="example---create-logic-app"></a>示例 - 创建逻辑应用

此示例在资源组 `testResourceGroup`（位置为 `westus`）中创建一个名为 `testLogicApp` 的工作流。 JSON 文件 `testDefinition.json` 包含工作流定义。

```azurepowershell-interactive
New-AzLogicApp -ResourceGroupName testResourceGroup -Location westus -Name testLogicApp -DefinitionFilePath .\testDefinition.json
```

成功创建工作流后，PowerShell 会显示新工作流定义。

## <a name="update-logic-apps-from-powershell"></a>从 PowerShell 更新逻辑应用

还可以使用 cmdlet [`Set-AzLogicApp`](/powershell/module/az.logicapp/set-azlogicapp)，从 Azure PowerShell 更新逻辑应用工作流。

### <a name="example---update-logic-app"></a>示例 - 更新逻辑应用

此示例演示如何使用不同的 JSON 定义文件 `newTestDefinition.json` 更新[在上一部分中创建的示例工作流](#example---create-logic-app)。

```azurepowershell-interactive
Set-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp -DefinitionFilePath .\newTestDefinition.json
```

成功更新工作流后，PowerShell 会显示逻辑应用更新后的工作流定义。

## <a name="delete-logic-apps-from-powershell"></a>从 PowerShell 删除逻辑应用

可以使用 cmdlet 从 Azure PowerShell中删除逻辑应用的工作流 [`Remove-AzLogicApp`](/powershell/module/az.logicapp/remove-azlogicapp)。

### <a name="example---delete-logic-app"></a>示例 - 删除逻辑应用

此示例会删除[在上一节中创建的示例工作流](#example---create-logic-app)。

```azurepowershell-interactive
Remove-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

在使用 `y` 响应确认提示之后，会删除逻辑应用。

### <a name="considerations---delete-logic-app"></a>注意事项 - 删除逻辑应用

删除逻辑应用会以下列方式方式影响工作流实例：

* 逻辑应用服务会可能取消任何正在进行和挂起的运行。

  即使使用较大的卷或积压工作 (backlog)，大多数运行在完成或开始之前都将被取消。 但是，取消过程可能需要一些时间才能完成。 同时，在运行时执行取消过程中，可能会选取某些运行来执行。

* 逻辑应用服务不会创建或运行新的工作流实例。

* 如果删除工作流，然后重新创建相同的工作流，则重新创建的工作流不会具有与删除的工作流相同的元数据。 必须重新保存任何调用删除工作流的工作流。 这样，调用方就可获取重新创建的工作流的正确信息。 否则，对重新创建的工作流的调用将失败并显示 `Unauthorized` 错误。 此行为也适用于在集成帐户中使用项目的工作流和调用 Azure 函数的工作流。

## <a name="show-logic-apps-in-powershell"></a>在 PowerShell 中显示逻辑应用

可以使用命令 [`Get-AzLogicApp`](/powershell/module/az.logicapp/get-azlogicapp) 获取特定逻辑应用工作流。

### <a name="example---get-logic-app"></a>示例 - 获取逻辑应用

此示例在资源组 `testResourceGroup` 中返回逻辑应用 `testLogicApp`。

```azurepowershell-interactive
Get-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Microsoft 代码示例浏览器](/samples/browse/?products=azure-logic-apps)中找到其他逻辑应用脚本示例。