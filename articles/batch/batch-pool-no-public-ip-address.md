---
title: 创建不具有公共 IP 地址的 Azure Batch 池
description: 了解如何创建不具有公共 IP 地址的池
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 22c9163b0b8e809fba3c870393c03dd7c0d3c194
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433753"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>创建不具有公共 IP 地址的 Azure Batch 池

创建 Azure Batch 池时，可以预配没有公共 IP 地址的虚拟机配置池。 本文介绍如何设置没有公共 IP 地址的 Batch 池。

## <a name="why-use-a-pool-without-public-ip-addresses"></a>为何使用不具有公共 IP 地址的池？

默认情况下，将为 Azure Batch 虚拟机配置池中的所有计算节点分配一个公共 IP 地址。 Batch 服务使用此地址来计划任务以及与计算节点的通信，包括对 Internet 的出站访问。

要限制对这些节点的访问并减少从 Internet 发现的这些节点的能力，可以预配无公共 IP 地址的池。

> [!IMPORTANT]
> 以下区域中，支持 Azure Batch 中没有公共 IP 地址的池，但这种支持目前处于公共预览版：法国中部、东亚、美国西部、美国中南部、美国西部 2、美国东部、北欧、美国东部 2、美国中部、西欧、美国中北部、美国西部、澳大利亚东部、日本东部、日本西部。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

- **身份验证**。 要在[虚拟网络](./batch-virtual-network.md)中使用没有公共 IP 地址的池，则批处理客户端 API 必须使用 Azure Active Directory (AD) 身份验证。 有关 Azure AD 的 Azure Batch 支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。 如果不是在虚拟网络中创建池，则可以使用 Azure AD 身份验证，也可以使用基于密钥的身份验证。

- **一个 Azure VNet**。 如果要在[虚拟网络](batch-virtual-network.md)中创建池，请遵循以下要求和配置。 若要提前准备具有一个或多个子网的 VNet，可以使用 Azure 门户、Azure PowerShell、Azure 命令行接口 (CLI) 或其他方法。
  - VNet 必须与用于创建池的 Batch 帐户位于同一订阅和区域中。
  - 为池指定的子网必须提供足够的未分配 IP 地址来容纳面向该池的 VM 的数量；即，池的 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 属性的总和。 如果子网没有足够的未分配 IP 地址，池将分配部分计算节点，并发生调整大小错误。
  - 必须禁用专用链接服务和终结点网络策略。 这可以使用 Azure CLI 完成：```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resource-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> 对于每 100 个专用或低优先级节点，Batch 会分配一个专用链路服务和一个负载平衡器。 这些资源受订阅的[资源配额](../azure-resource-manager/management/azure-subscription-service-limits.md)限制。 对于大型池，可能需要为一个或多个此类资源[请求增加配额](batch-quota-limit.md#increase-a-quota)。 此外，不得将资源锁应用于由 Batch 创建的任何资源，因为这可能会由于用户启动的操作（如删除池或者将池大小调整为零）而导致资源清理被阻止。

## <a name="current-limitations"></a>当前限制

1. 没有公共 IP 地址的池必须使用虚拟机配置而不是云服务配置。
1. 对于没有公共 IP 地址的池，Batch 计算节点的[自定义终结点配置](pool-endpoint-configuration.md)不起作用。
1. 由于没有公共 IP 地址，因此不能[将自己指定的公共 IP 地址用于](create-pool-public-ip.md)此类型的池。

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>在 Azure 门户中创建没有公共 IP 地址的池

1. 导航到 Azure 门户中的批处理帐户。
1. 在左侧的“设置”窗口中，选择“池”。
1. 在“池”窗口中，选择“添加”。 
1. 在“添加池”窗口中，从“映像类型”下拉列表中选择要使用的选项。 
1. 为映像选择正确的发布服务器/付费选项/SKU。
1. 指定剩余所需设置，包括“节点大小”、“目标专用节点”和“低优先级节点”，以及任何所需的可选设置。  
1. （可选）选择要使用的虚拟网络和子网。 此虚拟网络必须与要创建的池位于同一资源组中。
1. 在“IP 地址预配类型”中，选择“NoPublicIPAddresses”。 

![选择了 NoPublicIPAddresses 的“添加池”屏幕的屏幕快照。](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>使用 Batch REST API 来创建没有公共 IP 地址的池

下面的示例展示了如何使用 [Azure Batch REST API](/rest/api/batchservice/pool/add) 来创建使用公共 IP 地址的池。

### <a name="rest-api-uri"></a>REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>请求正文

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>对 Internet 的出站访问

在没有公共 IP 地址的池中，你的虚拟机将无法访问公共 Internet，除非你适当地配置了网络设置，例如使用[虚拟网络 NAT](../virtual-network/nat-gateway/nat-overview.md)。 请注意，NAT 仅允许从虚拟网络中的虚拟机对 Internet 进行出站访问。 Batch 创建的计算节点将无法被公开访问，因为这些计算节点没有关联的公共 IP 地址。

提供出站连接的另一种方法是使用用户定义的路由 (UDR)。 这使你可以将流量路由到具有公共 Internet 访问权限的代理计算机。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[在虚拟网络中创建池](batch-virtual-network.md)。
- 了解如何[对 Batch 帐户使用专用终结点](private-connectivity.md)。
