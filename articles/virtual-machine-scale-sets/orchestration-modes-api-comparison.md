---
title: 业务流程模式 API 比较
description: 了解统一业务流程模式与灵活业务流程模式之间的 API 差异。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 20278ebae9e590a99293c37df506b7d07ff79ddc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868282"
---
# <a name="preview-orchestration-modes-api-comparison"></a>预览：业务流程模式 API 比较 

本文比较虚拟机规模集的统一业务流程模式与[灵活业务流程](..\virtual-machines\flexible-virtual-machine-scale-sets.md)模式之间的 API 差异。 若要详细了解统一和灵活的虚拟机规模集，请参阅[业务流程模式](virtual-machine-scale-sets-orchestration-modes.md)。

> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="instance-view"></a>实例视图

| 统一 API | 灵活的替代项 |
|-|-|
| 虚拟机规模集实例视图 | 获取单个 VM 的实例视图；使用 Resource Graph 查询电源状态 |


## <a name="scale-set-lifecycle-batch-operations"></a>规模集生命周期批处理操作  

| 统一 API | 灵活的替代项 |
|-|-|
| 虚拟机规模集 VM 生命周期批处理操作：  | 对特定实例调用单个 VM API： |
| [解除分配](/rest/api/compute/virtualmachinescalesetvms/deallocate)  | [调用单个 VM API - 解除分配](/rest/api/compute/virtualmachines/deallocate)   |
| [删除](/rest/api/compute/virtualmachinescalesetvms/delete)  | [调用单个 VM API - 删除](/rest/api/compute/virtualmachines/delete)  |
| [获取实例视图](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)  | [调用单个 VM API - 实例视图](/rest/api/compute/virtualmachines/instanceview)  |
| [执行维护](/rest/api/compute/virtualmachinescalesetvms/performmaintenance)  | [调用单个 VM API - 执行维护](/rest/api/compute/virtualmachines/performmaintenance)  |
| [关闭电源](/rest/api/compute/virtualmachinescalesetvms/poweroff)  | [调用单个 VM API - 关闭电源](/rest/api/compute/virtualmachines/poweroff)  |
| [重新部署](/rest/api/compute/virtualmachinescalesetvms/redeploy)  | [调用单个 VM API - 重新部署](/rest/api/compute/virtualmachines/redeploy)  |
| [重置映像](/rest/api/compute/virtualmachinescalesetvms/reimage)  | [调用单个 VM API - 重置映像](/rest/api/compute/virtualmachines/reimage)  |
| [全部重置映像](/rest/api/compute/virtualmachinescalesetvms/reimageall)  | 不适用 |
| [重启](/rest/api/compute/virtualmachinescalesetvms/restart)  | [调用单个 VM API - 重启](/rest/api/compute/virtualmachines/restart)  |
| [模拟逐出](/rest/api/compute/virtualmachinescalesetvms/simulateeviction) | [调用单个 VM API - 模拟逐出](/rest/api/compute/virtualmachines/simulateeviction)  |
| [启动](/rest/api/compute/virtualmachinescalesetvms/start) | [调用单个 VM API - 启动](/rest/api/compute/virtualmachines/start) |


## <a name="get-or-update"></a>Get 或 Update 

### <a name="uniform-api"></a>统一 API
虚拟机规模集 VM Get 或 Update 实例：
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [更新](/rest/api/compute/virtualmachinescalesetvms/update)

### <a name="flexible-alternative"></a>灵活的替代项 
调用单个 VM API：
- [ARM 锁定资源](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources?tabs=json)，用于实例保护类型行为 


## <a name="list-instances"></a>列出实例 

### <a name="uniform-api"></a>统一 API
`VMSS List Instances`: 
- 返回在此预览期间与每个实例关联的规模集 ID 

### <a name="flexible-alternative"></a>灵活的替代项
Azure Resource Graph： 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-operations"></a>规模集操作 

### <a name="uniform-api"></a>统一 API
虚拟机规模集操作：
- [更新实例](/rest/api/compute/virtual-machine-scale-sets/update-instances)
- [解除分配](/rest/api/compute/virtual-machine-scale-sets/deallocate)
- [执行维护](/rest/api/compute/virtual-machine-scale-sets/perform-maintenance)
- [关闭电源](/rest/api/compute/virtual-machine-scale-sets/power-off)
- [重新部署](/rest/api/compute/virtual-machine-scale-sets/redeploy)
- [重置映像](/rest/api/compute/virtual-machine-scale-sets/reimage)
- [全部重置映像](/rest/api/compute/virtual-machine-scale-sets/reimage-all)
- [重新启动](/rest/api/compute/virtual-machine-scale-sets/restart)
- [设置业务流程服务状态](/rest/api/compute/virtual-machine-scale-sets/set-orchestration-service-state)
- [开始](/rest/api/compute/virtual-machine-scale-sets/start)

### <a name="flexible-alternative"></a>灵活的替代项
对单个 VM 调用操作。


## <a name="vm-extension"></a>VM 扩展

### <a name="uniform-api"></a>统一 API
虚拟机规模集 VM 扩展：
- [创建或更新](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [删除](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [列表](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [更新](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

### <a name="flexible-alternative"></a>灵活的替代项
对单个 VM 调用操作。


## <a name="networking"></a>网络 

### <a name="uniform-api"></a>统一 API
- NAT 池/端口转发 
- 灵活规模集中不支持的 NAT 池  

### <a name="flexible-alternative"></a>灵活的替代项
- 对每个 VM 设置单独的 NAT 规则


## <a name="scale-set-apis"></a>规模集 API

### <a name="uniform-api"></a>统一 API
统一虚拟机规模集 API：
- [转换为单个放置组](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [强制执行恢复 Service Fabric 平台更新域演练](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

### <a name="flexible-alternative"></a>灵活的替代项
在灵活虚拟机规模集上不受支持。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解不同的业务流程模式](virtual-machine-scale-sets-orchestration-modes.md)