---
title: Azure 防火墙服务标记概述
description: 服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529543"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火墙服务标记

服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

Azure 防火墙服务标记可用于网络规则目标字段。 它们可用于代替特定的 IP 地址。

## <a name="supported-service-tags"></a>支持的服务标记

有关可在 Azure 防火墙网络规则中使用的服务标记的列表，请参阅[虚拟网络服务标记](../virtual-network/service-tags-overview.md#available-service-tags)。

## <a name="configuration"></a>配置

Azure 防火墙支持通过 PowerShell、Azure CLI 或 Azure 门户来配置服务标记。

### <a name="configure-via-azure-powershell"></a>通过 Azure PowerShell 配置

在此示例中，我们必须首先获取以前创建的 Azure 防火墙实例的上下文。

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

接下来必须创建新规则。  对于“源”或“目标”，可以指定要利用的服务标记的文本值，如前文所述。

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

接下来，必须用创建的新网络规则来更新包含 Azure 防火墙定义的变量。

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

最后，必须将网络规则更改提交到正在运行的 Azure 防火墙实例。
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 防火墙规则，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。
