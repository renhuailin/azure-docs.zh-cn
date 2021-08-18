---
title: 在 Azure 开发测试实验室中为 VM 指定资源组 | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中为实验室中的 VM 指定资源组。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8b7b331f505864edb5fd2b2cca2f56d9d6349db3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282738"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中为实验室虚拟机指定资源组

作为实验室所有者，你可以配置要在特定的资源组中创建的实验室虚拟机。 此功能在以下场景中很有用处：

- 订阅中的实验室创建的资源组较少。
- 让实验室在你配置的一个固定资源组中运行。
- 处理在 Azure 订阅中创建资源组所需的限制和批准。
- 将所有实验室资源整合到单个资源组中，以简化对这些资源的跟踪以及在资源组级别应用[策略](../governance/policy/overview.md)来管理资源。

有了此功能，就可以使用脚本为所有实验室 VM 指定 Azure 订阅中的新资源组或现有资源组。 目前，Azure 开发测试实验室通过 API 来支持此功能。

> [!NOTE]
> 在开发测试实验室中创建实验室时，所有订阅限制都适用。 请将实验室视同订阅中的任何其他资源。 如果是资源组，则限制为[每个订阅 980 个资源组](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 

## <a name="use-azure-portal"></a>使用 Azure 门户
请按照以下步骤为实验室中创建的所有 VM 指定资源组。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航菜单中，选择“所有服务”。 
3. 从列表中选择“开发测试实验室”。
4. 从实验室列表中选择实验室。  
5. 在左侧菜单的“设置”部分选择“配置和策略” 。 
6. 在左侧菜单上选择“实验室设置”。 
7. 选择“一个资源组中的所有虚拟机”。 
8. 在下拉列表中选择现有资源组，或者选择“新建”，输入资源组的名称，然后选择“确定”  。 

    ![为所有实验室 VM 选择该资源组](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>使用 PowerShell 
下面的示例介绍了如何使用 PowerShell 脚本在新资源组中创建所有实验室虚拟机。

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

请使用以下命令调用该脚本。 ResourceGroup.ps1 是包含上述脚本的文件：

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
如果使用 Azure 资源管理器模板创建实验室，请在模板的实验室 properties 节中使用 vmCreationResourceGroupId 属性，如以下示例所示：

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>用于为实验室 VM 配置资源组的 API
在使用此 API 时，作为实验室所有者，你有以下选择：

- 为所有虚拟机选择实验室的资源组。
- 为所有虚拟机选择实验室的资源组以外的某个现有的资源组。
- 为所有虚拟机输入新资源组名称。
- 继续使用现有行为，即为实验室中的每个 VM 都创建资源组。
 
此设置适用于在实验室中创建的新虚拟机。 实验室中那些在它们自己的资源组中创建的旧 VM 不会受影响。 在实验室中创建的环境会继续保留在它们自己的资源组中。

如何使用此 API：
- 请使用 API 版本 2018-10-15-preview。
- 如果指定新资源组，请确保你对订阅中的资源组有写入权限。 如果没有写入权限，在指定的资源组中创建新虚拟机会失败。
- 使用 API 时，请传入 **完整的资源组 ID**。 例如：`/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`。 请确保资源组与实验室位于同一订阅中。 


## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-set-lab-policy.md)
- [常见问题解答](devtest-lab-faq.yml)
