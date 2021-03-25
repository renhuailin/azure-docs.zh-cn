---
title: 使用 Azure 资源管理器模板（ARM 模板）创建和启用 Azure DDoS 防护计划。
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）创建和启用 Azure DDoS 防护计划。
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99092494"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>快速入门：使用 ARM 模板创建 Azure DDoS 保护标准

此快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建分布式拒绝服务 (DDoS) 防护计划和虚拟网络 (VNet)，然后为 VNet 启用防护计划。 Azure DDoS 防护标准计划在订阅中定义一组已启用 DDoS 防护的虚拟网络。 可以为组织配置一个 DDoS 防护计划，然后从多个订阅将虚拟网络链接到相同计划。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans)。

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

该模板定义了两个资源：

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>部署模板

在本示例中，模板会新建资源组、DDoS 防护计划和 VNet。

1. 要登录到 Azure 并打开模板，请选择“部署到 Azure”按钮。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. 输入值以新建资源组、DDoS 防护计划和 VNet 名称。

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS 快速入门模板。":::

    - 订阅：在其中部署资源的 Azure 区域的名称。
    - 资源组：选择现有资源组或创建新资源组。
    - **区域**：在其中部署资源组的区域，例如美国东部。
    - **Ddos 防护计划名称**：新 DDoS 防护计划的名称。
    - **虚拟网络名称**：创建新 VNet 的名称。
    - 位置：使用资源组所在区域部署资源的函数。
    - **Vnet 地址前缀**：使用默认值或输入 VNet 地址。
    - **子网前缀**：使用默认值或输入 VNet 子网。
    - **启用 Ddos 防护计划**：默认为 `true`，以启用 DDoS 防护计划。

1. 选择“查看 + 创建”。
1. 验证是否已通过模板验证，然后选择“创建”以开始部署。

## <a name="review-deployed-resources"></a>查看已部署的资源

要复制 Azure CLI 或 Azure PowerShell 命令，请选择“复制”按钮。 单击“试用”按钮打开 Azure Cloud Shell 并运行命令。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

输出显示新资源。

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>清理资源

完成后，可以删除资源。 该命令将删除资源组及其包含的所有资源。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)
