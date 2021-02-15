---
title: 如何跨区域迁移启用了 Azure Arc 的服务器
description: 了解如何将启用了 Azure Arc 的服务器从一个区域迁移到另一个区域。
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: d4e0f1e41e928ab489f7c2c167eea31785d9bc21
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417123"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>如何跨区域迁移启用了 Azure Arc 的服务器

在某些情况下，你想要将现有的 Azure Arc 启用的服务器从一个区域移到另一个区域。 例如，你已在错误的区域中注册了计算机，从而提高了可管理性，或出于管理目的而移动了计算机。

若要将启用了 Azure Arc 的服务器从一个 Azure 区域迁移到另一个 Azure 区域，你必须卸载 VM 扩展，删除 Azure 中的资源，然后在另一个区域中重新创建它。 在执行这些步骤之前，你应该审核计算机以验证安装了哪些 VM 扩展。

> [!NOTE]
> 当完成此过程后，安装的扩展将继续运行并执行正常操作，你将无法对其进行管理。 如果尝试在计算机上重新部署扩展，则可能会遇到不可预知的行为。

## <a name="move-machine-to-other-region"></a>将计算机移动到其他区域

> [!NOTE]
> 在此操作过程中，它会在迁移期间造成停机。

1. 使用[Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)或[Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)删除从[Azure 门户](manage-vm-extensions-portal.md#uninstall-extension)安装的 VM 扩展。

2. 使用带有 [disconnect](manage-agent.md#disconnect)参数的 **Azcmagent** 工具从 azure Arc 断开计算机的连接，并从 azure 中删除计算机资源。 断开计算机与启用了 Arc 的服务器的连接不会删除已连接的计算机代理，因此不需要在此过程中删除代理。 可以通过交互方式登录，也可以使用用于集成多个代理的相同服务主体或使用 Microsoft 标识平台 [访问令牌](../../active-directory/develop/access-tokens.md)自动运行。 如果未使用服务主体向启用了 Azure Arc 的服务器注册计算机，请参阅以下 [文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 创建服务主体。

3. 在另一个区域中，将已连接的计算机代理重新注册为启用了 Arc 的服务器。 运行 `azcmagent` 带有 [Connect](manage-agent.md#connect) 参数的工具完成此步骤。

4. 重新部署最初部署到启用了 Arc 的服务器上的虚拟机的 VM 扩展。 如果使用 Azure 策略将用于 VM 的 Azure Monitor (insights) 代理或 Log Analytics 代理部署，则在下一个 [评估周期](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)后重新部署代理。

## <a name="next-steps"></a>后续步骤

* 有关疑难解答信息，请参阅 [连接计算机代理疑难解答指南](troubleshoot-agent-onboard.md)。

* 了解如何使用 [Azure 策略](../../governance/policy/overview.md)管理计算机，例如 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区进行报告，使用 [vm 策略 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) 启用监视，等等。
