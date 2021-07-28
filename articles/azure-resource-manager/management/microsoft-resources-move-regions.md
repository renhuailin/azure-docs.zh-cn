---
title: 为 Microsoft.Resources 中的资源移动区域
description: 介绍了如何将位于 Microsoft.Resources 命名空间中的资源移动到新区域。
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725865"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>将 Microsoft.Resources 资源移动到新区域

可能需要将现有资源移动到新区域。 本文介绍了如何移动位于 Microsoft.Resources 命名空间中的 templateSpecs 和 deploymentScripts 这两个资源类型。

## <a name="move-template-specs-to-new-region"></a>将模板规格移动到新区域

如果一个区域中有一个[模板规格](../templates/template-specs.md)，而你想要将其移动到新区域，则可以导出该模规格范并重新部署。

1. 使用命令导出现有的模板规格。对于参数值，请提供与要导出的模板规格匹配的值。

   对于 Azure PowerShell，请使用：

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   对于 Azure CLI，请使用：

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. 使用导出的模板规格创建新的模板规格。下面的示例显示了新区域的 `westus`，但你可以提供所需的区域。

   对于 Azure PowerShell，请使用：

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   对于 Azure CLI，请使用：

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>将部署脚本移动到新区域

1. 选择包含要移动到新区域的部署脚本的资源组。

1. [导出模板](../templates/export-template-portal.md)。 导出时，选择部署脚本和任何其他必需的资源。

1. 在导出的模板中，删除以下属性：

   * tenantId
   * principalId
   * clientId

1. 导出的模板中包含部署脚本区域的硬编码值。

   ```json
   "location": "westus2",
   ```

   更改模板以允许参数设置位置。 有关详细信息，请参阅[在 ARM 模板中设置资源位置](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [部署导出的模板](../templates/deploy-powershell.md)并为部署脚本指定新区域。

## <a name="next-steps"></a>后续步骤

* 若要了解如何将资源移到新的资源组或订阅，请参阅[将资源移到新的资源组或订阅](move-resource-group-and-subscription.md)。
* 若要了解如何将资源移动到新区域，请参阅[跨区域移动 Azure 资源](move-resources-overview.md#move-resources-across-regions)。
