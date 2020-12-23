---
title: 配置负载均衡器 TCP 重置和空闲超时
titleSuffix: Azure Load Balancer
description: 本文介绍如何配置 Azure 负载均衡器 TCP 空闲超时并重置。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747201"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>配置 Azure 负载均衡器的 TCP 重置和空闲超时

Azure 负载均衡器的空闲超时范围如下：

* 对于出站规则为4分钟到100分钟
* 负载均衡器规则和入站 NAT 规则4到30分钟

默认情况下，它设置为4分钟。 如果处于非活动状态的时间超过超时值，则不能保证在客户端和服务之间保持 TCP 或 HTTP 会话。 

以下部分介绍如何更改负载均衡器资源的空闲超时和 tcp 重置设置。

## <a name="set-tcp-reset-and-idle-timeout"></a>设置 tcp 重置和空闲超时
---
# <a name="portal"></a>[**门户**](#tab/tcp-reset-idle-portal)

若要为负载均衡器设置空闲超时和 tcp 重置，请编辑负载均衡规则。 

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单中，选择 " **资源组** "。

3. 选择负载均衡器的资源组。 在此示例中，资源组名为 **myResourceGroup** 。

4. 选择负载均衡器。 在此示例中，负载均衡器名为 **myLoadBalancer** 。

5. 在 " **设置** " 中，选择 " **负载均衡规则** "。

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="编辑负载均衡器规则。" border="true":::

6. 选择负载均衡规则。 在此示例中，负载均衡规则名为 **mylbrule 更改** 。

7. 在负载平衡规则中，将 "空闲超时" 下的滑块 **(分钟)** 移动到超时值。  

8. 在 " **TCP 重置** " 下，选择 " **已启用** "。

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="编辑负载均衡器规则。" border="true":::

9. 选择“保存” 。

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

若要设置空闲超时和 tcp 重置，请在以下负载平衡规则参数中设置值，并将其设置为 [AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)：

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

将以下示例替换为资源中的值：

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

若要设置空闲超时和 tcp 重置，请对 [az network lb rule update](/cli/azure/network/lb/rule?az_network_lb_rule_update)使用以下参数：

* **--idle-timeout**
* **--enable-tcp-reset**

开始之前验证环境：

* 登录 Azure 门户，并通过运行 `az login` 来检查订阅是否处于活动状态。
* 通过运行 `az --version` 在终端或命令窗口中检查 Azure CLI 版本。 有关最新版本，请参阅[最新发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果没有最新版本，请按照[适用于你操作系统或平台的安装指南](/cli/azure/install-azure-cli)来更新安装。

将以下示例替换为资源中的值：

* **myResourceGroup**
* **myLoadBalancer**
* **Mylbrule 更改**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>后续步骤

有关 tcp 空闲超时和重置的详细信息，请参阅 [负载均衡器 Tcp 重置和空闲超时](load-balancer-tcp-reset.md)

有关配置负载均衡器分发模式的详细信息，请参阅 [配置负载均衡器分发模式](load-balancer-distribution-mode.md)。
