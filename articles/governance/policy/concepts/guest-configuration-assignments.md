---
title: 了解来宾配置分配资源
description: 来宾配置创建名为来宾配置分配的扩展资源，这些资源将配置映射到计算机。
ms.date: 08/15/2021
ms.topic: conceptual
ms.openlocfilehash: c106ca492166cc604607bfc8da3f9c7d7b3bff2d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868312"
---
# <a name="understand-guest-configuration-assignment-resources"></a>了解来宾配置分配资源

分配 Azure Policy 时，如果类别为“来宾配置”，则会提供元数据来描述来宾分配。

[提供本文档的视频演练](https://youtu.be/DmCphySEB7A)。

可以将来宾分配视为计算机和 Azure Policy 方案之间的链接。 例如，下面的代码片段将 Azure Windows 基线配置与策略范围内任何计算机的最低版本 `1.0.0` 相关联。

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

## <a name="how-azure-policy-uses-guest-configuration-assignments"></a>Azure Policy 如何使用来宾配置分配

来宾配置服务使用元数据信息自动为具有 AuditIfNotExists 或 DeployIfNotExists 策略效果的定义创建审核资源 。 资源类型为 `Microsoft.GuestConfiguration/guestConfigurationAssignments`。 Azure Policy 使用来宾分配资源的 complianceStatus 属性来报告合规性状态。 有关详细信息，请参阅[获取符合性数据](../how-to/get-compliance-data.md)。

### <a name="deletion-of-guest-assignments-from-azure-policy"></a>删除 Azure Policy 中的来宾分配

删除 Azure Policy 分配时，如果该策略创建了来宾配置分配，那么来宾配置分配也会被删除。

## <a name="manually-creating-guest-configuration-assignments"></a>手动创建来宾配置分配

Azure 资源管理器中的来宾分配资源可以由 Azure Policy 或任何客户端 SDK 创建。

部署模板示例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2021-01-25",
      "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
      "name": "myMachine/Microsoft.GuestConfiguration/myConfig",
      "location": "westus2",
      "properties": {
        "guestConfiguration": {
          "name": "myConfig",
          "contentUri": "https://mystorageaccount.blob.core.windows.net/mystoragecontainer/myConfig.zip?sv=SASTOKEN",
          "contentHash": "SHA256HASH",
          "version": "1.0.0",
          "assignmentType": "ApplyAndMonitor",
          "configurationParameter": {}
        }
      }
    }    
  ]
}
```

下表描述了来宾分配资源的每个属性。

| 属性 | 说明 |
|-|-|
| name | 内容包 MOF 文件中配置的名称。 |
| contentUri | 指向内容包 (.zip) 的 HTTPS URI 路径。 |
| contentHash | 内容包的 SHA256 哈希值，用于验证是否未更改。 |
| 版本 | 内容包的版本。 仅用于内置包，不用于自定义内容包。 |
| assignmentType | 分配行为。 允许的值：`Audit`、`ApplyandMonitor` 和 `ApplyandAutoCorrect`。 |
| configurationParameter | 内容包 MOF 文件中的 DSC 资源类型、名称和值的列表，该文件下载到计算机之后将被重写。 |

### <a name="deletion-of-manually-created-guest-configuration-assignments"></a>删除手动创建的来宾配置分配

必须手动删除通过任何手动方法（如 Azure 资源管理器模板部署）创建的来宾配置分配。
删除父资源（虚拟机或已启用 Arc 的虚拟机计算机）时也会删除来宾配置分配。

若要手动删除来宾配置分配，请使用以下示例。 请务必替换用“\<\>”括号指示的所有示例字符串。

```PowerShell
# First get details about the guest configuration assignment
$resourceDetails = @{
  ResourceGroupName = '<myResourceGroupName>'
  ResourceType      = 'Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments/'
  ResourceName      = '<myVMName>/Microsoft.GuestConfiguration'
  ApiVersion        = '2020-06-25'
}
$guestAssignment = Get-AzResource @resourceDetails

# Review details of the guest configuration assignment
$guestAssignment

# After reviewing properties of $guestAssignment to confirm
$guestAssignment | Remove-AzResource
```

## <a name="next-steps"></a>后续步骤

- 阅读[来宾配置概述](./guest-configuration.md)。
- 设置自定义来宾配置包[开发环境](../how-to/guest-configuration-create-setup.md)。
- 为来宾配置[创建包项目](../how-to/guest-configuration-create.md)。
- 从开发环境[测试包项目](../how-to/guest-configuration-create-test.md)。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](../how-to/guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)。
