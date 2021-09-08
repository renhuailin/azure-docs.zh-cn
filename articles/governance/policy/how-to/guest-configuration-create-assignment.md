---
title: 如何使用模板创建来宾配置分配
description: 了解如何直接从 Azure 资源管理器将配置部署到计算机。
ms.date: 08/09/2021
ms.topic: how-to
ms.openlocfilehash: aa7938a9421a9e7680af34af475585c4dc1c818a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868305"
---
# <a name="how-to-create-a-guest-configuration-assignment-using-templates"></a>如何使用模板创建来宾配置分配

[将来宾配置包分配](../concepts/guest-configuration-assignments.md)给多台计算机的最佳方法是使用 [Azure Policy](./guest-configuration-create-definition.md)。 也可以将来宾配置包分配给单台计算机。

## <a name="built-in-and-custom-configurations"></a>内置配置和自定义配置

若要将来宾配置包分配给单台计算机，请修改以下示例。 一共有两种情况。

- 使用[已发布](./guest-configuration-create-publish.md)的包的链接将自定义配置应用于计算机。
- 将[内置](../samples/built-in-packages.md)配置应用于计算机，例如 Azure 基线。

## <a name="extending-other-resource-types-such-as-arc-enabled-servers"></a>扩展其他资源类型，例如已启用 Arc 的服务器

在以下每个部分中，示例都包含一个名称以 `Microsoft.Compute/virtualMachines` 开头的 type 属性。 来宾配置资源提供程序 `Microsoft.GuestConfiguration` 是必须引用父类型的[扩展资源](../../../azure-resource-manager/management/extension-resource-types.md)。

若要修改示例以适用于其他资源类型（例如[已启用 Arc 的服务器](../../../azure-arc/servers/overview.md)），可将父类型更改为资源提供程序的名称。
对于已启用 Arc 的服务器，资源提供程序为 `Microsoft.HybridCompute/machines`。

将以下“<>”字段替换为特定于你的环境的值：

- **<vm_name>** ：将把配置应用到的计算机资源的名称
- **<configuration_name>** ：要应用的配置的名称
- **<vm_location>** ：将在其中创建来宾配置分配的 Azure 区域
- **<Url_to_Package.zip>** ：对于自定义内容包，使用指向 .zip 文件的 HTTPS 链接
- **<SHA256_hash_of_package.zip>** ：对于自定义内容包，使用 .zip 文件的 SHA256 哈希

## <a name="assign-a-configuration-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板分配配置

可以部署包含来宾配置分配资源的 [Azure 资源管理器模板](../../../azure-resource-manager/templates/deployment-tutorial-local-template.md?tabs=azure-powershell)。

以下示例分配自定义配置。

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "<configuration_name>",
                    "contentUri": "<Url_to_Package.zip>",
                    "contentHash": "<SHA256_hash_of_package.zip>",
                    "assignmentType": "ApplyAndMonitor"
                }
            }
        }
```

以下示例分配 `AzureWindowBaseline` 内置配置。

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "AzureWindowsBaseline",
                    "version": "1.*",
                    "assignmentType": "ApplyAndMonitor",
                    "configurationParameter": [
                    {
                        "name": "Minimum Password Length;ExpectedValue",
                        "value": "16"
                    },
                    {
                        "name": "Minimum Password Length;RemediateValue",
                        "value": "16"
                    },
                    {
                        "name": "Maximum Password Age;ExpectedValue",
                        "value": "75"
                    },
                    {
                        "name": "Maximum Password Age;RemediateValue",
                        "value": "75"
                    }
                ]
                }
            }
        }
```

## <a name="assign-a-configuration-using-bicep"></a>使用 Bicep 分配配置

可以使用 [Azure Bicep](../../../azure-resource-manager/bicep/overview.md) 来部署来宾配置分配。

以下示例分配自定义配置。

```Bicep
resource myVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource myConfiguration 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: '<configuration_name>'
  scope: myVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: '<configuration_name>'
      contentUri: '<Url_to_Package.zip>'
      contentHash: '<SHA256_hash_of_package.zip>'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
    }
  }
}
```

以下示例分配 `AzureWindowBaseline` 内置配置。

```Bicep
resource myWindowsVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource AzureWindowsBaseline 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: 'AzureWindowsBaseline'
  scope: myWindowsVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: 'AzureWindowsBaseline'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
      configurationParameter: [
        {
          name: 'Minimum Password Length;ExpectedValue'
          value: '16'
        }
        {
          name: 'Minimum Password Length;RemediateValue'
          value: '16'
        }
        {
          name: 'Maximum Password Age;ExpectedValue'
          value: '75'
        }
        {
          name: 'Maximum Password Age;RemediateValue'
          value: '75'
        }
      ]
    }
  }
}
```

## <a name="assign-a-configuration-using-terraform"></a>使用 Terraform 分配配置

可以使用 [Terraform](https://www.terraform.io/) 来[部署](/azure/developer/terraform/get-started-windows-powershell)来宾配置分配。

> [!IMPORTANT]
> Terraform 提供程序 [azurerm_policy_virtual_machine_configuration_assignment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_configuration_policy_assignment) 未更新，还不支持 `assignmentType` 属性，仅支持执行审核的配置。

以下示例分配自定义配置。

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "<configuration_name>" {
  name               = "<configuration_name>"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name            = "<configuration_name>"
    contentUri      =  '<Url_to_Package.zip>'
    contentHash     =  '<SHA256_hash_of_package.zip>'
    version         = "1.*"
    assignmentType  = "ApplyAndMonitor
  }
}
```

以下示例分配 `AzureWindowBaseline` 内置配置。

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "AzureWindowsBaseline" {
  name               = "AzureWindowsBaseline"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name    = "AzureWindowsBaseline"
    version = "1.*"
    parameter {
      name  = "Minimum Password Length;ExpectedValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Length;RemediateValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Age;ExpectedValue"
      value = "75"
    }
    parameter {
      name  = "Minimum Password Age;RemediateValue"
      value = "75"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

- 阅读[来宾配置概述](../concepts/guest-configuration.md)。
- 设置自定义来宾配置包[开发环境](../how-to/guest-configuration-create-setup.md)。
- 为来宾配置[创建包项目](../how-to/guest-configuration-create.md)。
- 从开发环境[测试包项目](../how-to/guest-configuration-create-test.md)。
- [发布包项目](./guest-configuration-create-publish.md)，以便计算机可以访问它。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](../how-to/guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)。
