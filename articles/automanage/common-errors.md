---
title: 排查常见的 Azure Automanage 加入错误
description: 常见的 Automanage 加入错误以及这些错误的故障排除方法
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 95d2f429c1ecb308d65aa01be0a6f3fa226b6168
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458162"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>排查常见的 Automanage 加入错误
Automanage 可能无法将计算机加入服务中。 本文档介绍如何排查部署失败的问题，分享部署失败的一些常见原因，并描述可能的后续缓解步骤。

## <a name="troubleshooting-deployment-failures"></a>排查部署失败的问题
将计算机加入 Automanage 将导致创建 Azure 资源管理器部署。 有关详细信息，请查看部署，以更详细地了解失败原因。 “失败详细信息”浮出控件中有指向部署的链接，如下图所示。

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Automanage 失败详细信息的浮出控件。":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-machine"></a>检查包含失败计算机的资源组的部署
失败浮出控件将包含一个链接，该链接指向包含无法加入的计算机的资源组中的部署。 该浮出控件还包含一个可用于筛选部署的前缀名称。 单击部署链接将转到“部署”边栏选项卡，你可在其中筛选部署以查看计算机的 Automanage 部署。 如果要跨多个区域进行部署，请确保单击正确区域中的部署。

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-machine"></a>检查包含失败计算机的订阅的部署
如果在资源组部署中未看到任何失败，则下一步是查看包含加入失败计算机的订阅中的部署。 单击失败浮出控件中的“订阅部署”链接，然后使用 Automanage-DefaultResourceGroup 筛选器筛选部署 。 使用“失败”边栏选项卡中的资源组名称筛选部署。 部署名称的后缀将为区域名称。 如果要跨多个区域进行部署，请确保单击正确区域中的部署。

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>检查链接到 Log Analytics 工作区的订阅中的部署
如果包含失败计算机的资源组或订阅中未出现任何失败的部署，但失败计算机已连接到其他订阅中的 Log Analytics 工作区，请访问链接到 Log Analytics 工作区的订阅并检查是否存在失败的部署。

## <a name="common-deployment-errors"></a>常见部署错误

错误 |  缓解措施
:-----|:-------------|
Automanage 帐户权限不足错误 | 如果你最近将包含新 Automanage 帐户的订阅移动到新租户中，则可能会出现此错误。 [此处](./repair-automanage-account.md)提供了解决此错误的步骤。
工作区区域不符合区域映射要求 | Automanage 无法加入你的计算机，原因是该计算机当前链接到的 Log Analytics 工作区未映射到受支持的自动化区域。 请确保现有 Log Analytics 工作区和自动化帐户位于[受支持的区域映射](../automation/how-to/region-mappings.md)。
“因为拒绝分配的名称为‘系统拒绝由托管应用程序创建的分配’，访问被拒绝” | 在资源上创建了 [denyAssignment](../role-based-access-control/deny-assignments.md)，它阻止了 Automanage 访问你的资源。 此 denyAssignment 可能是由[蓝图](../governance/blueprints/concepts/resource-locking.md)或[托管应用程序](../azure-resource-manager/managed-applications/overview.md)创建的。
“OS 信息: 名称='(null)'，版本='(null)'，代理状态=‘未准备就绪’。” | 确保运行[最低受支持的代理版本](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)、代理正在运行（[linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) 和 [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)），并且代理是最新的（[Linux](../virtual-machines/extensions/update-linux-agent.md) 和 [Windows](../virtual-machines/extensions/agent-windows.md)）。
“无法确定 VM 的 OS。OS 名称: ...，版本: ...。 请检查 VM 代理是否正在运行，当前状态是否为‘准备就绪’。” | 确保运行[最低受支持的代理版本](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)、代理正在运行（[linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) 和 [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)），并且代理是最新的（[Linux](../virtual-machines/extensions/update-linux-agent.md) 和 [Windows](../virtual-machines/extensions/agent-windows.md)）。
“VM 在处理扩展 'IaaSAntimalware' 时报告失败” | 确保尚未在 VM 上安装其他反恶意软件/防病毒产品/服务。 如果仍然失败，请联系支持人员。
ASC 工作区: Automanage 目前不支持 <位置> 中的 Log Analytics 服务。 | 检查你的 VM 是否位于[受支持的区域](./automanage-virtual-machines.md#supported-regions)。
由于策略冲突而导致模板部署失败。 请查看详细信息了解更多信息。 | 某个策略阻止 Automanage 加入你的 VM。 请检查应用于包含你要加入 Automanage 的 VM 的订阅或资源组的策略。
“分配失败；没有其他可用信息” | 请向 Microsoft Azure 支持人员提交支持案例。

## <a name="next-steps"></a>后续步骤

* [详细了解 Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [在 Azure 门户中为计算机启用 Automanage](quick-create-virtual-machines-portal.md)