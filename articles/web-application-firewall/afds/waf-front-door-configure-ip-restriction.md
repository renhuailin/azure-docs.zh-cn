---
title: 为 Azure Front Door 配置 IP 限制 WAF 规则
description: 了解如何配置 Web 应用程序防火墙规则，以限制现有 Azure Front Door 终结点的 IP 地址。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 65e378c0380804c13e4b42d855aede7781b93592
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211662"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>配置适用于 Azure Front Door 的 Web 应用程序防火墙的 IP 限制规则

本文演示如何使用 Azure 门户、Azure CLI、Azure PowerShell 或 Azure 资源管理器模板在适用于 Azure Front Door 的 Web 应用程序防火墙 (WAF) 中配置 IP 限制规则。

基于 IP 地址的访问控制规则是一个自定义 WAF 规则，可用于控制对 Web 应用程序的访问。 为此，它指定无类别域际路由 (CIDR) 格式的 IP 地址列表或 IP 地址范围列表。 IP 地址匹配中有两种类型的匹配变量：RemoteAddr 和 SocketAddr 。 RemoteAddr 是通常通过 X-Forwarded-For 请求标头发送的原始客户端 IP。 SocketAddr 是 WAF 看到的源 IP 地址。 如果用户位于代理后，SocketAddr 通常是代理服务器地址。

默认情况下，可从 Internet 访问 Web 应用程序。 如果要限制从已知 IP 地址列表或 IP 地址范围列表访问的客户端，可以创建一个 IP 匹配规则，其中包含 IP 地址列表作为匹配值，并将运算符设置为“Not”（求反为 true）且操作设置为“阻止”。 应用 IP 限制规则后，源自此允许列表外部的地址的请求将收到“403 禁止访问”响应。

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>通过 Azure 门户配置 WAF 策略

### <a name="prerequisites"></a>先决条件

按照[快速入门：创建 Front Door，以实现高度可用的全局 Web 应用程序](../../frontdoor/quickstart-create-front-door.md)中所述的介绍创建 Azure Front Door 配置文件。

### <a name="create-a-waf-policy"></a>创建 WAF 策略

1. 在 Azure 门户中，选择“创建资源”，在搜索框中键入“Web 应用程序防火墙”，然后选择“Web 应用程序防火墙(WAF)”  。
2. 选择“创建”  。
3. 在“创建 WAF 策略”页上，使用以下值完成“基本信息”选项卡 ：
   
   |设置  |“值”  |
   |---------|---------|
   |策略适用于     |全球 WAF (Front Door)|
   |订阅     |选择订阅|
   |资源组     |选择 Front Door 所在的资源组。|
   |策略名称     |为策略键入名称|
   |策略状态     |Enabled|

   选择“下一步: 策略设置”

1. 在“策略设置”选项卡上，选择“防护” 。 对于“阻止响应正文”，键入“你已受到阻止!” 这样你便可以看到自定义规则已生效。
2. 选择“下一步: 托管规则”。
3. 选择“下一步: 自定义规则”。
4. 选择“添加自定义规则”。
5. 在“添加自定义规则”页上，使用以下测试值创建自定义规则：

   |设置  |“值”  |
   |---------|---------|
   |自定义规则名称     |FdWafCustRule|
   |状态     |Enabled|
   |规则类型     |匹配|
   |优先级    |100|
   |匹配类型     |IP 地址|
   |匹配变量|RemoteAddr|
   |操作|不包含|
   |IP 地址或范围|10.10.10.0/24|
   |Then|拒绝流量|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="自定义规则":::

   选择“添加”。
6. 选择“下一步: 关联”。
7. 选择“添加前端主机”。
8. 对于“前端主机”，选择你的前端主机，然后选择“添加” 。
9. 选择“查看 + 创建”。
10. 通过策略验证后，选择“创建”。

### <a name="test-your-waf-policy"></a>测试 WAF 策略

1. WAF 策略部署完成后，浏览到你的 Front Door 前端主机名。
2. 应看到自定义阻止消息。

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 规则测试":::

   > [!NOTE]
   > 专用 IP 地址专门用于自定义规则中，以确保规则会触发。 在实际部署中，针对特定情况使用 IP 地址创建“允许”和“拒绝”规则 。

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>通过 Azure CLI 配置 WAF 策略

### <a name="prerequisites"></a>先决条件
在开始配置 IP 限制策略之前，请设置 CLI 环境并创建 Azure Front Door 配置文件。

#### <a name="set-up-the-azure-cli-environment"></a>设置 Azure CLI 环境
1. 安装 [Azure CLI](/cli/azure/install-azure-cli)，或使用 Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 选择下面的 CLI 命令中的“试用”按钮，然后在打开的 Cloud Shell 会话中登录到 Azure 帐户。 会话启动后，输入 `az extension add --name front-door` 以添加 Azure Front Door 扩展。
 2. 如果在 Bash 中本地使用 CLI，请使用 `az login` 登录到 Azure。

