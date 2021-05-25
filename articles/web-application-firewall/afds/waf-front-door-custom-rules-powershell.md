---
title: 为 Azure Front Door 配置 WAF 自定义规则和默认规则集
description: 了解如何为现有 Front Door 终结点配置由自定义规则和托管规则组成的 WAF 策略。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 9a0e262db9f5c37189a589eefc451a88dd5ea8c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563403"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 应用程序防火墙策略

Azure Web 应用程序防火墙 (WAF) 策略定义了在请求到达 Front Door 时所需的检查。
本文介绍了如何配置由一些自定义规则组成并启用了 Azure 托管默认规则集的 WAF 策略。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

在开始设置速率限制策略之前，请先创建 PowerShell 环境和 Front Door 配置文件。

### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境

Azure PowerShell 提供一组可以使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](/powershell/azure/) 并在任何 PowerShell 会话中使用它。 按照页面上的说明操作，以使用 Azure 凭据登录，并安装 Az PowerShell 模块。

#### <a name="sign-in-to-azure"></a>登录 Azure

```
Connect-AzAccount

```
在安装 Front Door 模块之前，请确保安装最新版本的 PowerShellGet。 运行下面的命令，重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>创建 Front Door 配置文件

遵循以下文档中的说明来创建 Front Door 配置文件：[快速入门：创建 Front Door 配置文件](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>基于 http 参数的自定义规则

下面的示例展示了如何使用 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 配置包含两个匹配条件的自定义规则。 请求来自引荐者定义的指定站点，且查询字符串不包含“password”。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>基于 http 请求方法的自定义规则

使用 [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) 创建阻止“PUT”方法的规则，如下所示：

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>创建基于大小约束的自定义规则

下面的示例使用 Azure PowerShell 创建阻止 URL 长度超过 100 个字符的请求的规则：
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>添加托管默认规则集

下面的示例使用 Azure PowerShell 创建托管默认规则集：
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，在包含 Front Door 配置文件的指定资源组中，使用 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 配置包含前面步骤中所创建的规则的安全策略。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>将策略关联到 Front Door 前端主机

将安全策略对象关联到现有 Front Door 前端主机，并更新 Front Door 属性。 首先，使用 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 检索 Front Door 对象。
接下来，使用 [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 将前端 WebApplicationFirewallPolicyLink 属性设置为上一步中创建的“$myWAFPolicy$”的“resourceId”。 

下面的示例使用资源组名称 myResourceGroupFD1，并假设你已按照[快速入门：创建 Front Door](../../frontdoor/quickstart-create-front-door.md) 一文中的说明操作创建了 Front Door 配置文件。 同样，在下面的示例中，将 $frontDoorName 替换为你的 Front Door 配置文件名。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 只需设置一次 WebApplicationFirewallPolicyLink 属性，即可将安全策略关联到 Front Door 前端。 后续策略更新会自动应用到前端。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Front Door](../../frontdoor/front-door-overview.md) 
- 详细了解[结合使用 WAF 和 Front Door](afds-overview.md)