---
title: 跨可用性区域创建池
description: 了解如何创建具有区域性策略的 Batch 池来帮助防范故障。
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: 0abec404b0ec951bb398cfb06ae41290e986a241
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741128"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>跨可用性区域创建 Azure Batch 池

支持[可用性区域](https://azure.microsoft.com/global-infrastructure/availability-zones/)的 Azure 区域最少具有三个单独的区域，每个都有其自己的独立电源、网络和冷却系统。 在使用虚拟机配置创建 Azure Batch 池时，可以选择跨可用性区域预配 Batch 池。 创建具有此区域性策略的池有助于 Batch 计算节点防范 Azure 数据中心级别的故障。

例如，可以在支持三个可用性区域的 Azure 区域中创建具有区域性策略的池。 如果一个可用性区域中的 Azure 数据中心出现基础结构故障，Batch 池在另外两个可用性区域中仍会具有正常运行的节点，因此，该池仍可用于进行任务计划。

## <a name="regional-support-and-other-requirements"></a>区域性支持和其他要求

Batch 在支持可用性区域时会维护与 Azure 的奇偶校验。 若要使用区域性选项，必须在[受支持的 Azure 区域](../availability-zones/az-region.md)中创建池。

为了跨可用性区域分配 Batch 池，你在其中创建池的 Azure 区域必须在多个区域中支持请求的 VM SKU。 可以通过调用[资源 Sku 列表 API](/rest/api/compute/resourceskus/list) 并检查 [resourceSku](/rest/api/compute/resourceskus/list#resourcesku) 的“locationInfo”字段对此进行验证。 请确保请求的 VM SKU 支持多个区域。

对于[用户订阅模式 Batch 帐户](accounts.md#batch-accounts)，请确保你在其中创建池的订阅在请求的 VM SKU 上没有区域产品/服务限制。 若要确认这一点，请调用[资源 SKU 列表 API](/rest/api/compute/resourceskus/list) 并检查 [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions)。 如果存在区域限制，可以提交[支持票证](/troubleshoot/azure/general/region-access-request-process)来删除区域限制。

另请注意，如果池已经启用了节点间通信并且使用[支持 InfiniBand 的 VM SKU](../virtual-machines/workloads/hpc/enable-infiniband.md)，则无法创建具有区域性策略的池。

## <a name="create-a-batch-pool-across-availability-zones"></a>跨可用性区域创建 Batch 池

下面的示例演示如何跨可用性区域创建 Batch 池。

> [!NOTE]
> 在使用区域性策略创建池时，Batch 服务会尝试跨所选区域中的所有可用性区域来分配池；你无法跨区域指定特定的分配。

### <a name="batch-management-client-net-sdk"></a>Batch 管理客户端 .NET SDK

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
- 了解如何[创建没有公共 IP 地址的 Azure Batch 池](./batch-pool-no-public-ip-address.md)。
