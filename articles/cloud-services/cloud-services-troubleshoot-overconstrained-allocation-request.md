---
title: 将云服务（经典）部署到 Azure 时对 OverconstrainedAllocationRequest 进行故障排除 | Microsoft Docs
description: 本文介绍了如何在将云服务（经典）部署到 Azure 时解决 OverconstrainedAllocationRequest 异常。
services: cloud-services
documentationcenter: ''
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54982085d259ccd678ba66a3f87c9d0bc051528d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090054"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>将云服务（经典）部署到 Azure 时对 OverconstrainedAllocationRequest 进行故障排除

在本文中，你将对阻止部署 Azure 云服务（经典）的受限制分配故障进行故障排除。

将实例部署到云服务或者添加新的 Web 角色或辅助角色实例时，Microsoft Azure 会分配计算资源。

在执行这些操作时，甚至在达到 Azure 订阅限制之前，有时可能会收到错误。

> [!TIP]
> 规划服务的部署时，本信息可能也有用。

## <a name="symptom"></a>症状

![图像显示“操作日志(经典)”边栏选项卡。](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|异常类型  |错误消息  |
|---------|---------|
|OverconstrainedAllocationRequest |由于部署请求约束，无法预配此部署所需的 VM 大小（或 VM 大小的组合）。 可能的话，请尝试放宽约束（例如虚拟网络绑定）、部署到不具有其他部署的托管服务及不同的地缘组（或不具有地缘组的托管服务），或尝试部署到不同的区域。|

## <a name="cause"></a>原因

如果云服务为“已固定”或“未固定”，则根本原因会有所不同。

- [未固定：第一次部署新的云服务（经典）失败](#not-pinned-to-a-cluster)
- [已固定：现有云服务（经典）失败](#pinned-to-a-cluster)

> [!NOTE]
> 将第一个实例部署到云服务时（不管是部署到过渡环境还是生产环境），都会将该云服务固定到某个群集。
>
> 随着时间的推移，群集中的资源可能会被完全利用。 当固定的群集中没有足够的资源可用时，如果云服务（经典）发出分配请求来请求更多资源，则该请求将导致[分配失败](cloud-services-allocation-failures.md)。

## <a name="solution"></a>解决方案

在以下情况下，请按照有关分配失败的指导进行操作。

### <a name="not-pinned-to-a-cluster"></a>未固定到群集

首次部署云服务（经典）时，尚未选择该群集，因此云服务未固定。 Azure 可能出现部署失败，原因是：

- 你选择了区域中不可用的特定大小。
- 区域中未提供不同角色所需的大小组合。

在此情况下遇到分配错误时，建议的操作过程是检查区域中的可用大小，并更改之前指定的大小。

1. 可以在[云服务（经典）产品](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)页上查看区域中的可用大小。

    > [!NOTE]
    > “产品”页将不会显示可用容量。 对于任何新的分配，Azure 应能够在该时间点选取区域中的最佳群集。

1. 更新云服务（经典）的服务定义文件，以指定区域中的不同[产品大小](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services)。

### <a name="pinned-to-a-cluster"></a>已固定到群集

现有的云服务已固定到群集。 云服务（经典）的任何进一步部署都将在同一个群集中进行。

在此情况下遇到分配错误时，建议的操作过程是重新部署到新的云服务（经典）（并更新 CNAME）。

> [!TIP]
> 这种解决方案可能会最成功，因为它允许平台从该区域的所有群集中进行选择。

> [!NOTE]
> 此解决方案应该不会导致停机。

1. 将工作负荷部署到新的云服务（经典）。
    - 有关更多说明，请参阅[如何创建和部署云服务（经典）](cloud-services-how-to-create-deploy-portal.md)指南。

    > [!WARNING]
    > 如果你不希望丢失与此部署槽位关联的 IP 地址，则可以使用[解决方案 3 - 保留 IP 地址](cloud-services-allocation-failures.md#solutions)。

1. 更新 CNAME 或 A 记录，以将流量指向新的云服务（经典）。
    - 有关进一步说明，请参阅[为 Azure 云服务（经典）配置自定义域名](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records)指南。

1. 没有流量流向旧站点后，即可删除旧的云服务（经典）。
    - 有关进一步的说明，请参阅[删除部署和云服务（经典）](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service)指南。
    - 若要查看云服务（经典）中的网络流量，请参阅[云服务（经典）监视简介](cloud-services-how-to-monitor.md)。

有关进一步的修正步骤，请参阅[云服务（经典）分配失败故障排除 | Microsoft Docs](cloud-services-allocation-failures.md#common-issues)。

## <a name="next-steps"></a>后续步骤

有关更多分配失败解决方案和背景信息：

> [!div class="nextstepaction"]
> [分配失败 - 云服务（经典）](cloud-services-allocation-failures.md)

如果你有本文未解决的 Azure 问题，请访问 [MSDN 和 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 论坛。 可将问题发布到这些论坛上，或发布到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 还可提交 Azure 支持请求。 若要提交支持请求，请在 [Azure 支持](https://azure.microsoft.com/support/options/)页上，选择“获取支持”。
