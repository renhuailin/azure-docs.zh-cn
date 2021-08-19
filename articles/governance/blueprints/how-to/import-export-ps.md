---
title: 使用 PowerShell 导入和导出蓝图
description: 了解如何将蓝图定义作为代码进行使用。 使用导入和导出命令对其进行共享、源代码管理和管理。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: b4d7eef9ca650d6d8796acb4e235f49889d42821
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323265"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>使用 PowerShell 导入和导出蓝图定义

可以通过 Azure 门户完全管理 Azure 蓝图。 随着组织推进使用 Azure 蓝图，它们应该开始将蓝图定义视为托管代码。 此概念通常称为基础结构即代码 (IaC)。 将蓝图定义视为代码，可提供除 Azure 门户提供的优势之外的其他优势。 优势包括：

- 共享蓝图定义
- 备份蓝图定义
- 在不同租户或订阅中重用蓝图定义
- 在源代码管理中放置蓝图定义
  - 在测试环境中自动测试蓝图定义
  - 支持持续集成和持续部署 (CI/CD) 管道

无论出于何种原因，将蓝图定义作为代码进行管理都是有好处的。 本文介绍了如何在 [Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) 模块中使用 `Import-AzBlueprintWithArtifact` 和 `Export-AzBlueprintWithArtifact` 命令。

## <a name="prerequisites"></a>先决条件

本文假定你具备中等程度的 Azure 蓝图使用知识。 如果尚不具备，请阅读以下文章：

- [在门户中创建蓝图](../create-blueprint-portal.md)
- 了解[部署阶段](../concepts/deployment-stages.md)和[蓝图生命周期](../concepts/lifecycle.md)
- 通过 PowerShell [创建](../create-blueprint-powershell.md)和[管理](./manage-assignments-ps.md)蓝图定义和分配

如果尚未安装，请按照[添加 Az.Blueprint 模块](./manage-assignments-ps.md#add-the-azblueprint-module)中的说明安装并验证 PowerShell 库中的 Az.Blueprint 模块。

## <a name="folder-structure-of-a-blueprint-definition"></a>蓝图定义的文件夹结构

在学习如何导入和导出蓝图之前，先看看组成蓝图定义的文件的结构。 蓝图定义应存储在其自己的文件夹中。

> [!IMPORTANT]
> 如果没有将任何值传递给 `Import-AzBlueprintWithArtifact` cmdlet 的“Name”参数，则使用存储蓝图定义的文件夹的名称。

除了蓝图定义（必须命名为 `blueprint.json`）外，还有组成蓝图定义的项目。 每个项目都必须位于名为 `artifacts` 的子文件夹中。
放在一起，文件夹中作为 JSON 文件的蓝图定义的结构应如下所示：

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>导出蓝图定义

导出蓝图定义的步骤非常简单。 导出蓝图定义对于共享、备份或置于源代码管理中非常有用。

- Blueprint [必填]
  - 指定蓝图定义
  - 使用 `Get-AzBlueprint` 获取引用对象
- OutputPath [必填]
  - 指定用于保存蓝图定义 JSON 文件的路径
  - 输出文件位于使用蓝图定义名称的子文件夹中
- Version（可选）
  - 如果蓝图引用对象包含对多个版本的引用，则指定要输出的版本。

1. 获取对要从表示为 `{subId}` 的订阅中导出的蓝图定义的引用：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. 使用 `Export-AzBlueprintWithArtifact` cmdlet 导出指定的蓝图定义：

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>导入蓝图定义

在[所需的文件夹结构](#folder-structure-of-a-blueprint-definition)中具有[导出的蓝图定义](#export-your-blueprint-definition)或手动创建的蓝图定义后，便可以将该蓝图定义导入到另一个管理组或订阅中。

有关内置蓝图定义的示例，请参阅 [Azure 蓝图 GitHub 存储库](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)。

- Name [必填]
  - 指定新蓝图定义的名称
- InputPath [必填]
  - 指定创建蓝图定义的路径
  - 必须与[所需的文件夹结构](#folder-structure-of-a-blueprint-definition)相匹配
- ManagementGroupId（可选）
  - 在不是当前上下文默认值的情况下，用于保存蓝图定义的管理组 ID
  - 必须指定“ManagementGroupId”或“SubscriptionId” 
- SubscriptionId（可选）
  - 在不是当前上下文默认值的情况下，用于保存蓝图定义的订阅 ID
  - 必须指定“ManagementGroupId”或“SubscriptionId” 

1. 使用 `Import-AzBlueprintWithArtifact` cmdlet 导入指定的蓝图定义：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

导入蓝图定义后，[使用 PowerShell 进行分配](./manage-assignments-ps.md#create-blueprint-assignments)。

有关创建高级蓝图定义的信息，请参阅以下文章：

- 使用[静态和动态参数](../concepts/parameters.md)。
- 自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 使用[蓝图资源锁定](../concepts/resource-locking.md)保护部署。
- [将蓝图作为代码管理](https://github.com/Azure/azure-blueprints/blob/master/README.md)

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。