#### <a name="create-an-azure-front-door-profile"></a>创建 Azure Front Door 配置文件
按照[快速入门：创建 Front Door，以实现高度可用的全局 Web 应用程序](../../frontdoor/quickstart-create-front-door.md)中所述的介绍创建 Azure Front Door 配置文件。

### <a name="create-a-waf-policy"></a>创建 WAF 策略

使用 [az network front-door waf-policy create](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-create) 命令创建 WAF 策略。 在下面的示例中，将策略名称 IPAllowPolicyExampleCLI 替换为唯一策略名称。

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>添加自定义 IP 访问控制规则

使用 [az network front-door waf-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule#ext-front-door-az-network-front-door-waf-policy-rule-create) 命令为刚创建的 WAF 策略添加自定义 IP 访问控制规则。

在以下示例中：
-  将 IPAllowPolicyExampleCLI 替换为前面创建的唯一策略。
-  将 ip-address-range-1、ip-address-range-2 替换为你自己的范围 。

首先，为在上一步中创建的策略创建 IP 允许规则。 
> [!NOTE]
> --defer 是必需的，因为规则必须具有匹配条件才能在下一步中添加。

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
接下来，向规则添加匹配条件：

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>查找 WAF 策略的 ID 
使用 [az network front-door waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-show) 命令查找 WAF 策略的 ID。 将以下示例中的 IPAllowPolicyExampleCLI 替换为前面创建的唯一策略。

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>将 WAF 策略链接到 Azure Front Door 前端主机

使用 [az network front-door update](/cli/azure/ext/front-door/network/front-door#ext-front-door-az-network-front-door-update) 命令将 Azure Front Door WebApplicationFirewallPolicyLink ID 设置为策略 ID。 将 IPAllowPolicyExampleCLI 替换为前面创建的唯一策略。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此示例中，WAF 策略被应用于 FrontendEndpoints[0]。 可以将 WAF 策略链接到任何前端。
> [!Note]
> 仅需设置 WebApplicationFirewallPolicyLink 属性一次，即可将 WAF 策略链接到 Azure Front Door 前端。 后续策略更新会自动应用到前端。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 配置 WAF 策略

### <a name="prerequisites"></a>先决条件
在开始配置 IP 限制策略之前，请设置 PowerShell 环境并创建 Azure Front Door 配置文件。

#### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)模型来管理 Azure 资源的 cmdlet。

可以在本地计算机上安装 [Azure PowerShell](/powershell/azure/) 并在任何 PowerShell 会话中使用它。 遵照页面上的说明使用 Azure 凭据登录到 PowerShell，然后安装 Az 模块。

1. 使用以下命令连接到 Azure，然后使用交互式对话框登录。
    ```
    Connect-AzAccount
    ```
 2. 在安装 Azure Front Door 模块之前，请确保安装最新版本的 PowerShellGet 模块。 运行下面的命令，然后重新打开 PowerShell。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 使用以下命令安装 Az.FrontDoor 模块。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>创建 Azure Front Door 配置文件
按照[快速入门：创建 Front Door，以实现高度可用的全局 Web 应用程序](../../frontdoor/quickstart-create-front-door.md)中所述的介绍创建 Azure Front Door 配置文件。

### <a name="define-an-ip-match-condition"></a>定义 IP 匹配条件
使用 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 命令定义 IP 匹配条件。
在以下示例中，将 ip-address-range-1、ip-address-range-2 替换为你自己的范围 。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>创建自定义 IP 允许规则

使用 [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) 命令定义操作并设置优先级。 在下面的示例中，将阻止与列表匹配但不是来自客户端 IP 的请求。

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>配置 WAF 策略
使用 `Get-AzResourceGroup` 找到包含该 Azure Front Door 配置文件的资源组的名称。 接下来，使用 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 将 WAF 策略配置 IP 规则。

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>将 WAF 策略链接到 Azure Front Door 前端主机

将 WAF 策略对象链接到现有前端主机并更新 Azure Front Door 属性。 首先，使用 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 检索 Azure Front Door 对象。 接下来，使用 [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 命令将 WebApplicationFirewallPolicyLink 属性设置为在上一步中创建的 $IPAllowPolicyExamplePS 的资源 ID。

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此示例中，WAF 策略被应用于 FrontendEndpoints[0]。 可以将 WAF 策略链接到任何前端。 仅需设置 WebApplicationFirewallPolicyLink 属性一次，即可将 WAF 策略链接到 Azure Front Door 前端。 后续策略更新会自动应用到前端。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>使用资源管理器模板配置 WAF 策略
若要查看使用自定义 IP 限制规则创建 Azure Front Door 和 WAF 策略的模板，请转到 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure Front Door 配置文件](../../frontdoor/quickstart-create-front-door.md)。
