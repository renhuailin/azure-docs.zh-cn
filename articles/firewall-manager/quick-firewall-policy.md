---
title: 快速入门：创建 Azure 防火墙和防火墙策略 - 资源管理器模板
description: 在本快速入门中将部署 Azure 防火墙和防火墙策略。
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom: devx-track-azurepowershell - subject-armqs - mode-arm
ms.openlocfilehash: 9ce29e4ae856ce73b046fe4b4138fe85d48b0a03
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112004990"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>快速入门：创建 Azure 防火墙和防火墙策略 - ARM 模板

在本快速入门中将使用 Azure 资源管理器模板（ARM 模板）创建 Azure 防火墙和防火墙策略。 防火墙策略具有允许连接到 `www.microsoft.com` 的应用程序规则，还有允许使用 WindowsUpdate FQDN 标记连接到 Windows 更新的规则。 网络规则允许 UDP 连接到位于 13.86.101.172 的时间服务器。

此外，规则中的 IP 组用于定义源 IP 地址。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

有关 Azure 防火墙管理器的信息，请参阅[什么是 Azure 防火墙管理器？](overview.md)。

有关 Azure 防火墙的信息，请参阅[什么是 Azure 防火墙？](../firewall/overview.md)。

有关 IP 组的信息，请参阅 [Azure 防火墙中的 IP 组](../firewall/ip-groups.md)。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

此模板创建一个中心虚拟网络，以及支持该方案所需的资源。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>部署模板

将 ARM 模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 此模板会创建 Azure 防火墙、虚拟 WAN 和虚拟中心、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. 在门户中的“创建使用规则和 Ipgroups 的防火墙和防火墙策略”页上，键入或选择以下值：
   - 订阅：从现有订阅中选择。
   - 资源组：从现有资源组中选择，或者选择“新建”，然后选择“确定”。 
   - 区域：选择区域。
   - 防火墙名称：键入防火墙的名称。

3. 选择“查看 + 创建”，然后选择“创建” 。 部署可能需要 10 分钟或更长时间才能完成。

## <a name="review-deployed-resources"></a>查看已部署的资源

部署完成后，将看到以下类似资源。

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="已部署资源":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要为防火墙创建的资源，请删除资源组。 这会删除该防火墙和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 防火墙管理器策略概述](policy-overview.md)
