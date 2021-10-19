---
title: 从自动化帐户启用 Azure 自动化更新管理
description: 本文介绍如何从自动化帐户启用更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 36d78edebdcc1624270cf27f136f701b2ebc0191
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715357"
---
# <a name="enable-update-management-from-an-automation-account"></a>从自动化帐户启用更新管理

本文介绍如何使用自动化帐户为环境中的 VM（包括已注册到[已启用 Azure Arc 的服务器](../../azure-arc/servers/overview.md)的计算机或服务器）启用[更新管理](overview.md)功能。 要大规模启用 Azure VM，必须使用更新管理启用现有 Azure VM。

> [!NOTE]
> 在启用更新管理时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../automation-security-overview.md)。
* [Azure 虚拟机](../../virtual-machines/windows/quick-create-portal.md)，或已注册到已启用 Azure Arc 的服务器的 VM 或服务器。 非 Azure VM 或服务器需要安装用于 Windows 或 Linux 的 [Log Analytics 代理](../../azure-monitor/agents/log-analytics-agent.md)，并向链接到自动化帐户的工作区（其中启用了更新管理）报告。 建议通过先将计算机连接到[已启用 Azure Arc 的服务器](../../azure-arc/servers/overview.md)来安装适用于 Windows 或 Linux 的日志分析代理，然后使用 Azure Policy 来分配[将日志分析代理部署到 Linux 或 Windows Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring)内置策略。  或者，如果计划使用用于 VM 的 Azure Monitor 来监视计算机，请改用[启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-update-management"></a>启用更新管理

1. 在自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择 Log Analytics 工作区和自动化帐户，然后选择“启用”以启用更新管理。 安装最多需要 15 分钟才能完成。

    ![启用更新管理](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>启用 Azure VM

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择“+添加 Azure VM”，再从列表中选择一个或多个 VM。 无法启用的虚拟机将灰显，无法选择。 Azure VM 可以位于任何区域中，无论自动化帐户位于哪里。

3. 选择“启用”，将所选 VM 添加到计算机组为此功能保存的搜索结果中。

    ![启用 Azure VM](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>启用非 Azure VM

对于在 Azure 外部托管的计算机或服务器（包括已注册到已启用 Azure Arc 的服务器的计算机或服务器），请执行以下步骤以使用更新管理来启用。  

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择“添加非 Azure 计算机”。 此操作将打开一个新的浏览器窗口，其中包含[有关安装和配置适用于 Windows 的 Log Analytics 代理的说明](../../azure-monitor/agents/log-analytics-agent.md)，使计算机可以开始向更新管理报告。 如果你启用当前由 Operations Manager 管理的计算机，则不需要新代理。 工作区信息将添加到代理配置中。

## <a name="enable-machines-in-the-workspace"></a>在工作区中启用计算机

必须将手动安装的计算机或已向工作区报告的计算机添加到 Azure 自动化中，才能启用更新管理。

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择“管理计算机”。 如果之前选择了“在所有可用的和将来的计算机上启用”选项，则“管理计算机”按钮可能灰显 

    ![保存的搜索](media/enable-from-automation-account/managemachines.png)

3. 要为向工作区报告的所有可用计算机启用更新管理，请在“管理计算机”页上选择“在所有可用计算机上启用”。 此操作禁止控件单独添加计算机，并将向工作区报告的所有计算机添加到计算机组保存的搜索查询 `MicrosoftDefaultComputerGroup` 中。 选中此项后，此操作会禁用“管理计算机”选项。

4. 若要为所有可用的计算机和将来的计算机启用该功能，请选择“在所有可用的和将来的计算机上启用”。 此选项从工作区中删除已保存的搜索和作用域配置，并允许该功能包括当前或将来向工作区报告的所有 Azure 和非 Azure 计算机。 选中此项后，此操作会永久禁用“管理计算机”选项，因为没有可用的范围配置。

    > [!NOTE]
    > 由于此选项会删除 Log Analytics 中保存的搜索和范围配置，因此在选择此选项之前，必须先删除 Log Analytics 工作区中的所有删除锁。 否则，该选项将无法删除配置，必须手动将其删除。

5. 如果需要，可以通过重新添加初始的已保存搜索查询将范围配置添加回来。 有关详细信息，请参阅[限制更新管理的部署范围](scope-configuration.md)。

6. 若要为一台或多台计算机启用该功能，请选择“在所选计算机上启用”，并选择每台计算机旁边的“添加” 。 此任务会将所选计算机名称添加到计算机组为此功能保存的搜索查询。

## <a name="next-steps"></a>后续步骤

* 若要对 VM 使用更新管理，请参阅[管理 VM 的更新和修补程序](manage-updates-for-vm.md)。

* 当不再需要通过更新管理来管理 VM 或服务器时，请参阅[从更新管理中删除 VM](remove-vms.md)。

* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](../troubleshoot/update-management.md)。
