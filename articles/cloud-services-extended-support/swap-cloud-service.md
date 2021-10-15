---
title: 在 Azure 云服务（外延支持）中交换或切换部署
description: 了解如何在 Azure 云服务（外延支持）中交换或切换部署。
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.openlocfilehash: cd13a7f69d3085786407a405598df4bc7b8e0ef9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358432"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>在 Azure 云服务（外延支持）中交换或切换部署

在 Azure 云服务（外延支持）中，你可以在两个独立云服务部署之间进行交换。 与 Azure 云服务（经典）不同，Azure 云服务（外延支持）中的 Azure 资源管理器模型不使用部署槽位。 在 Azure 云服务（外延支持）中，当你部署新版云服务时，可以将其设置为可与 Azure 云服务（外延支持）中的现有云服务进行“交换”。

交换部署后，可以使用新的云服务部署来暂存和测试新版本。 实际上，交换促进了一种新的云服务，并将其暂存到生产版本中。

> [!NOTE]
> 无法在 Azure 云服务（经典）部署与 Azure 云服务（外延支持）部署之间进行交换。

首次部署一对云服务中的第二个时，必须将该云服务设置为可与另一个云服务交换。 部署第二对云服务后，不可在后续更新中使用现有的云服务与其进行交换。

可以通过使用 Azure 资源管理器模板（ARM 模板）、Azure 门户或 REST API 交换部署。

部署第二个云服务时，两个云服务都会将其 SwappableCloudService 属性设置为指向彼此。 对这些云服务进行的任何后续更新都需要指定此属性失败，即系统将返回错误，指示无法删除或更新 SwappableCloudService 属性。

设置后的 SwappableCloudService 属性将被视为只读。 不得将该属性删除或更改为其他值。 删除（可交换云服务对的）其中一个云服务将导致其余云服务的 SwappableCloudService 属性遭到清除。

## <a name="arm-template"></a>ARM 模板

如果使用 ARM 模板部署方法将云服务设置为可交换，请将 `cloudServices` 对象的 `networkProfile` 中的 `SwappableCloudService` 属性设置为配对云服务的 ID。

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中交换部署：

1. 在门户菜单中，选择“云服务(外延支持)”或“仪表板” 。
1. 选择要更新的云服务。
1. 在云服务的“概述”中，选择“交换” ：

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="显示云服务的“交换”选项卡的屏幕截图。":::

1. 在交换确认窗格中，验证部署信息，然后选择“确定”以交换部署：

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="显示确认部署交换信息的屏幕截图。":::

部署交换的速度很快，因为只有部署的云服务的虚拟 IP 地址发生了变化。

若要节省计算成本，可在验证交换的云服务是否按预期方式工作后，删除（已指定为应用程序部署过渡环境）的云服务。

## <a name="rest-api"></a>REST API

若要使用 [REST API](/rest/api/compute/load-balancers/swap-public-ip-addresses) 切换到 Azure 云服务（外延支持）中的新云服务部署，请使用以下命令和 JSON 配置：

```http
POST https://management.azure.com/subscriptions/subid/providers/Microsoft.Network/locations/westus/setLoadBalancerFrontendPublicIpAddresses?api-version=2021-02-01
```

```json
{
  "frontendIPConfigurations": [
    {
      "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb1/frontendIPConfigurations/lbfe1",
      "properties": {
        "publicIPAddress": {
          "id": "/subscriptions/subid/resourceGroups/rg2/providers/Microsoft.Network/publicIPAddresses/pip2"
        }
      }
    },
    {
      "id": "/subscriptions/subid/resourceGroups/rg2/providers/Microsoft.Network/loadBalancers/lb2/frontendIPConfigurations/lbfe2",
      "properties": {
        "publicIPAddress": {
          "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/publicIPAddresses/pip1"
        }
      }
    }
  ]
}
```

## <a name="common-questions-about-swapping-deployments"></a>有关交换部署的常见问题

查看以下关于 Azure 云服务（外延支持）中部署交换的常见问题的答案。

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>切换到新的云服务部署的先决条件是什么？

若要在 Azure 云服务（外延支持）中成功进行部署交换，必须满足两个重要的先决条件：

* 如果要将静态或保留 IP 地址用于某个可交换云服务，则其他云服务也必须使用保留 IP 地址。 否则，交换将失败。
* 角色的所有实例都必须正在运行，交换才能成功。 若要检查实例的状态，请在 Azure 门户中，转到新部署云服务的“概述”，或使用 Windows Powershell 中的 `Get-AzRole` 命令。

来宾 OS 更新和服务修复操作可能导致部署交换失败。 有关详细信息，请参阅[排查云服务部署问题](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)。

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>能否与其他变异操作并行执行 VIP 交换？

不是。 VIP 交换是指仅限网络的更改，必须在在云服务上开始任何其他计算操作之前完成。 在 VIP 交换期间开始执行云服务的更新、删除或自动缩放操作，或在另一个计算操作正在进行时触发 VIP 交换，都可能会使云服务出现无法恢复的错误。

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>交换是否会导致应用程序停机？应如何处理它？

云服务交换通常非常快，因为它只更改 Azure 负载均衡器中的配置。 在某些情况下，交换可能需要 10 秒或更长时间并导致暂时性连接故障。 若要减少交换对用户的影响，请考虑实施客户端重试逻辑。

## <a name="next-steps"></a>后续步骤 

* 查看部署 Azure 云服务（外延支持）的[先决条件](deploy-prerequisite.md)。
* 查看 Azure 云服务（外延支持）的[常见问题解答](faq.yml)。
* 使用以下选项之一部署 Azure 云服务（外延支持）云服务：
  * [Azure 门户](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM 模板](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
