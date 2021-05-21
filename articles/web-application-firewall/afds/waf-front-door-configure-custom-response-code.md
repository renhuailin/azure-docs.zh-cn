---
title: 使用 Azure Front Door 为 Web 应用程序防火墙 (WAF) 配置自定义响应
description: 了解如何配置在 WAF 阻止请求时显示的自定义响应代码和消息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563675"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>为 Azure Web 应用程序防火墙 (WAF) 配置自定义响应

默认情况下，当 WAF 由于匹配规则而阻止请求时，它将返回 403 状态代码以及“请求被阻止”消息。 默认消息还包括跟踪参考字符串，可用于链接到该请求的[日志条目](./waf-front-door-monitor.md)。  你可以为用例配置自定义响应状态代码和带有参考字符串的自定义消息。 本文介绍如何配置在 WAF 阻止请求时显示的自定义响应页面。

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>使用门户配置自定义响应状态代码和消息

可以从 WAF 门户中的“策略设置”下配置自定义响应状态代码和正文。

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF 策略设置":::

在上面的示例中，我们将响应代码保留为 403，并配置了一个简短的“请联系我们”消息，如下图所示：

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="自定义响应示例":::

“{{azure-ref}}”在响应正文中插入唯一引用字符串。 此值与 `FrontdoorAccessLog` 和 `FrontdoorWebApplicationFirewallLog` 日志中的 TrackingReference 字段匹配。

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>使用 PowerShell 配置自定义响应状态代码和消息

### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境

Azure PowerShell 提供一组可以使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](/powershell/azure/) 并在任何 PowerShell 会话中使用它。 遵照页面上的说明使用 Azure 凭据登录，并安装 Az PowerShell 模块。

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用交互式登录对话框连接到 Azure

```
Connect-AzAccount
Install-Module -Name Az

```
确保已安装 PowerShellGet 最新版本。 运行下面的命令，重新打开 PowerShell。
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 在这里，我们使用 [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) 创建资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>使用自定义响应创建新的 WAF 策略 

下面的示例创建了一个新的 WAF 策略，并将自定义响应状态代码设置为 405，并且使用 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 发送“你被阻止”的消息。

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

使用 [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy) 修改现有 WAF 策略的自定义响应代码或响应正文设置。

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Front Door 的 Web 应用程序防火墙](../afds/afds-overview.md)