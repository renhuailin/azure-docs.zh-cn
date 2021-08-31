---
title: 如何跨区域迁移已启用 Azure Arc 的服务器
description: 了解如何将已启用 Azure Arc 的服务器从一个区域迁移到另一个区域。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 9635bcf4f2f557c1f4b32a15c9cd936e5b5f02fe
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114387107"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>如何跨区域迁移已启用 Azure Arc 的服务器

在某些情况下，你希望将现有的已启用 Azure Arc 的服务器从一个区域移到另一个区域。 例如，你发现计算机注册到了错误的区域、想要提高可管理性，或者出于监管原因而要移动计算机。

若要将已启用 Azure Arc 的服务器从一个 Azure 区域迁移到另一个区域，必须卸载 VM 扩展，删除 Azure 中的资源，然后在另一个区域中重新创建该资源。 在执行这些步骤之前，应该审核计算机以确认安装了哪些 VM 扩展。

> [!NOTE]
> 尽管在完成此过程后，已安装的扩展将继续运行并执行其常规操作，但你无法管理这些扩展。 如果你尝试在计算机上重新部署扩展，可能会遇到不可预知的行为。

## <a name="move-machine-to-other-region"></a>将计算机移到另一区域

> [!NOTE]
> 在此操作中，会导致迁移期间出现停机。

1. 使用 [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension) 或 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension) 删除通过 [Azure 门户](manage-vm-extensions-portal.md#uninstall-extensions)安装的 VM 扩展。

2. 结合使用 azcmagent 工具与 [Disconnect](manage-agent.md#disconnect) 参数从 Azure Arc 中断开计算机的连接，然后从 Azure 中删除计算机资源。 从已启用 Arc 的服务器断开计算机的连接不会删除 Connected Machine 代理，并且在此过程中不需要删除该代理。 可以在以交互方式登录时手动运行此内容，或者使用用于加入多个代理的同一服务主体或使用 Microsoft 标识平台[访问令牌](../../active-directory/develop/access-tokens.md)来自动运行此内容。 如果你未使用服务主体将计算机注册到已启用 Azure Arc 的服务器，请参阅以下[文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)创建服务主体。

3. 将 Connected Machine Agent 重新注册到另一区域中的已启用 Arc 的服务器。 使用 [Connect](manage-agent.md#connect) 参数运行 `azcmagent` 工具即可完成此步骤。

4. 重新部署最初部署到了已启用 Arc 的服务器中的计算机的 VM 扩展。 如果你使用 Azure 策略部署了用于 VM 的 Azure Monitor（见解）代理或 Log Analytics 代理，将在下一个[评估周期](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)后重新部署这些代理。

## <a name="next-steps"></a>后续步骤

* 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否向预期的 Log Analytics 工作区报告、使用 [VM 见解](../../azure-monitor/vm/vminsights-enable-policy.md)策略启用监视，等等。