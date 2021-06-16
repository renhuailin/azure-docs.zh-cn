---
title: 配置 Front Door 的 WAF 速率限制规则 - Azure PowerShell
description: 了解如何配置现有 Front Door 终结点的速率限制规则。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6db1c2a80700da1710294b2b8d08ec87c9398a2b
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678469"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 应用程序防火墙速率限制规则
Azure Front Door 的 Azure Web 应用程序防火墙 (WAF) 速率限制规则控制了一分钟持续时间内允许来自客户端的请求数。
本文介绍了如何使用 Azure PowerShell 配置 WAF 速率限制规则，此规则控制允许从客户端到 URL 中包含 /promo 的 Web 应用程序的请求数。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 速率限制应用于每个客户端 IP 地址。 如果有多个客户端从不同的 IP 地址访问 Front Door，则它们会应用自己的速率限制。

## <a name="prerequisites"></a>先决条件
在开始设置速率限制策略之前，请先创建 PowerShell 环境和 Front Door 配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](/powershell/azure/) 并在任何 PowerShell 会话中使用它。 按照页面上的说明操作，以使用 Azure 凭据登录，并安装 Az PowerShell 模块。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用交互式登录对话框连接到 Azure
```
Connect-AzAccount

```
在安装 Front Door 模块之前，请先确保已安装 PowerShellGet 的当前版本。 运行下面的命令，然后重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>创建 Front Door 配置文件
按照[快速入门：创建 Front Door 配置文件](../../frontdoor/quickstart-create-front-door.md)中的说明操作，以创建 Front Door 配置文件：

## <a name="define-url-match-conditions"></a>定义 URL 匹配条件
使用 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 定义 URL 匹配条件（URL 包含 /promo）。
下面的示例将 /promo 匹配为 RequestUri 变量的值：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>创建自定义速率限制规则
使用 [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) 设置速率限制。 在下面的示例中，限制设置为 1000。 任何在一分钟内向 promo 页面发送超过 1000 个请求的客户端都会被阻止，直到下一分钟开始。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzureRmResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，在包含 Front Door 配置文件的指定资源组中，使用 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 配置具有自定义速率限制规则的安全策略。

下面的示例使用资源组名称 myResourceGroupFD1 和 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)，并假设你已按照[快速入门：创建 Front Door](../../frontdoor/quickstart-create-front-door.md) 一文中的说明操作创建了 Front Door 配置文件。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>将策略关联到 Front Door 前端主机
将安全策略对象关联到现有的 Front Door 前端主机，并更新 Front Door 属性。 首先，使用 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 命令检索 Front Door 对象。
接下来，使用 [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 命令将前端 WebApplicationFirewallPolicyLink 属性设置为上一步中创建的“$ratePolicy”的“resourceId”。 

下面的示例使用资源组名称 myResourceGroupFD1，并假设你已按照[快速入门：创建 Front Door](../../frontdoor/quickstart-create-front-door.md) 一文中的说明操作创建了 Front Door 配置文件。 同样，在下面的示例中，将 $frontDoorName 替换为你的 Front Door 配置文件名。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 只需设置一次 WebApplicationFirewallPolicyLink 属性，即可将安全策略关联到 Front Door 前端。 后续策略更新会自动应用到前端。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Front Door](../../frontdoor/front-door-overview.md)。
