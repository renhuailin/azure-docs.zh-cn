---
title: 迁移到 Azure 防火墙高级预览版
description: 了解如何从 Azure 防火墙标准版迁移到 Azure 防火墙高级预览版。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 27125e7f635c6d8f0690ebd39fb84eb3e0fb2989
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110700558"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>迁移到 Azure 防火墙高级预览版

可以将 Azure 防火墙标准版迁移到 Azure 防火墙高级预览版，以利用新的高级功能。 要详细了解 Azure 防火墙高级预览版功能，请参阅 [Azure 防火墙高级预览版功能](premium-features.md)。

下面两个示例演示如何：
- 使用 Azure PowerShell 迁移现有标准策略
- 使用高级策略将现有标准防火墙（与经典规则）迁移到 Azure 防火墙高级版。

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>使用 Azure PowerShell 迁移现有策略

`Transform-Policy.ps1` 是从现有标准策略创建新高级策略的 Azure PowerShell 脚本。

如果是标准防火墙策略 ID，该脚本会将其转换为高级 Azure 防火墙策略。 此脚本首先连接到 Azure 帐户，拉取策略，转换/添加各种参数，然后上传新的高级策略。 新的高级策略命名为 `<previous_policy_name>_premium`。

用法示例：

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> 脚本不迁移“威胁情报”设置。 需要先记下这些设置，然后再手动进行迁移。

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>使用 Azure 门户迁移现有标准防火墙

此示例演示如何使用 Azure 门户将标准防火墙（经典规则）迁移到具有高级策略的 Azure 防火墙高级版。

1. 在 Azure 门户中，选择标准防火墙。 在“概述”页上，选择“迁移到防火墙策略”。 

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="迁移到防火墙策略":::

1. 在“迁移到防火墙策略”页上，选择“查看 + 创建”。 
1. 选择“创建”  。

   部署需要数分钟才能完成。
1. 使用 `Transform-Policy.ps1`[Azure PowerShell 脚本](#migrate-an-existing-policy-using-azure-powershell)将这个新的标准策略转换为高级策略。
1. 在门户中，选择标准防火墙资源。 
1. 在“自动化”下，选择“导出模板”。   请将此浏览器选项卡保持打开状态。 稍后我们将返回到这里。
   > [!TIP]
   > 如果要确保不会丢失模板，请下载并保存，以防浏览器选项卡关闭或刷新。
1. 打开新的浏览器选项卡，导航到 Azure 门户，并打开包含防火墙的资源组。
1. 删除现有的标准防火墙实例。

   此过程需要花费几分钟时间才能完成。

1. 返回到浏览器选项卡，其中包含已导出的模板。
1. 选择“部署”，然后在“自定义部署”页上选择“编辑模板”。  
1. 编辑模板文本：
   
   1. 在 `Microsoft.Network/azureFirewalls` 资源下的 `Properties`、`sku` 下，将 `tier` 从“标准”更改为“高级”。
   1. 在模板 `Parameters` 下，将 `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` 的 `defaultValue` 从：
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      更改为：

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. 选择“保存”。
1. 选择“查看 + 创建”  。
1. 选择“创建”  。

部署完成后，现在可以配置所有新的 Azure 防火墙高级预览版功能。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 防火墙高级功能](premium-features.md)
