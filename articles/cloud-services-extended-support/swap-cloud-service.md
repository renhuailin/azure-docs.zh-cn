---
title: 在两个 Azure 云服务（外延支持）之间交换/切换
description: 在两个 Azure 云服务（外延支持）之间交换/切换
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294197"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>在两个 Azure 云服务（外延支持）之间交换/切换
使用云服务（外延支持），你可以在两个独立云服务部署之间进行交换。 与云服务（经典）不同，Azure 资源管理器模型中不存在槽的概念。 当你决定部署新版云服务（外延支持）时，可将其与另一现有云服务（外延支持）进行“交换”，以便使用此部署来暂存和测试新版云服务。 仅当部署第二个云服务时，云服务才可与其他云服务进行“交换”。 使用基于 ARM 模板的部署方法时，可将云服务对象的“网络配置文件”中的“SwappableCloudService”属性设置为已配对云服务的 ID，从而完成此操作。 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> 云服务（经典）不得与云服务（外延支持）进行交换

使用“交换”功能来切换处理两个云服务的 URL，推动新云服务过渡到正式版本。
可以通过“云服务”页或仪表板交换部署。

1. 在 [Azure 门户](https://portal.azure.com)中，选择要更新的云服务。 此步骤打开云服务实例边栏选项卡。
2. 在边栏选项卡上，选择“交换”
   :::image type="content" source="media/swap-cloud-service-1.png" alt-text="显示云服务“交换”选项的图像":::
   
3. 将打开以下确认提示
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="显示云服务交换的图像":::
   
4. 验证部署信息后，选择“确定”  交换部署。
交换速度很快，因为两个云服务只有虚拟 IP 地址 (VIP) 发生了变化。

若要节省计算成本，可在验证交换的云服务是否按预期方式工作后，删除（已指定为应用程序部署过渡环境）的云服务。

在两个云服务（外延支持）部署之间执行“交换”的 rest API 如下所示：
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>有关交换部署的常见问题

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>两个云服务进行交换的先决条件是什么？
云服务（外延支持）成功交换的两个重要先决条件：
* 如果要将静态/保留 IP 地址用于某个可交换的云服务，则其他云服务也必须使用保留 IP。 否则，交换将失败。
* 在执行交换之前，角色的所有实例必须都在运行。 可在 Azure 门户的“概述”  边栏选项卡中检查实例的状态。 或者，也可以使用 Windows PowerShell 中的 Get-AzRole 命令。

请注意，来宾 OS 更新和服务修复操作也会导致部署交换失败。 有关详细信息，请参阅排查云服务部署问题。

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>能否与其他变异操作并行执行 VIP 交换？
不是。 VIP 交换是指需要（在云服务上执行任何其他计算操作之前）完成的仅限网络的更改。 在 VIP 交换期间在云服务上执行更新、删除或自动缩放操作，或在另一个计算操作正在进行时触发 VIP 交换，都可能会使云服务出现异常，而且这可能无法恢复。 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>应用程序的交换是否会导致停机？ 应如何处理它？
如前一部分中所述，云服务交换通常非常快，因为它只更改 Azure 负载均衡器中的配置。 在某些情况下，它会需要 10 秒或更长时间并导致暂时性连接故障。 若要减少对客户的影响，请考虑实施客户端重试逻辑。

## <a name="next-steps"></a>后续步骤 
- 查看部署云服务（外延支持）的[先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.md)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
