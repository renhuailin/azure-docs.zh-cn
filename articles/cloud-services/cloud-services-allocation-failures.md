---
title: 排除云服务（经典）分配故障 | Microsoft Docs
description: 对部署 Azure 云服务时的分配失败进行故障排除。 了解分配的工作原理以及分配失败的原因。
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f8aaff0cf7261dc8fa845a178488dd449066ff85
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824792"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>对在 Azure 中部署云服务（经典）时的分配失败进行故障排除

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]


## <a name="summary"></a>总结

将实例部署到云服务或者添加新的 Web 角色或辅助角色实例时，Microsoft Azure 会分配计算资源。 在执行这些操作时，甚至在达到 Azure 订阅限制之前，有时可能会收到错误。 本文说明一些常见分配故障的原因，并建议可能的补救方法。 规划服务的部署时，本信息可能也有用。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>背景 - 分配的工作原理

Azure 数据中心的服务器分区成群集。 会在多个群集中尝试新的云服务分配请求。 将第一个实例部署到云服务时（不管是部署到过渡环境还是生产环境），都会将该云服务固定到某个群集。 云服务的任何进一步部署都会发生在同一个群集。 在本文中，这种情况称为“固定到群集”。 下面的图 1 说明在多个群集中尝试进行一般分配的情况；图 2 说明固定到群集 2（因为现有的云服务 CS_1 托管于此处）的分配情况。

![分配图](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>发生分配故障的原因

当分配请求固定到某个群集时，由于可用的资源池仅限于某个群集，很可能找不到可用的资源。 此外，如果分配请求固定到某个群集，但该群集不支持你所请求的资源类型，那么，即使该群集有可用的资源，请求仍会失败。 下图 3 说明由于唯一候选群集没有可用的资源，导致已固定的分配失败的情况。 图 4 说明由于唯一候选群集不支持所请求的 VM 大小（虽然群集有可用的资源），导致已固定的分配失败的情况。

![固定分配故障](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>云服务分配失败疑难解答

### <a name="error-message"></a>错误消息

在 Azure 门户中，导航到你的云服务，并在侧栏中选择“操作日志(经典)”来查看日志。

请参阅针对以下异常的进一步解决方案：

|异常类型  |错误消息  |解决方案  |
|---------|---------|---------|
|FabricInternalServerError     |操作失败，错误代码为“InternalError”，错误消息为“服务器遇到了内部错误。 请重试该请求。”|[FabricInternalServerError 故障排除](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |操作失败，错误代码为“InternalError”，错误消息为“服务器遇到了内部错误。 请重试该请求。”|[ServiceAllocationFailure 故障排除](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |操作“`{Operation ID}`”失败：“请求的 VM 层当前不可用于此订阅的区域 (`{Region ID}`) 中。 请尝试另一层或部署到其他位置。”|[LocationNotFoundForRoleSize 故障排除](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |Azure 操作“`{Operation ID}`”失败，代码为 Compute.ConstrainedAllocationFailed。 详细信息：分配失败；无法满足请求中的约束。 请求的新服务部署绑定至地缘组，或以虚拟网络为目标，或此托管服务下已经有部署。 上述任一情况都会将新的部署局限于特定的 Azure 资源。 请稍后重试，或尝试减少 VM 大小或角色实例数目。 或者，可能的话，删除先前提到的约束，或尝试部署到不同的区域。|[ConstrainedAllocationFailed 故障排除](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |由于部署请求约束，无法预配此部署所需的 VM 大小（或 VM 大小的组合）。 可能的话，请尝试放宽约束（例如虚拟网络绑定）、部署到不具有其他部署的托管服务及不同的地缘组（或不具有地缘组的托管服务），或尝试部署到不同的区域。|[OverconstrainedAllocationRequest 故障排除](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

示例错误消息：

> “Azure 操作‘{operation id}’失败，代码为 Compute.ConstrainedAllocationFailed。 详细信息：分配失败；无法满足请求中的约束。 请求的新服务部署绑定至地缘组，或以虚拟网络为目标，或此托管服务下已经有部署。 上述任一情况都会将新的部署局限于特定的 Azure 资源。 请稍后重试，或尝试减少 VM 大小或角色实例数目。 或者，可能的话，删除先前提到的约束，或尝试部署到不同的区域。”

### <a name="common-issues"></a>常见问题

以下是造成分配请求被固定到单个群集的常见分配案例。

* 部署到过渡槽 - 如果某个云服务在任一槽中存在部署，则会将整个云服务固定到特定的群集。  这意味着，如果生产槽中已存在部署，则只能将新的过渡部署分配到与生产槽相同的群集中。 如果群集已接近容量，则请求可能失败。
* 缩放 - 将新实例添加到现有云服务时，必须在同一群集中进行分配。  通常可分配小型缩放请求，但情况并非总是如此。 如果群集已接近容量，则请求可能失败。
* 地缘组 - 进行新的目标为空云服务的部署时，可以通过该区域任何群集中的结构对部署进行分配，除非已将云服务固定到地缘组。 将会在相同的群集中尝试部署到相同的地缘组。 如果群集已接近容量，则请求可能失败。
* 地缘组 vNet - 旧式虚拟网络已绑定到地缘组而不是区域，而这些虚拟网络中的云服务则会固定到地缘组群集。 将会在固定的群集中尝试部署到此类虚拟网络。 如果群集已接近容量限制，则请求可能失败。

## <a name="solutions"></a>解决方案

1. 重新部署到新的云服务 - 这种解决方案很可能是最成功的，因为它允许平台从该区域的所有群集中进行选择。

   * 将工作负荷部署到新的云服务  
   * 更新 CNAME 或 A 记录，以将流量指向新的云服务
   * 一旦零流量流向旧站点，就可以删除旧的云服务。 此解决方案应该不会导致停机。
2. 删除生产槽位和过渡槽位 - 此解决方案会保留现有的 DNS 名称，但会导致应用程序停机。

   * 请删除现有云服务的生产槽位和过渡槽位，使云服务为空，然后
   * 在现有云服务中创建新部署。 这会在该区域的所有群集上重新尝试进行分配。 确保云服务未绑定到地缘组。
3. 保留 IP - 此解决方案将保留现有 IP 地址，但会导致应用程序停机。  

   * 请使用 PowerShell 为现有部署创建 ReservedIP

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * 按照上面的第 2 种方法进行操作，确保在服务的 CSCFG 中指定新的 ReservedIP。
4. 删除新部署的地缘组 - 不再建议使用地缘组。 按照上面第 1 种方法的步骤部署新的云服务。 确保云服务不在地缘组中。
5. 转换为区域虚拟网络 - 请参阅[如何从地缘组迁移到区域虚拟网络 (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet)。
