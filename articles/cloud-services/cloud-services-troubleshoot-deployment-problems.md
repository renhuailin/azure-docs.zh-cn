---
title: 解决云服务（经典版）部署问题 | Microsoft Docs
description: 将云服务部署到 Azure 时，可能会遇到几个常见问题。 本文提供了部分问题的解决方案。
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9b88ed48468d3169470b0a1e37e1f8d8c0c9b2db
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090090"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>解决 Azure 云服务（经典版）部署问题

> [!IMPORTANT]
> [Azure 云服务（外延支持）](../cloud-services-extended-support/overview.md)是 Azure 云服务产品基于 Azure 资源管理器的新型部署模型。 进行此更改后，在基于 Azure 服务管理器的部署模型上运行的 Azure 云服务已重命名为云服务（经典），所有新部署都应使用[云服务（外延支持）](../cloud-services-extended-support/overview.md)。

将云服务应用程序包部署到 Azure 时，可通过 Azure 门户中的“属性”  窗格获取有关部署的信息。 可以使用此窗格中的详细信息来帮助你解决云服务的问题，还可以在提交新的支持请求时将此信息提供给 Azure 支持人员。

可按如下所述找到“属性”  窗格：

* 在 Azure 门户中，依次单击云服务的部署、“所有设置”  “属性”  。

> [!NOTE]
> 可以通过单击“属性”  窗格右上角的图标将该窗格的内容复制到剪贴板。
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>问题：我无法访问我的网站，但我的部署已启动且所有角色实例均已就绪
在门户中显示的网站 URL 链接不包括该端口。 网站的默认端口为 80。 如果应用程序已配置为在其他端口中运行，则必须在访问网站时向 URL 添加正确的端口号。

1. 在 Azure 门户中，单击云服务的部署。
2. 在 Azure 门户的“属性”  窗格中，检查角色实例（位于“输入终结点”  下）的端口。
3. 如果端口不是 80，请在访问应用程序时会正确的端口值添加到 URL。 若要指定非默认端口，请键入 URL，后跟冒号 (:) 以及端口号（无空格）。

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>问题：我的角色实例在我未执行任何操作的情况下被回收
当 Azure 检测到问题节点并因此将角色实例移到新节点时，系统会自动进行服务修复。 当发生这种情况时，可能会看到角色实例自动回收。 若要查看是否进行了服务修复，请执行以下操作：

1. 在 Azure 门户中，单击云服务的部署。
2. 在 Azure 门户的“属性”  窗格中查看相关信息，确定在观察角色回收期间是否进行了服务修复。

在主机 OS 和来宾 OS 更新期间，角色也会差不多每月回收一次。  
有关详细信息，请参阅博客文章 [Role Instance Restarts Due to OS Upgrades](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)（因 OS 升级导致的角色实例重启）

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>问题：无法进行 VIP 交换并收到错误
如果部署更新正在进行，则不能进行 VIP 交换。 出现以下情况时，部署更新可能会自动进行：

* 新的来宾操作系统已发布，而已配置为自动进行更新。
* 进行了服务修复。

要了解是否是自动更新阻止你执行 VIP 交换，请执行以下操作：

1. 在 Azure 门户中，单击云服务的部署。
2. 在 Azure 门户的“属性”  窗格中，查看“状态”  的值。 如果状态为“就绪”  ，则请检查“上次操作”  ，查看最近是否进行了更新，因为更新可能会阻止执行 VIP 交换。
3. 重复进行生产部署所需的步骤 1 和步骤 2。
4. 如果自动更新正在进行，则请等待其完成，再尝试进行 VIP 交换。

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>问题：角色实例在“已启动”、“正在初始化”、“忙碌”和“已停止”这几种状态之间循环往复
这种情况可能表示应用程序代码、包或配置文件存在问题。 在这种情况下，应能看到状态每隔几分钟更改一次，而 Azure 门户则可能会显示“正在回收”  、“忙”  或“正在初始化”  之类的内容。 这表示应用程序存在问题，导致角色实例无法运行。

有关如何解决此问题的详细信息，请参阅博客文章 [Azure PaaS Compute Diagnostics Data](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)（Azure PaaS 计算诊断数据）和[导致角色回收的常见问题](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)。

## <a name="problem-my-application-stopped-working"></a>问题：我的应用程序停止工作
1. 在 Azure 门户中，单击角色实例。
2. 在 Azure 门户的“属性”  窗格中，考虑是否存在以下情况，以便解决问题：
   * 如果角色实例最近停止过（可查看“中止计数”  的值），则可能是因为部署正在进行更新。 等待，看角色实例是否会自行恢复运行。
   * 如果角色实例处于“忙”  状态，请检查应用程序代码，以查看是否已处理了 [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) 事件。 可能需要添加或修复处理此事件的某些代码。
   * 浏览博客文章 [Azure PaaS Compute Diagnostics Data](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)（Azure PaaS 计算诊断数据）中的诊断数据和故障排除方案。

> [!WARNING]
> 如果回收云服务，请重置部署的属性，以便有效清除有关原始问题的信息。
>
>

## <a name="next-steps"></a>后续步骤
查看更多针对云服务的 [故障排除文章](./cloud-services-allocation-failures.md) 。

若要了解如何使用 Azure PaaS 计算机诊断数据对云服务角色问题进行故障排除，请参阅 [Kevin Williamson 博客系列](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)。