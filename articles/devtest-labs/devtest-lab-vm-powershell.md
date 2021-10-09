---
title: 使用 Azure PowerShell 在 Azure 开发测试实验室中创建虚拟机
description: 了解如何使用 Azure 开发测试实验室通过 Azure PowerShell 创建和管理虚拟机。
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 128962ae54ef5dbbcc9578cb5dbff9d1531336ea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600582"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用 Azure PowerShell 通过开发测试实验室创建虚拟机
本文介绍如何使用 Azure PowerShell 在 Azure 开发测试实验室中创建虚拟机。 可以使用 PowerShell 脚本在 Azure 开发测试实验室的实验室中自动创建虚拟机。 

## <a name="prerequisites"></a>先决条件
开始之前：

- 如果你不想使用现有实验室来测试本文中的脚本或命令，请[创建实验室](devtest-lab-create-lab.md)。 
- [安装 Azure PowerShell](/powershell/azure/install-az-ps) 或使用集成到 Azure 门户中的 Azure Cloud Shell。 

## <a name="powershell-script"></a>PowerShell 脚本
本部分中的示例脚本使用 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) cmdlet。  此 cmdlet 采用实验室的资源 ID、要执行的操作的名称 (`createEnvironment`)，以及执行该操作所需的参数。 参数在一个包含所有虚拟机说明属性的哈希表中。 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

使用上述脚本中虚拟机的属性，我们可以创建使用 Windows Server 2016 DataCenter 作为 OS 的虚拟机。 对于每种类型的虚拟机，这些属性会略有不同。 [定义虚拟机](#define-virtual-machine)部分显示了如何确定要在此脚本中使用的属性。

以下命令提供一个示例，演示了如何运行保存在文件 Create-LabVirtualMachine.ps1 中的脚本。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定义虚拟机
此部分演示如何获取特定于要创建的虚拟机类型的属性。 

### <a name="use-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建 VM 时，可以生成 Azure 资源管理器模板。 无需完成创建 VM 的过程。 只需要执行这些步骤，直到看到该模板。 如果尚未创建实验室 VM，则这是获取所需 JSON 说明的最佳方式。 

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航菜单中，选择“所有服务”。
3. 从服务列表中搜索并选择“开发测试实验室”。 
4. 在“开发测试实验室”页的实验室列表中，选择你的实验室。
5. 在实验室主页的工具栏中，选择“+ 添加”。 
6. 为 VM 选择“基础映像”。 
7. 选择“提交”按钮上方页面底部的“自动化选项”。 
8. 你会看到用于创建虚拟机的“Azure 资源管理器模板”。 
9. **resources** 节中的 JSON 段包含之前所选映像类型的定义。 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

在此示例中，你将了解如何获取 Azure 市场映像的定义。 可以采用相同的方式获取自定义映像、公式或环境的定义。 添加虚拟机所需的任何项目，并设置所需的任何高级设置。 在为必填字段和任何可选字段提供值之后，请在选择“自动化选项”按钮之前执行它。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
以下过程提供了使用 REST API 获取映像属性的步骤：这些步骤仅适用于实验室中的现有 VM。 

1. 导航到[虚拟机 - 列表](/rest/api/dtl/virtualmachines/list)页，选择“试用”按钮。 
2. 选择 **Azure 订阅**。
3. 输入 **实验室的资源组**。
4. 输入 **实验室的名称**。 
5. 选择“运行”。
6. 此时会显示基于已创建 VM 的 **映像属性**。 

## <a name="set-expiration-date"></a>设置过期日期
在训练、演示和试验等方案中，你可能需要创建虚拟机并在固定的持续时间后自动将其删除，以免产生不必要的成本。 使用 PowerShell 创建 VM 时，可以为其设置到期日期，如示例 [PowerShell 脚本](#powershell-script)部分所示。

下面是一个示例 PowerShell 脚本，用于为实验室中的所有现有 VM 设置到期日期：

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>后续步骤
请参阅以下内容：[Azure 开发测试实验室的 Azure PowerShell 文档](/powershell/module/az.devtestlabs/)
