---
title: 在将云服务 (经典) 部署到 Azure 时对 ConstrainedAllocationFailed 进行故障排除 |Microsoft Docs
description: 本文介绍如何在将云服务 (经典) 部署到 Azure 时解决 ConstrainedAllocationFailed 异常。
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738271"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>在将云服务 (经典) 部署到 Azure 时对 ConstrainedAllocationFailed 进行故障排除

在本文中，你将对 Azure 云服务 (经典) 由于分配约束而无法部署的分配失败进行故障排除。

将实例部署到云服务 (经典) 或添加新的 web 角色或辅助角色实例时，Microsoft Azure 会分配计算资源。

甚至在达到 Azure 订阅限制之前，甚至可能会在这些操作期间收到错误。

> [!TIP]
> 规划服务的部署时，本信息可能也有用。

## <a name="symptom"></a>症状

在 Azure 门户中，导航到云服务 (经典) ，并在侧栏中选择 " *操作日志 (经典)* " 查看日志。

![Image 显示经典) 边栏选项卡 (操作日志。](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

检查云服务的日志 (经典) 时，你将看到以下异常：

|异常类型  |错误消息  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 操作 " `{Operation ID}` " 失败，代码为 ConstrainedAllocationFailed。 详细信息：分配失败；无法满足请求中的约束。 请求的新服务部署绑定至地缘组，或以虚拟网络为目标，或此托管服务下已经有部署。 上述任一情况都会将新的部署局限于特定的 Azure 资源。 请稍后重试，或尝试减少 VM 大小或角色实例的数量。 或者，可能的话，删除先前提到的约束，或尝试部署到不同的区域。|

## <a name="cause"></a>原因

将第一个实例部署到云服务 (在过渡或生产) 中，该云服务会固定到群集。

随着时间的推移，此群集中的资源可能会完全使用。 如果云服务 (经典) 在固定的群集中有足够的资源可用时，向分配请求更多资源，则该请求将导致分配失败。 有关详细信息，请参阅 [分配失败的常见问题](cloud-services-allocation-failures.md#common-issues)。

## <a name="solution"></a>解决方案

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