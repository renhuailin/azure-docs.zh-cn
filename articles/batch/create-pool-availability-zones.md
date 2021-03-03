---
title: 在可用性区域之间创建池
description: 了解如何创建具有区域性策略的批处理池来帮助防止故障。
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725282"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>跨可用性区域创建 Azure Batch 池

支持 [可用性区域](https://azure.microsoft.com/global-infrastructure/availability-zones/) 的 Azure 区域至少有三个独立的区域，每个区域都具有自己的独立电源、网络和冷却系统。 使用虚拟机配置创建 Azure Batch 池时，可以选择在可用性区域中设置 Batch 池。 通过此区域策略创建池有助于保护 Batch 计算节点的 Azure 数据中心级故障。

例如，可以在支持三个可用性区域的 Azure 区域中创建具有区域性策略的池。 如果一个可用性区域中的 Azure 数据中心出现基础结构故障，则在其他两个可用性区域中，批处理池仍具有正常运行的节点，因此池仍可用于任务计划。

## <a name="regional-support-and-other-requirements"></a>区域支持和其他要求

批处理在支持可用性区域上维护与 Azure 的奇偶校验。 若要使用区域性选项，必须在 [受支持的 Azure 区域](../availability-zones/az-region.md)中创建池。

为了在可用性区域之间分配批处理池，创建池的 Azure 区域必须在多个区域中支持请求的 VM SKU。 可以通过调用 [资源 Sku 列表 API](/rest/api/compute/resourceskus/list)并检查 [ResourceSku](/rest/api/compute/resourceskus/list#resourcesku)的 " **locationInfo** " 字段来对此进行验证。 确保请求的 VM SKU 支持多个区域。

对于 [用户订阅模式批处理帐户](accounts.md#batch-accounts)，请确保要在其中创建池的订阅在请求的 VM SKU 上没有区域提供限制。 若要确认这一点，请调用 [资源 Sku 列表 API](/rest/api/compute/resourceskus/list) 并检查 [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions)。 如果存在区域限制，则可以提交 [支持票证](/troubleshoot/azure/general/region-access-request-process) 来删除区域限制。

另请注意，如果池已启用节点间通信，并使用支持不允许的 [VM SKU](../virtual-machines/workloads/hpc/enable-infiniband.md)，则无法使用该区域策略创建池。

## <a name="create-a-batch-pool-across-availability-zones"></a>跨可用性区域创建批处理池

下面的示例演示如何在可用性区域中创建批处理池。

> [!NOTE]
> 使用区域性策略创建池时，批处理服务将尝试跨所选区域中的所有可用性区域分配池;不能跨区域指定特定分配。

### <a name="batch-management-client-net-sdk"></a>Batch Management 客户端 .NET SDK

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

请求正文

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)（如池、节点、作业和任务）。
- 了解如何[在 Azure 虚拟网络的子网中创建池](batch-virtual-network.md)。
- 了解如何 [创建不带公共 IP 地址的 Azure Batch 池](./batch-pool-no-public-ip-address.md)。