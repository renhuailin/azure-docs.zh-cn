---
title: 排查常见的 Azure Automanage 载入错误
description: 常见的 Automanage 载入错误以及如何解决这些错误
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 13388bf289c1d10a2e7da04a4dd5d26be109535e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697789"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>排查常见的 Automanage 载入错误
Automanage 可能无法将计算机登记到服务中。 本文档介绍了如何排查部署故障，如何分担部署可能会失败的一些常见原因，并介绍了在缓解时可能会发生的后续步骤。

## <a name="troubleshooting-deployment-failures"></a>部署故障排除
将计算机加入到 Automanage 将导致创建 Azure 资源管理器部署。 如果载入失败，请咨询部署以获取更多详细信息，以了解失败的原因。 "故障详细信息" 弹出窗口中提供了部署的链接，如下图所示。

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Automanage 失败详细信息弹出。":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>检查包含失败 VM 的资源组的部署
"失败" 弹出窗口将包含一个链接，该链接指向包含失败的计算机的资源组中的部署和可用于筛选部署的前缀名称。 单击此链接将转到 "部署" 边栏选项卡，你可以在其中筛选部署以查看计算机的 Automanage 部署。 如果要跨多个区域进行部署，请确保在正确的区域中单击部署。

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>检查包含失败 VM 的订阅的部署
如果在资源组部署中看不到任何失败，则下一步是查看订阅中的部署，其中包含未通过载入的 VM。 单击 "失败" 弹出窗口中的 " **订阅部署** " 链接，并使用 **Automanage-DefaultResourceGroup** 筛选器筛选部署。 使用 "失败" 边栏选项卡中的资源组名称筛选部署。 部署名称的后缀为区域名称。 如果要跨多个区域进行部署，请确保在正确的区域中单击部署。

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>检查链接到 Log Analytics 工作区的订阅中的部署
如果资源组或订阅中未出现包含失败的 VM 的任何失败的部署，并且失败的 VM 连接到不同订阅中的 Log Analytics 工作区，则请访问链接到 Log Analytics 工作区的订阅并检查失败的部署。

## <a name="common-deployment-errors"></a>常见部署错误

错误 |  缓解操作
:-----|:-------------|
Automanage 帐户权限不足错误 | 如果最近将包含新 Automanage 帐户的订阅移动到新租户中，则可能会发生这种情况。 [此处](./repair-automanage-account.md)提供了解决此问题的步骤。
工作区区域与区域映射要求不匹配 | Automanage 无法载入你的计算机，但该计算机当前链接到的 Log Analytics 工作区未映射到受支持的自动化区域。 请确保现有 Log Analytics 工作区和自动化帐户位于 [受支持的区域映射](https://docs.microsoft.com/azure/automation/how-to/region-mappings)。
"分配失败;没有其他可用信息 " | 请打开 Microsoft Azure 支持案例。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure Automanage 的详细信息](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)

