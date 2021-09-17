---
title: 从自动化帐户启用 Azure 自动化更改跟踪和库存
description: 本文介绍了如何从自动化帐户启用更改跟踪和库存。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 4e5e0b563b2701a8b5ddbffbf485a3127d52ece0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769892"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>从自动化帐户启用更改跟踪和清单

本文介绍如何使用自动化帐户为环境中的 VM 启用[更改跟踪和清单](overview.md)。 若要大规模启用 Azure VM，必须使用更改跟踪和库存启用现有 VM。

> [!NOTE]
> 在启用更改跟踪和库存时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../automation-security-overview.md)。
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

1. 导航到你的自动化帐户，在“配置管理”下选择“库存”或“更改跟踪”。  

2. 选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用更改跟踪和库存。 完成设置最多需要 15 分钟时间。

    ![启用更改跟踪和库存](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>启用 Azure VM

1. 在自动化帐户中，选择“配置管理”下的“库存”或“更改跟踪”  。

2. 单击“+ 添加 Azure VM”并从列表中选择一个或多个 VM。 无法启用的虚拟机将灰显，无法选择。 Azure VM 可以位于任何区域中，无论自动化帐户位于哪里。 

3. 单击“启用”以将选定 VM 添加到计算机组为此功能保存的搜索结果。 有关详细信息，请参阅[限制“更改跟踪和清单”的部署范围](manage-scope-configurations.md)。

      [ ![启用 Azure VM](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>启用非 Azure VM

需要手动添加 Azure 中没有的计算机。 建议通过首先将计算机连接到[启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md)来安装适用于 Windows 或 Linux 的 Log Analytics 代理，然后使用 Azure Policy 分配[将 Log Analytics 代理部署到 Linux 或 Windows Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring)内置策略 。 如果还计划使用用于 VM 的 Azure Monitor 来监视计算机，请改用[启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划。

1. 在你的自动化帐户中，在“配置管理”下选择“库存”或“更改跟踪”  。

2. 单击“添加非 Azure 计算机”。 此操作将打开一个新的浏览器窗口，其中包含 [安装和配置适用于 Windows 的 Log Analytics 代理的说明](../../azure-monitor/agents/log-analytics-agent.md)以便计算机可以开始报告更改跟踪和库存操作。 如果你启用当前由 Operations Manager 管理的计算机，则不需要新代理，且工作区信息将输入到现有代理中。

## <a name="enable-machines-in-the-workspace"></a>在工作区中启用计算机

必须将手动安装的计算机或已向工作区报告的计算机添加到 Azure 自动化中，才能启用更改跟踪和库存。

1. 在你的自动化帐户中，在“配置管理”下选择“库存”或“更改跟踪”  。

2. 选择“管理计算机”。 如果之前选择了“在所有可用的和将来的计算机上启用”选项，则“管理计算机”选项可能灰显 

    ![保存的搜索](media/enable-from-automation-account/manage-machines.png)

3. 若要为所有可用的计算机启用更改跟踪和清单，请在“管理计算机”页上选择“在所有可用的计算机上启用” 。 此操作禁止控件单独添加计算机，并会将所有向工作区报告的计算机都添加到计算机组保存的搜索查询。 选中此项后，此操作会禁用“管理计算机”选项。

4. 若要为所有可用的计算机和将来的计算机启用该功能，请选择“在所有可用的和将来的计算机上启用”。 此选项将从工作区中删除已保存的搜索和范围配置，并为所有向工作区报告的 Azure 和非 Azure 计算机都启用该功能。 选中此项后，此操作将永久禁用“管理计算机”选项，因为没有剩余的作用域配置。

    > [!NOTE]
    > 由于此选项会删除 Log Analytics 中保存的搜索和范围配置，因此在选择此选项之前，必须先删除 Log Analytics 工作区中的所有删除锁。 否则，该选项将无法删除配置，必须手动将其删除。

5. 如果需要，可以通过重新添加初始的已保存搜索将范围配置添加回来。 有关详细信息，请参阅[限制“更改跟踪和清单”的部署范围](manage-scope-configurations.md)。

6. 若要为一台或多台计算机启用该功能，请选择“在所选计算机上启用”并单击每台计算机旁边的“添加”以启用该功能。 此任务会将所选计算机名称添加到计算机组为此功能保存的搜索查询。

## <a name="next-steps"></a>后续步骤

* 有关使用此功能的详细信息，请参阅[管理更改跟踪](manage-change-tracking.md)和[管理清单](manage-inventory-vms.md)。

* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](../troubleshoot/change-tracking.md)。
