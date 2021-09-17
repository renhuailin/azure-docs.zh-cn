---
title: 快速入门：使用 Azure PowerShell 创建和更新 Azure 防火墙策略
description: 本快速入门介绍如何使用 Azure PowerShell 创建和更新 Azure 防火墙策略。
services: firewall-manager
author: vaboya
ms.author: victorh
ms.date: 08/16/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.openlocfilehash: 3e7eef6eed76f2b6e3e122c1a373580e05556f03
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254314"
---
# <a name="quickstart-create-and-update-an-azure-firewall-policy-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建和更新 Azure 防火墙策略

在本快速入门中，你将使用 Azure PowerShell 创建包含网络和应用程序规则的 Azure 防火墙策略。 还可添加网络和应用程序规则来更新现有策略。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="set-up-the-network-and-policy"></a>设置网络和策略

首先，创建资源组和虚拟网络。 然后创建 Azure 防火墙策略。

### <a name="create-a-resource-group"></a>创建资源组

资源组包含此过程中使用的所有资源。

```azurepowershell
New-AzResourceGroup -Name Test-FWpolicy-RG -Location "East US"
```

### <a name="create-a-virtual-network"></a>创建虚拟网络

```azurepowershell
$ServerSubnet = New-AzVirtualNetworkSubnetConfig -Name subnet-1 -AddressPrefix 10.0.0.0/24
$testVnet = New-AzVirtualNetwork -Name Test-FWPolicy-VNET -ResourceGroupName Test-FWPolicy-RG -Location "East US" -AddressPrefix 10.0.0.0/8 -Subnet $ServerSubnet
```
### <a name="create-a-firewall-policy"></a>创建防火墙策略

```azurepowershell
New-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG -Location "EAST US"
```

## <a name="create-a-network-rule-collection-group-and-add-new-rules"></a>创建网络规则集合组并添加新规则

首先，创建规则集合组，然后使用规则添加规则集合。

### <a name="create-the-network-rule-collection-group"></a>创建网络规则集合组

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newnetworkrulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "NetworkRuleCollectionGroup" -Priority 200 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
```
### <a name="create-network-rules"></a>创建网络规则

```azurepowershell
$networkrule1= New-AzFirewallPolicyNetworkRule -Name NwRule1 -Description testRule1  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.1/32 -DestinationPort 22 
$networkrule2= New-AzFirewallPolicyNetworkRule -Name NWRule2 -Description TestRule2  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.10/32 -DestinationPort 1434
```
### <a name="create-a-network-rule-collection-and-add-new-rules"></a>创建网络规则集合并添加新规则

```azurepowershell
$newrulecollectionconfig=New-AzFirewallPolicyFilterRuleCollection -Name myfirstrulecollection -Priority 1000 -Rule $networkrule1,$networkrule2 -ActionType Allow
$newrulecollection = $networkrulecollectiongroup.Properties.RuleCollection.Add($newrulecollectionconfig)
```

### <a name="update-the-network-rule-collection-group"></a>更新网络规则集合组

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -Priority "200" -FirewallPolicyObject $firewallpolicy -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```

### <a name="output"></a>输出

查看新规则集合及其规则：

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-Output  $output
```

## <a name="add-network-rules-to-an-existing-rule-collection"></a>向现有规则集合添加网络规则

现在，你已有了一个规则集合，可以向其添加更多规则。

### <a name="get-existing-network-rule-group-collection"></a>获取现有网络规则组集合

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy 
```

### <a name="create-new-network-rules"></a>创建新的网络规则

```azurepowershell
$newnetworkrule1 = New-AzFirewallPolicyNetworkRule -Name newNwRule01 -Description testRule01  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.5/32 -DestinationPort 3389
$newnetworkrule2 = New-AzFirewallPolicyNetworkRule -Name newNWRule02 -Description TestRule02  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.15/32 -DestinationPort 1434
```
### <a name="update-the-network-rule-collection-and-add-new-rules"></a>更新网络规则集合并添加新规则

```azurepowershell
$getexistingrullecollection = $networkrulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myfirstrulecollection"}
$getexistingrullecollection.RuleS.Add($newnetworkrule1)
$getexistingrullecollection.RuleS.Add($newnetworkrule2)
```
### <a name="update-network-rule-collection-group"></a>更新网络规则收集组

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 200 -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>输出

查看刚添加的规则：

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-output $output
```
## <a name="create-an-application-rule-collection-and-add-new-rules"></a>创建应用程序规则集合并添加新规则

首先，创建规则集合组，然后使用规则添加规则集合。

### <a name="create-the-application-rule-collection-group"></a>创建应用程序规则集合组

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newapprulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "ApplicationRuleCollectionGroup" -Priority 300 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
```
### <a name="create-new-application-rules"></a>创建新的应用程序规则

```azurepowershell
$apprule1 = New-AzFirewallPolicyApplicationRule -Name apprule1 -Description testapprule1 -SourceAddress 192.168.0.1/32 -TargetFqdn "*.contoso.com" -Protocol HTTPS
$apprule2 = New-AzFirewallPolicyApplicationRule -Name apprule2 -Description testapprule2  -SourceAddress 192.168.0.10/32 -TargetFqdn "www.contosoweb.com" -Protocol HTTPS
```
### <a name="create-a-new-application-rule-collection-with-rules"></a>创建包含规则的新应用程序规则集合

```azurepowershell
$apprulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
$apprulecollection = New-AzFirewallPolicyFilterRuleCollection -Name myapprulecollection -Priority 1000 -Rule $apprule1,$apprule2 -ActionType Allow 
$newapprulecollection = $apprulecollectiongroup.Properties.RuleCollection.Add($apprulecollection) 
```

### <a name="update-the-application-rule-collection-group"></a>更新应用程序规则集合组

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection 
```

### <a name="output"></a>输出

检查新的规则集合组及其新规则：

```azurepowershell
$output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="add-application-rules-to-an-existing-rule-collection"></a>向现有规则集合添加应用程序规则

现在，你已有了一个规则集合，可以向其添加更多规则。

```azurepowershell 
#Create new Application Rules for exist Rule collection
$newapprule1 = New-AzFirewallPolicyApplicationRule -Name newapprule01 -Description testapprule01 -SourceAddress 192.168.0.5/32 -TargetFqdn "*.contosoabc.com" -Protocol HTTPS
$newapprule2 = New-AzFirewallPolicyApplicationRule -Name newapprule02 -Description testapprule02  -SourceAddress 192.168.0.15/32 -TargetFqdn "www.contosowebabcd.com" -Protocol HTTPS
```

### <a name="update-the-application-rule-collection"></a>更新应用程序规则集合

```azurepowershell
$apprulecollection = $apprulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myapprulecollection"}
$apprulecollection.Rules.Add($newapprule1)
$apprulecollection.Rules.Add($newapprule2)

# Update Application Rule collection Group  
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>输出

查看新规则：

```azurepowershell
$Output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="clean-up-resources"></a>清理资源

当你不再需要所创建的资源时，请删除资源组。 这会删除所有创建的资源。

若要删除资源组，请使用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell
Remove-AzResourceGroup -Name Test-FWpolicy-RG
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关 Azure 防火墙管理器部署的详细信息](deployment-overview.md)