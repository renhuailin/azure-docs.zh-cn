---
title: 在将云服务 (经典) 部署到 Azure 时对 OverconstrainedAllocationRequest 进行故障排除 |Microsoft Docs
description: 本文介绍如何在将云服务 (经典) 部署到 Azure 时解决 OverconstrainedAllocationRequest 异常。
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744458"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>在将云服务 (经典) 部署到 Azure 时对 OverconstrainedAllocationRequest 进行故障排除

在本文中，你将对阻止部署 Azure 云服务 (经典) 的受限制分配故障进行故障排除。

将实例部署到云服务或者添加新的 Web 角色或辅助角色实例时，Microsoft Azure 会分配计算资源。

甚至在达到 Azure 订阅限制之前，甚至可能会在这些操作期间收到错误。

> [!TIP]
> 规划服务的部署时，本信息可能也有用。

## <a name="symptom"></a>症状

![Image 显示经典) 边栏选项卡 (操作日志。](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|异常类型  |错误消息  |
|---------|---------|
|OverconstrainedAllocationRequest |由于部署请求约束，无法预配此部署所需的 VM 大小（或 VM 大小的组合）。 可能的话，请尝试放宽约束（例如虚拟网络绑定）、部署到不具有其他部署的托管服务及不同的地缘组（或不具有地缘组的托管服务），或尝试部署到不同的区域。|

## <a name="cause"></a>原因

如果云服务已 **固定** 或 **未固定**，根本原因会有所不同。

- [**未固定：** 第一次部署新的云服务失败 (经典)](#not-pinned-to-a-cluster)
- [**固定：** (经典) 的现有云服务的故障](#pinned-to-a-cluster)

> [!NOTE]
> 将第一个实例部署到云服务 (在过渡或生产) 中，该云服务会固定到群集。
>
> 随着时间的推移，群集中的资源可能会完全使用。 如果云服务 (经典) 在固定群集中有足够的资源可用时向其他资源发出分配请求，则请求将导致 [分配失败](cloud-services-allocation-failures.md)。

## <a name="solution"></a>解决方案

在以下情况下，请按照有关分配失败的指导进行操作。

### <a name="not-pinned-to-a-cluster"></a>未固定到群集

首次将云服务部署 (经典) 时，尚未选择该群集，因此云服务未 *固定*。 Azure 可能出现部署失败，原因是：

- 您选择了在区域中不可用的特定大小。
- 区域中不提供不同角色所需的大小组合。

在此情况下遇到分配错误时，建议的操作过程是检查区域中的可用大小，并更改之前指定的大小。

1. 可以在 [云服务 (经典) 产品](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) "页上查看区域中的可用大小。

    > [!NOTE]
    > " *产品* " 页不会显示可用容量。 对于任何新的分配，Azure 应该能够在该时间点选择区域中的最佳群集。

1.  (经典) 中更新云服务的服务定义文件，以指定区域中的不同 [产品大小](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) 。

### <a name="pinned-to-a-cluster"></a>已固定到群集

将现有的云服务 *固定* 到群集。 云服务的任何进一步部署 (经典) 会在同一群集中发生。

在此情况下遇到分配错误时，建议的操作过程是将部署到新的云服务 (经典)  (并更新 *CNAME*) 。

> [!TIP]
> 这种解决方案可能会最成功，因为它允许平台从该区域的所有群集中进行选择。

> [!NOTE]
> 此解决方案应该不会导致停机。

1.  (经典) 将工作负荷部署到新的云服务。
    - 有关更多说明，请参阅 [如何创建和部署云服务 (经典) ](cloud-services-how-to-create-deploy-portal.md) 指南。

    > [!WARNING]
    > 如果你不希望丢失与此部署槽位关联的 IP 地址，则可以使用 [解决方案 3-保留 IP 地址](cloud-services-allocation-failures.md#solutions)。

1. 更新 *CNAME* 或 *A* 记录，将流量指向新的云服务 (经典) 。
    - 有关进一步说明，请参阅为 [Azure 云服务配置自定义域名 (经典) ](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) 指南。

1. 在将零流量发送到旧站点后，你可以 (经典) 中删除旧的云服务。
    - 有关进一步的说明，请参阅 [删除部署和云服务 (经典) ](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) 指南。
    - 若要查看云服务中的网络流量 (经典) ，请参阅 [云服务简介 (经典) 监视](cloud-services-how-to-monitor.md)。

请参阅 [排查云服务 (经典) 分配失败 |Microsoft Docs](cloud-services-allocation-failures.md#common-issues) 获取进一步的补救步骤。

## <a name="next-steps"></a>后续步骤

有关更多分配失败解决方案和背景信息：

> [!div class="nextstepaction"]
> [分配失败-云服务 (经典) ](cloud-services-allocation-failures.md)

如果本文未解决你的 Azure 问题，请访问 MSDN 上的 Azure 论坛 [并 Stack Overflow](https://azure.microsoft.com/support/forums/)。 可将问题发布到这些论坛上，或发布到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 还可提交 Azure 支持请求。 若要提交支持请求，请在 [Azure 支持](https://azure.microsoft.com/support/options/)页上，选择“获取支持”。
