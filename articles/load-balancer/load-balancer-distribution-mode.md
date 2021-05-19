---
title: 配置 Azure 负载均衡器分发模式
titleSuffix: Azure Load Balancer
description: 在本文中，我们开始配置 Azure 负载均衡器的分配模式以支持源 IP 关联。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739910"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>配置 Azure 负载均衡器的分配模式

Azure 负载均衡器支持两种分发模式，用于将流量分发到应用程序：

* 基于哈希
* 源 IP 关联

本文介绍如何配置 Azure 负载均衡器的分发模式。


## <a name="configure-distribution-mode"></a>配置分发模式

---

# <a name="azure-portal"></a>[**Azure 门户**](#tab/azure-portal)

可以通过修改门户中的负载均衡规则来更改分发模式的配置。

1. 登录 Azure 门户，并单击“资源组”来找到包含要更改的负载均衡器的资源组。
2. 在“负载均衡器概述”屏幕中，选择“设置”下的“负载均衡规则” 。
3. 在“负载均衡规则”屏幕中，选择要更改分发模式的负载均衡规则。
4. 在规则下，通过更改“会话持续性”下拉框来更改分发模式。 

可以使用以下选项： 

* **无(基于哈希)** - 指定任何虚拟机可能处理来自同一客户端的后续请求。
* **客户端 IP (源 IP 关联二元组)** - 指定来自同一客户端 IP 地址的连续请求将由同一虚拟机处理。
* **客户端 IP 和协议(源 IP 关联三元组)** - 指定来自同一客户端 IP 地址和协议组合的连续请求将由同一虚拟机处理。

5. 选择分发模式，然后选择“保存”。

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="更改负载均衡器规则上的会话持续性。" border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用 PowerShell 更改现有负载均衡规则上的负载均衡器分发设置。 以下命令将更新分发模式： 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

设置 `LoadDistribution` 元素的值，实现所需的负载均衡类型。 

* 为二元组（源 IP 和目标 IP）负载均衡指定 SourceIP。 

* 为三元组（源 IP、目标 IP 和协议类型）负载均衡指定 SourceIPProtocol。 

* 指定默认值来实现五元组负载均衡的默认行为。

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

使用 Azure CLI 更改现有负载均衡规则上的负载均衡器分发设置。  以下命令将更新分发模式：

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
设置 `--load-distribution` 的值，实现所需的负载均衡类型。

* 为二元组（源 IP 和目标 IP）负载均衡指定 SourceIP。 

* 为三元组（源 IP、目标 IP 和协议类型）负载均衡指定 SourceIPProtocol。 

* 指定默认值来实现五元组负载均衡的默认行为。

有关本文中使用的命令的详细信息，请参阅 [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>后续步骤

* [Azure 负载均衡器概述](load-balancer-overview.md)
* [开始配置面向 Internet 的负载均衡器](quickstart-load-balancer-standard-public-powershell.md)
* [配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)